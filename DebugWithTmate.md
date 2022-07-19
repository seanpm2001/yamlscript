Debug with tmate
================

Simple Instructions for Remote Debug on Win/Mac/Linux

## Introduction

With [This GitHub Action](
https://github.com/marketplace/actions/debugging-with-tmate) you can start a
remote debugging shell session on Linux, Mac and even Windows.

This document describes the steps I use to do this.

## Initial Setup

* Create a branch from `main` or `master` called `debug-with-tmate`.

* Add a file called `./.github/workflows/debug-with-tmate.yaml` with content:

  ```
  name: CI
  on: [push]
  jobs:
    build:
      # runs-on: ubuntu-22.04
      # runs-on: ubuntu-20.04
      runs-on: windows-2022
      # runs-on: windows-2019
      # runs-on: macos-12
      # runs-on: macos-11
      steps:
      - uses: actions/checkout@v2
      - name: Setup tmate session
        uses: mxschmitt/action-tmate@v3
  ```

* Select the operating system you want to debug with by uncommenting it

* Commit the file with msg: "Debug with tmate"

* Push the commit to GitHub

## Running the tmate

* Click the "Actions" tab on any GitHub page

* The top Workflow entry should be running
  * Open it
  * You may have to wait a minute for the job to start tmate
  * Eventually the log will print (every 5 secs):

    ```
    Web shell: https://tmate.io/t/7p5MkvnAVH8caRq6s5seRxZGA
    SSH: ssh 7p5MkvnAVH8caRq6s5seRxZGA@nyc1.tmate.io
    ```

* Open the tmate connection
  * Using the ssh command in a terminal
  * Or opening the URL to a web page with an embedded terminal

* You will see a msg like this at the top:

  ```
  Tip: if you wish to use tmate only for remote access, run: tmate -F
  To see the following messages again, run in a tmate session: tmate show-messages
  Press <q> or <ctrl-c> to continue
  ---------------------------------------------------------------------
  Connecting to ssh.tmate.io...
  Note: clear your terminal before sharing readonly access
  web session read only: https://tmate.io/t/ro-r7rKbZ2JJLmvNra5mSgujwzAh
  ssh session read only: ssh ro-r7rKbZ2JJLmvNra5mSgujwzAh@nyc1.tmate.io
  web session: https://tmate.io/t/7p5MkvnAVH8caRq6s5seRxZGA
  ssh session: ssh 7p5MkvnAVH8caRq6s5seRxZGA@nyc1.tmate.io
  ```

* Hit ctl-C to erase the message and begin a shell session
  * Your working directory will be a clone of your repo
  * The clone will be shallow depth=1

* You will be in the `debug-with-tmate` branch
  * That's ok because the branch will always be a single tmate commit over main

* Debug your code

* When you exit the shell, the GitHub action will complete

## Subsequent sessions

This section describes how to maintain your `debug-with-tmate` branch cleanly.

Essentially you will use `git rebase`, `git commit --amend` and `git push -f`
so that your debugging commit will always be a single commit over the code you
are testing.

* To update the code to the latest main:
  * `git rebase main`

* If you need to change the remote OS, edit the `debug-with-tmate.yaml` file.
  * Commit the change with `git commit --amend`
  * Then `git push --force`

* If you need to start a new tmate with nothing to commit/push
  * Run `git commit --amend --no-edit`
  * This will change the commit id
  * Then `git push --force` will trigger a new build (tmate)
  * You can also manually re-run the last job from the web page

## Notes on Windows

* The starting environment is a bash shell using https://gitforwindows.org/
  * Bash 5.1.16
  * Perl 5.32.1
    * cpan, cpanm
  * Python 3.9.13
    * pip
  * gcc 11.2.0
  * GNU make 4.3
  * MS 'cmd' shell 10.0.20348.768
  * MS PowerShell

* You can run `cmd` to start the windows `cmd` shell

* You can run `powershell` to start a PowerShell shell

### Debugging Perl Modules on Windows

I've had numerous problems trying to run `make test` on Windows.

The `cpanm` and `vim` (and other) commands are pre-installed, but they don't
work.
Also `perl Makefile.PL` runs fine, but `make` fails with a weird error.

You can fix all this by using the `pacman` package installer:

Run: `pacman --sync --noconfirm make vim`.

After that everything worked as expected.

#### Debugging Perl Modules on Windows with Strawberry Perl

The default shell and perl are very Unix like.

To run in a more native Windows environment you'll want to use the cmd shell.

Run `cmd` to start the cmd shell.
Note that this shell is really bad.
It doesn't have tab completion.
Use the `exit` command when you want to leave (`ctl-D` doesn't work).
Hit `Enter` after using `ctl-C`.
The `ls` and `cd` commands work pretty much like Bash.
You'll have to figure the rest out yourself.

To use Strawberry Perl, put it in the PATH with:
`set PATH=C:\strawberry\perl\bin;C:\strawberry\perl\site\bin;C:\strawberry\c\bin;%PATH%`

After that you can install CPAN modules with `cpanm` (you don't even to install
`make` with pacman).

I'm still figuring out how shebang lines work in Windows...
