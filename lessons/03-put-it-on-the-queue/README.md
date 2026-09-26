# Lesson 3 · Put it on the queue

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=290) (starts at 4:50)

## Goal

Lesson three. We measure how long sending takes, then move it onto a queue and let a worker do it.

## Commands and files (as shown in the video)

### Terminal · benchmark sync

```bash
Benchmark::measure(fn () => $user->notify(new NoteReminder($note)));
Benchmark::measure(fn () => $user->notify(new NoteReminder($note)));
```

### `app/Notifications/NoteReminder.php` · should queue

```php
<?php

namespace App\Notifications;

use App\Models\Note;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

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

### Terminal · benchmark queued

```bash
Benchmark::measure(fn () => $user->notify(new NoteReminder($note)));
Benchmark::measure(fn () => $user->notify(new NoteReminder($note)));
DB::table('jobs')->count();
```

### Terminal · queue work

```bash
php artisan queue:work
```

## Recap

So: should queue is one line. The request only writes a job and answers. And a worker, running on its own, does the slow part. But what if the slow part fails?
