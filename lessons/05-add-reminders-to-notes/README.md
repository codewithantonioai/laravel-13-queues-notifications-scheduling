# Lesson 5 · Add reminders to notes

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=554) (starts at 9:14)

## Goal

Lesson five. Notes need to know when to remind you. That means a new column, a validation rule, and a field in the form.

## Commands and files (as shown in the video)

### Terminal · make migration

```bash
php artisan make:migration add_remind_at_to_notes_table
```

### `database/migrations/2026_09_23_234321_add_remind_at_to_notes_table.php` · migration

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::table('notes', function (Blueprint $table) {
            $table->timestamp('remind_at')->nullable();
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::table('notes', function (Blueprint $table) {
            $table->dropColumn('remind_at');
        });
    }
};
```

### Terminal · migrate

```bash
php artisan migrate
```

### `app/Models/Note.php` · model cast

```php
<?php

namespace App\Models;

use Database\Factories\NoteFactory;
use Illuminate\Database\Eloquent\Attributes\Fillable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
use Illuminate\Support\Facades\Storage;

#[Fillable(['title', 'content', 'image_path', 'remind_at'])]
class Note extends Model
{
    /** @use HasFactory<NoteFactory> */
    use HasFactory;

    protected static function booted(): void
    {
        static::updated(function (Note $note) {
            if ($note->wasChanged('image_path') && $note->getOriginal('image_path')) {
                Storage::disk('public')->delete($note->getOriginal('image_path'));
            }
        });

        static::deleted(function (Note $note) {
            if ($note->image_path) {
                Storage::disk('public')->delete($note->image_path);
            }
        });
    }

    protected function casts(): array
    {
        return [
            'remind_at' => 'datetime',
        ];
    }

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class);
    }
}
```

### `app/Http/Requests/StoreNoteRequest.php` · rule

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreNoteRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'title' => ['required', 'min:3', 'max:255'],
            'content' => ['nullable', 'string'],
            'image' => ['nullable', 'image', 'max:2048'],
            'remind_at' => ['nullable', 'date', 'after:now'],
            'tags' => ['array'],
            'tags.*' => ['exists:tags,id'],
        ];
    }
}
```

### `resources/views/notes/create.blade.php` · field

```blade
<x-layout>
    <h1 class="text-4xl font-bold">New note</h1>

    <form method="POST" action="/notes" enctype="multipart/form-data"
        class="mt-8 space-y-4">
        @csrf
        <input name="title" placeholder="Title" value="{{ old('title') }}" class="field">
        @error('title')
            <p class="text-red-400">{{ $message }}</p>
        @enderror
        <textarea name="content" placeholder="Content" class="field">{{ old('content') }}</textarea>
        <input type="file" name="image" accept="image/*" class="field">
        @error('image')
            <p class="text-red-400">{{ $message }}</p>
        @enderror
        <label class="block text-slate-400">
            Remind me at
            <input type="datetime-local" name="remind_at"
                value="{{ old('remind_at') }}" class="field mt-1">
        </label>
        @error('remind_at')
            <p class="text-red-400">{{ $message }}</p>
        @enderror
        <div class="flex flex-wrap gap-3">
            @foreach ($tags as $tag)
                <label class="tag-option">
                    <input type="checkbox" name="tags[]" value="{{ $tag->id }}"
                        @checked(in_array($tag->id, old('tags', [])))>
                    {{ $tag->name }}
                </label>
            @endforeach
        </div>
        <button class="button">Save note</button>
    </form>
</x-layout>
```

## Recap

So: a nullable timestamp holds the reminder. After now keeps it in the future. And a date time local field lets the user pick it. On to the scheduler.
