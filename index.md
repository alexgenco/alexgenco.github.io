---
title: alexgenco.github.io
layout: default
---

- Revert a range of commits

```shell
git revert OLDER_COMMIT^..NEWER_COMMIT
```

- Get `readline` bindings

```shell
bind -p
```

- Highlights from `bind -p`

| Binding         | Command                    | Meaning                                                 |
|---              |---                         |---                                                      |
| `ctrl-x ctrl-e` | `edit-and-execute-command` | Open current command in `$EDITOR`                       |
| `ctrl-x ctrl-u` | `undo`                     | Undo last edit of this command line                     |
| `esc-{`         | `complete-into-braces`     | Put all possible bracket expansions at cursor           |
| `esc-.`         | `insert-last-argument`     | Put the last argument of the previous command at cursor |
| `esc-t`         | `transpose-words`          | Swap word at cursor and the previous word               |
