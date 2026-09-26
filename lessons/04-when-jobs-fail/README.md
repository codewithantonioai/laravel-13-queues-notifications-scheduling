# Lesson 4 · When jobs fail

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=432) (starts at 7:12)

## Goal

Lesson four. The mail server goes down. What happens to our reminders?

## Commands and files (as shown in the video)

### Terminal · mail down

```bash
docker stop mailpit
# a new reminder was queued from Tinker
php artisan queue:work
```

### Terminal · queue failed

```bash
php artisan queue:failed
```

### `app/Notifications/NoteReminder.php` · tries backoff

```php
<?php

namespace App\Notifications;

use App\Models\Note;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;
use Illuminate\Queue\Attributes\Backoff;
use Illuminate\Queue\Attributes\Tries;

#[Tries(3)]
#[Backoff(5)]
class NoteReminder extends Notification implements ShouldQueue
{
    use Queueable;

    public function __construct(public Note $note)
    {
    }

    public function via(object $notifiable): array
    {
        return ['mail'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Reminder: '.$this->note->title)
            ->greeting('Hi '.$notifiable->name.'!')
            ->line('You asked us to remind you about this note:')
            ->line('"'.$this->note->title.'"')
            ->action('Open the note', route('notes.edit', $this->note))
            ->line('Have a productive day!');
    }
}
```

### Terminal · retries

```bash
# Mailpit still down · a new reminder queued
php artisan queue:work
php artisan queue:failed
```

### Terminal · retry all

```bash
docker start mailpit
php artisan queue:retry all
php artisan queue:work
```

## Recap

So: jobs get one attempt unless you say otherwise. Tries and backoff give them room to recover. And queue failed and queue retry bring back the ones that didn't make it. Now, let's give notes a reminder time.
