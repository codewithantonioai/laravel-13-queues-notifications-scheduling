# Laravel 13 Queues, Notifications & Scheduled Tasks (Email Reminders)

Companion repository for the **Code with Antonio** video course · [Watch on YouTube](https://youtu.be/wBuYJl4_c7w).

Laravel 13 Queues, Notifications & Scheduled Tasks, hands on: email reminders sent in the background, retried when they fail, on time every minute.

## What you'll build

Notes gets reminders: a scheduled command finds due notes and emails their owner through the queue

## Lessons

| # | Lesson | Video | Folder |
|---|---|---|---|
| 1 | Work that shouldn't block a request | [0:00](https://youtu.be/wBuYJl4_c7w?t=0) | [lessons/01-work-that-shouldn-t-block-a-request](lessons/01-work-that-shouldn-t-block-a-request) |
| 2 | Your first notification | [2:44](https://youtu.be/wBuYJl4_c7w?t=164) | [lessons/02-your-first-notification](lessons/02-your-first-notification) |
| 3 | Put it on the queue | [4:50](https://youtu.be/wBuYJl4_c7w?t=290) | [lessons/03-put-it-on-the-queue](lessons/03-put-it-on-the-queue) |
| 4 | When jobs fail | [7:12](https://youtu.be/wBuYJl4_c7w?t=432) | [lessons/04-when-jobs-fail](lessons/04-when-jobs-fail) |
| 5 | Add reminders to notes | [9:14](https://youtu.be/wBuYJl4_c7w?t=554) | [lessons/05-add-reminders-to-notes](lessons/05-add-reminders-to-notes) |
| 6 | The scheduler | [11:16](https://youtu.be/wBuYJl4_c7w?t=676) | [lessons/06-the-scheduler](lessons/06-the-scheduler) |
| 7 | A daily summary, and production | [13:41](https://youtu.be/wBuYJl4_c7w?t=821) | [lessons/07-a-daily-summary-and-production](lessons/07-a-daily-summary-and-production) |
| 8 | Test it with fakes | [15:45](https://youtu.be/wBuYJl4_c7w?t=945) | [lessons/08-test-it-with-fakes](lessons/08-test-it-with-fakes) |

## Requirements

- Laravel 13.x (13.32.0) · PHP 8.4 · Pest 5.2.1 · Mailpit (Docker) · cola `database`
- PHP, Composer and the Laravel installer (one command, see lesson 2) or Laravel Herd
- Node.js + npm

## Run the final app

```bash
cd app
composer install
cp .env.example .env
php artisan key:generate
touch database/database.sqlite
php artisan migrate
npm install && npm run build
composer run dev
```

## Repository structure

```
README.md          this file
lessons/           one folder per lesson: goal, timestamp, commands and files exactly as in the video
app/               the final working project
assets/            thumbnail
```

## License

MIT — use it for anything, credit appreciated.

---
Generated on 2026-09-26 from the course scripts. Code with Antonio · AI tutor.