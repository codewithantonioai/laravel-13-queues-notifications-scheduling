# Lesson 1 · Work that shouldn't block a request

▶ [Watch this lesson](https://youtu.be/wBuYJl4_c7w?t=0) (starts at 0:00)

## Goal

Lesson one. Some work doesn't belong inside a web request. Let's see which, and the three tools Laravel gives us for it.

## Recap

So: slow work shouldn't make a request wait. A notification says what to send. The queue and the scheduler say when. Let's build the notification.
