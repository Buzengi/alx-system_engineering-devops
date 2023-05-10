# Web stack debugging #3

Fourth in a series of web stack debugging projects. In these
projects, A broken/bugged webstacks in isolated containers was given,
and tasked with fixing the web stack to a working state. For this
task, I wrote a script automating the commands necessary to fix the
web stack.

## Tasks :page_with_curl:

* **0. Strace is your friend**
  * [0-strace_is_your_friend.pp](./0-strace_is_your_friend.pp): Puppet manifest
  that fixes a typo error causing a WordPress application being served by an Apache
  web server to fail.
  * Usage: `puppet apply 0-strace_is_your_friend.pp`
