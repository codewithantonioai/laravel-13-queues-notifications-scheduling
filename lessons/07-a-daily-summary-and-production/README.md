# Lesson 7 · A daily summary, and production

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=821) (starts at 13:41)

## Goal

Lesson seven. A second notification, a morning summary of the day's reminders, and what a real server needs to run all of this.

## Commands and files (as shown in the video)

### `app/Notifications/DailySummary.php` · daily summary

```php
<?php

namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;
use Illuminate\Support\Collection;

class DailySummary extends Notification implements ShouldQueue
{
    use Queueable;

    public function __construct(public Collection $notes)
    {
    }

    public function via(object $notifiable): array
    {
        return ['mail'];
    }

    public function toMail(object $notifiable): MailMessage
    {
        $message = (new MailMessage)
            ->subject('Your reminders for today')
            ->greeting('Good morning, '.$notifiable->name.'!')
            ->line('Here is what you wanted to remember today:');

        foreach ($this->notes as $note) {
            $message->line('• '.$note->remind_at->format('H:i').' · '.$note->title);
        }

        return $message->action('Open my notes', route('notes.index'));
    }
}
```

### `routes/console.php` · schedule call

```php
<?php

use App\Models\User;
use App\Notifications\DailySummary;
use Illuminate\Foundation\Inspiring;
use Illuminate\Support\Facades\Artisan;
use Illuminate\Support\Facades\Schedule;

Artisan::command('inspire', function () {
    $this->comment(Inspiring::quote());
})->purpose('Display an inspiring quote');

Schedule::command('notes:send-reminders')->everyMinute();

Schedule::call(function () {
    $users = User::with([
        'notes' => fn ($query) => $query->whereDate('remind_at', today()),
    ])->get();

    foreach ($users as $user) {
        if ($user->notes->isNotEmpty()) {
            $user->notify(new DailySummary($user->notes));
        }
    }
})->name('daily-summary')->dailyAt('07:00');
```

### Terminal · schedule list 2

```bash
php artisan schedule:list
```

### Terminal · schedule test

```bash
php artisan schedule:test --name=daily-summary
php artisan queue:work
```

## Recap

So: schedule call is enough for small tasks. Schedule test runs one right now. And in production, one cron line plus Supervisor keeps everything running. Last step: tests.
