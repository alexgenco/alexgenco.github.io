---
title: alexgenco.github.io
description: Mostly for remembering things
layout: default
---

- ## Revert a range of commits

```shell
git revert OLDER_COMMIT^..NEWER_COMMIT
```

- ## Get `readline` bindings

```shell
bind -p
```

- ## Highlights from `bind -p`

| Binding         | Command                    | Meaning                                                 |
|---              |---                         |---                                                      |
| `ctrl-x ctrl-e` | `edit-and-execute-command` | Open current command in `$EDITOR`                       |
| `ctrl-x ctrl-u` | `undo`                     | Undo last edit of this command line                     |
| `esc-{`         | `complete-into-braces`     | Put all possible bracket expansions at cursor           |
| `esc-.`         | `insert-last-argument`     | Put the last argument of the previous command at cursor |
| `esc-t`         | `transpose-words`          | Swap word at cursor and the previous word               |

- ## [Shell redirect to udp/tcp socket](https://www.anmolsarma.in/post/bash-net-redirections/)

```shell
# udp
echo ping > /dev/udp/127.0.0.1/1234

# tcp
echo ping > /dev/tcp/127.0.0.1/1234
```
