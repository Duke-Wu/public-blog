# How to process SIGTERM signal gracefully?

[How to process SIGTERM signal gracefully?](https://stackoverflow.com/questions/18499497/how-to-process-sigterm-signal-gracefully)

https://docs.python.org/3.7/library/signal.html#signal.signal

#### Question

Let's assume we have such a trivial daemon written in python:

```python
def mainloop():
    while True:
        # 1. do
        # 2. some
        # 3. important
        # 4. job
        # 5. sleep

mainloop()
```

and we daemonize it using `start-stop-daemon` which by default sends `SIGTERM` (`TERM`) signal on `--stop`.

Let's suppose the current step performed is `#2`. And at this very moment we're sending `TERM` signal.

What happens is that the execution terminates immediately.

I've found that I can handle the signal event using `signal.signal(signal.SIGTERM, handler)` but the thing is that it still interrupts the current execution and passes the control to `handler`.

So, my question is - is it possible to not interrupt the current execution but handle the `TERM` signal in a separated thread (?) so that I was able to set `shutdown_flag = True` so that `mainloop()` had a chance to stop gracefully?

#### Answer

A class based clean to use solution:

```python
import signal
import time

class GracefulKiller:
  kill_now = False
  def __init__(self):
    signal.signal(signal.SIGINT, self.exit_gracefully)
    signal.signal(signal.SIGTERM, self.exit_gracefully)

  def exit_gracefully(self,signum, frame):
    self.kill_now = True

if __name__ == '__main__':
  killer = GracefulKiller()
  while not killer.kill_now:
    time.sleep(1)
    print("doing something in a loop ...")

  print "End of the program. I was killed gracefully :)"
```


