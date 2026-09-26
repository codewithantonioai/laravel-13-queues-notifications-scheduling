# Lesson 2 · Your first notification

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=164) (starts at 2:44)

## Goal

Lesson two. We create a notification that reminds a user about one of their notes, and send it by hand to watch the email arrive.

## Commands and files (as shown in the video)

### Terminal · make notification

```bash
php artisan make:notification NoteReminder
```

### `app/Notifications/NoteReminder.php` · note reminder

```php
<?php

namespace App\Notifications;

use App\Models\Note;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class NoteReminder extends Notification
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

### Terminal · tinker notify

```bash
php artisan tinker
$user = User::where('email', 'antonio@example.com')->first();
$note = $user->notes()->latest()->first();
$user->notify(new App\Notifications\NoteReminder($note));
```

## Recap

So: each notification is one class. Via picks the channels, and to mail builds the email. Notify on the user sends it. But right now it's sent while we wait. Let's fix that.
