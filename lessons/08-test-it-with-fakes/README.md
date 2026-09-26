# Lesson 8 · Test it with fakes

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=945) (starts at 15:45)

## Goal

Lesson eight. We test the reminders without sending a single email, and without waiting an hour.

## Commands and files (as shown in the video)

### Terminal · make test

```bash
php artisan make:test NoteReminderTest
```

### `tests/Feature/NoteReminderTest.php` · test due

```php
<?php

use App\Models\Note;
use App\Models\User;
use App\Notifications\NoteReminder;
use Illuminate\Support\Facades\Notification;

beforeEach(function () {
    Notification::fake();

    $this->user = User::factory()->create();
    $this->note = Note::factory()->for($this->user)->create([
        'remind_at' => now()->addHour(),
    ]);
});

test('reminders are sent when they are due', function () {
    $this->artisan('notes:send-reminders')->assertSuccessful();
    Notification::assertNothingSent();

    $this->travel(61)->minutes();

    $this->artisan('notes:send-reminders')->assertSuccessful();
    Notification::assertSentTo($this->user, NoteReminder::class);
});
```

### `tests/Feature/NoteReminderTest.php` · test once

```php
<?php

use App\Models\Note;
use App\Models\User;
use App\Notifications\NoteReminder;
use Illuminate\Support\Facades\Notification;

beforeEach(function () {
    Notification::fake();

    $this->user = User::factory()->create();
    $this->note = Note::factory()->for($this->user)->create([
        'remind_at' => now()->addHour(),
    ]);
});

test('reminders are sent when they are due', function () {
    $this->artisan('notes:send-reminders')->assertSuccessful();
    Notification::assertNothingSent();

    $this->travel(61)->minutes();

    $this->artisan('notes:send-reminders')->assertSuccessful();
    Notification::assertSentTo($this->user, NoteReminder::class);
});

test('a reminder is only sent once', function () {
    $this->travel(61)->minutes();

    $this->artisan('notes:send-reminders');
    $this->artisan('notes:send-reminders');

    Notification::assertSentTimes(NoteReminder::class, 1);
    expect($this->note->fresh()->remind_at)->toBeNull();
});

test('reminders must be in the future', function () {
    $response = $this->actingAs($this->user)->post('/notes', [
        'title' => 'Too late',
        'remind_at' => now()->subDay(),
    ]);

    $response->assertInvalid(['remind_at']);
});
```

### Terminal · tests green

```bash
php artisan test --filter=NoteReminderTest
```

### Terminal · test broken

```bash
# SendNoteReminders: removed $note->update(['remind_at' => null])
php artisan test --filter=NoteReminderTest
```

### Terminal · full suite

```bash
php artisan test
```

## Recap

So: notification fake records instead of sending. Travel moves the clock. And breaking the code on purpose proves the test really works.
