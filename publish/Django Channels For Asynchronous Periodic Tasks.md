

## Django Channels For Asynchronous Periodic Tasks

> https://stackoverflow.com/questions/42513915/django-channels-for-asynchronous-periodic-tasks

### Questions

I found that most of the docs regarding Django Channels are about WebSockets. But I want to use them in a different way, and I believe it is possible.

How to run the async periodic task using Django channels? For example, I want to check the temperature on some website (through the API) every 15 seconds and I need a notification when its hit > 20.

It also means that this task will live for a long time (maybe even for 3 month), is Django capable of keeping the consumers live for a long time?

### Answer

Channels is easily put to work for background tasks - see here for notes on doing so with the new version of Channels:

<https://github.com/jayhale/channels-examples-bg-task>

With a background task in place, you could set up a task that you could call with cron that puts your tasks in the queue at whatever period you would like. Downside to cron is that it doesn't do sub-minute scheduling out of the box - some hacking is required. See: [Running a cron every 30 seconds](https://stackoverflow.com/questions/9619362/running-a-cron-every-30-seconds)

E.g.:

Add a job to the app user's crontab:

```shell
# crontab
/5 * * * * python manage.py cron_every_5_minutes
```

Your custom management command can spawn the tasks for channels:

```python
# myapp/management/commands/cron_every_5_minutes.py

from asgiref.sync import async_to_sync
from channels.layers import get_channel_layer
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    channel_layer = get_channel_layer()
    help = 'Periodic task to be run every 5 minutes'

    def handle(self, *args, **options):
        async_to_sync(channel_layer.send)('background-tasks', {'type': 'task_5_min'})
        
```

Regarding the expected reliability of a worker - they can run indefinitely, but you should expect them to occasionally fail. Managing the workers is more of a question of how you intend to supervise the processes (or containers, or however you are architecting).



### Reference

https://github.com/django/channels/pull/530

https://github.com/django/channels/issues/551

https://github.com/django/channels/issues/74

