# Lesson 6 · The scheduler

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=676) (starts at 11:16)

## Goal

Lesson six. A command that finds the reminders that are due, and the scheduler to run it every minute.

## Commands and files (as shown in the video)

### Terminal · make command

```bash
php artisan make:command SendNoteReminders
```

### `app/Console/Commands/SendNoteReminders.php` · send reminders

```php
<?php

namespace App\Console\Commands;

use App\Models\Note;
use App\Notifications\NoteReminder;
use Illuminate\Console\Attributes\Description;
use Illuminate\Console\Attributes\Signature;
use Illuminate\Console\Command;

#[Signature('notes:send-reminders')]
#[Description('Send the reminders that are due')]
class SendNoteReminders extends Command
{
    public function handle(): void
    {
        $notes = Note::with('user')
            ->where('remind_at', '<=', now())
            ->get();

        foreach ($notes as $note) {
            $note->user->notify(new NoteReminder($note));

            $note->update(['remind_at' => null]);
        }

        $this->info("Reminders sent: {$notes->count()}");
    }
}
```

### Terminal · run once

```bash
php artisan notes:send-reminders
```

### `routes/console.php` · schedule every minute

```php
<?php

use Illuminate\Foundation\Inspiring;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\Schedule;

Artisan::command('inspire', function () {
    $this->comment(Inspiring::quote());
})->purpose('Display an inspiring quote');

Schedule::command('notes:send-reminders')->everyMinute();
```

### Terminal · schedule list

```bash
php artisan schedule:list
```

### Terminal · schedule work

```bash
# 'Call the dentist' is due now
php artisan schedule:work
```

### Terminal · worker second terminal

```bash
php artisan queue:work
```

## Recap

So: an Artisan command does the work. One line in routes console dot php schedules it. And schedule work runs it while you develop. Next, a summary every morning, and how all this runs in production.
