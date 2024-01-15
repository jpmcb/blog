---
title: "Job scheduling with tmux"
date: 2024-01-15T09:00:00-00:00
---

Tmux is one of my favorite utilities: it's a terminal multiplexer
that lets you create persistent shell sessions, panes, windows, etc. all within a single
terminal. It's a great way to organize your shell sessions and natively give you
multi-shell environments to work in without having to rely on a terminal program for those features.

You'd think in a world of modern applications and fancy terminals
like iTerm 2 and Kitty, you wouldn't need such a utility. But time and time again,
tmux has proven itself to be a powerful and essential tool.
Especially when working with remote machines in the cloud or across SSH sessions,
tmux is critical in maintaining my organization and getting things done.

Beyond multiplexing, tmux has some incredible capabilities that extend its functionality
to be able to run and schedule jobs, automatically execute scripts within given contexts,
and much more.

Let's look at a few use cases where we can schedule jobs to run
and even create a whole production like environment, all organized and managed from tmux!

## Running commands

Tmux offers a way to run scripts in new sessions automatically:

```sh
tmux new -s my-session -c /path/to/directory 'echo "Hello Tmux!" && sleep 100'
```

Let's break this down: this arbitrary example creates a new session named "my-session",
sets the session directory using the `-c` flag, and then executes a command.

This command will echo "Hello Tmux!" and then sleep for 100 seconds.

When running this tmux command, we are automatically attached to the session and see
"Hello Tmux!" printed at the top of the screen and then the `sleep` command takes over.
Once the `sleep` command is done, the session exits.

If we wanted to run this in the background, we could provide the `-d` flag: this will
keep the new session detached and run the given commands behind the scenes in the background.

```text
$ tmux new -s my-session -d -c ~/workspace 'echo "hello world!" && sleep 1000'

$ tmux ls
my-session: 1 windows (created Mon Jan 15 11:02:21 2024)
```

Using `tmux ls` we can list out the current sessions and see `my-session` is running with 1 window in the background.
This is part of the power of tmux: you can have sessions exist and persist _outside_
of the current shell or session you are attached to. The sky is really the limit here
and using multiple sessions, windows, and panes has become a cornerstone of my workflows.

If we wanted to attach to the session and see the progress of the command we gave it, we could run `tmux a -t my-session`.
This will attach to the session named `my-session`.

## Persisting sessions

This is all great, but not all that useful when need to latter observe the results of our command or persist the history:
running a script for a new session or window or pane will automatically close once it's completed.

Instead, we can use a regular session we create and send it some commands remotely:

As an example, let's say we needed to run some tests in the background on our Typescript project with `npm run test`
and latter observe the results. We can do this with the `send-keys` command for sessions.
Here, I'll be using the OpenSauced API as my playground:

1. Create a new named session:

```sh
# Create a new named, detached session
# that starts in the given directory
tmux new -s my-npm-tests -d -c ~/workspace/opensauced/api
```

2. Send the command

```sh
# Send the test command to the session
tmux send-keys -t my-npm-tests "npm run test" Enter
```

A few things to note here: 

`Enter` uses the special "key binding syntax" for sending a literal `Enter` key
at the end of the command. If we needed to send something else, like "control c",
we could do that with `C-c` or `M-c` for "alt c". Check the official man page
where this has [a full description](http://man.openbsd.org/OpenBSD-current/man1/tmux.1#KEY_BINDINGS)
of what's possible with sending key bindings to sessions.

3. Attach to the session:

```text
tmux a -t my-npm-tests
```

Now that we've sent our test command to the session, at any point in the future we can attach to the session to see
how it did and check the results. Since the session will be persisted after the
command has run, there's no rush to observe the results! The shell's full history for that session will be right there when we need it!

4. Check results

Within the attached session, we can see the full history of the `npm` command
that was sent and check the results! This session is persisted so we can use the shell
from this session to do additional work, detach, close it, etc.

```text
$ npm run test
npm info using npm@9.6.7
npm info using node@v18.17.1

> @open-sauced/api@2.3.0-beta.2 test
> jest

npm info ok

$
```

## Script it!

What if there are 5 or 6 things I want to do behind the scenes?
Maybe I have a build and test process that can run many things in parallel at once?
Instead of using `send-keys` manually, let's create a small script that can do this all for us!

```sh
#!/usr/bin/env bash

# Create named, detached sessions
tmux new -s npm-test -d -c ~/workspace/opensauced/api
tmux new -s npm-build -d -c ~/workspace/opensauced/api

# Send commands to the detached sessions
tmux send-keys -t npm-test "npm run test" Enter
tmux send-keys -t npm-build "npm run build" Enter
```

Running this script yields the following tmux sessions:

```text
❯ tmux ls
npm-build: 1 windows (created Mon Jan 15 11:31:28 2024)
npm-test: 1 windows (created Mon Jan 15 11:31:28 2024)
```

and can be attached to in order to inspect the results of each command.

If the commands to run within individual sessions is more complex than just a sole one liner,
`send-keys` can also run a script or `make` command!

```sh
tmux send-keys -t kubernetes "make build" Enter
```

In this article, I'm assuming you always want to create a new session.
But many of the same rules, flags, and syntaxes also apply to creating new windows, panes, etc.
Tmux has a strong paradigm that is consistent across different ways to multi-plex shells
so it'd be just as simple to create 2 windows instead of two panes that we then send commands to:

```sh
#!/usr/bin/env bash

# Create named windows
tmux new-window -n npm-test -d -c ~/workspace/opensauced/api
tmux new-window -n npm-build -d -c ~/workspace/opensauced/api

# Send commands to the detached sessions
tmux send-keys -t 0:npm-test "npm run test" Enter
tmux send-keys -t 0:npm-build "npm run build" Enter
```

A few things to note here: instead of `-s` for the session name, we provide `-n` for the new window name.
You'll also notice the `send-keys` syntax now includes a `:`. The first part is the name of the session (in my case, session named `0`)
and the name of the window to send the keys to.

### Setting env variables for sessions

An important and powerful thing to remember here is environment variables: tmux provides the ability to
denote global environment variables (env vars available to all new sessions)
and session based env vars. In newer versions of tmux, I recommend setting the local session
variable with the `-e` flag:

```sh
tmux new -s my-session -d -e MYVAR=myvalue -c /dir
```

This session named `my-session` will have access to the `MYVAR` environment variable we provided when creating the new session:

```text
$ echo $MYVAR
myval
```

## Scheduling jobs with `at` and scripts

One of the more powerful things I've used this all for is local job scheduling.
Let's look at 2 examples using `at` and scripts:

#### One off `at` scheduling

`at` is a very basic command line utility that comes packaged with many desktop
Linux distros and lets you do very simple one off scheduling.

For example, let's say that you needed to do a git push 3 hours from now in a specific directory:

```sh
tmux new -d -s git-push-later \
  -c /path/to/your/repo 'echo "git push" | at now + 3 hours'
```

This will create a new detached session named `git-push-later` within the directory for your git repo
and it sends `git push` to the `at` command via a pipe with the argument "now + 3 hours".

Looking at scheduled jobs via `at`:

```text
$ at -l
1       Mon Jan 15 14:46:00 2024
```

I can see there is a scheduled job! Cool!! This isn't _too_ much different than
just running `at` manually from the given current directory, but it can be really useful and powerful
if I'm working in a different directory or need to quickly load up some env vars.
Better yet, you can easily combine this into a script that loads some global tmux environments
to then execute many `at` commands in sequence.

### Shell script scheduling

There are _alot_ of ways in Linux to do what I'm suggesting here, primarily through `cron` and `crontab`
but sometimes for a quick and dirty job that needs to run on repeat every so often in a background shell,
it can be quick and dirty to just wrap what I'm doing in a loop with a sleep command:

```sh
while true; do
    # The command to continously run
    npm run test

    # Sleep for 5 minutes between runs
    sleep 5m
done
```

This can then be thrown in a script and executed via a tmux `send-keys` command like we've seen:

```sh
tmux send-keys -t my-npm-tests "./run-tests-every-5-mins.sh" Enter
```

Why do it this way and not just have a cron job in the background?

For observable things, like builds, tests, etc., I really like to have a persistent
shell session that I can attach to, detach from, and occasionally keep track of.

Usually with this method, these aren't things that are _too_ important, so if the tmux
server dies, it's nothing I can't quickly spin back up with a little tmux script. It's nice having a sort of "location"
where these jobs are running in the background but always reachable from a different tmux window or tab.
I sometimes find I've lost track of things Linux abstracts away with `cron`, `systemd`, etc.
(which is generally a good thing: I don't want to have to think about the things `systemd` is managing!)
So, instead, for the little things I need to keep an eye on, I choose to keep track of them in a tmux session!

## Building production like environments

Using all of this and with my weird tendency to keep track of things in tmux sessions,
let's build a simple production like environment using a starter script,
docker, and a few tmux sessions!

Let's again look at an OpenSauced example: this starts a postgres database in docker,
boots up the API (which will then attach to that database), and then starts the frontend:

```sh
#!/usr/bin/env bash

# Create named, detached sessions
tmux new -s database -d -c ~/workspace/opensauced/api
tmux new -s api -d -c ~/workspace/opensauced/api
tmux new -s frontend -d -c ~/workspace/opensauced/app

# Start the database up
tmux send-keys -t database "docker run -it --rm --name database -p 25060:5432 my_postgres_image:latest" Enter

# Start the API
tmux send-keys -t api "npm run start" Enter

# Start the frontend app
tmux send-keys -t frontend "npm run start" Enter
```

Horrifying, I know.

But surprisingly, I've found this to be a really great way to keep the various
components of our system organized in a system I know well and can easily wrap my head around.

Then, when I'm done with this environment, I can easily tear it down by stopping the tmux sessions:

```sh
tmux kill-session database
tmux kill-session api
tmux kill-session frontend
```

And that's it! Easy organization, job scheduling, and multi-tasking with tmux!

---

{{< comments >}}
