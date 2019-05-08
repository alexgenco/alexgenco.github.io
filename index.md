---
title: alexgenco.github.io
---

## Ruby function composition with `<<` and `>>` (requires Ruby 2.6)

```ruby
p1 = proc { |x| x + 1 }
p2 = proc { |x| x * 2 }
p3 = p1 >> p2
p4 = p1 << p2

p3.call(5) # => (5 + 1) * 2 = 12
p4.call(5) # => (5 * 2) + 1 = 11
```

```shell
git revert OLDER_COMMIT^..NEWER_COMMIT
```

## Revert a range of commits

```shell
git revert OLDER_COMMIT^..NEWER_COMMIT
```

---

## Shell redirect to udp/tcp socket ([article](https://www.anmolsarma.in/post/bash-net-redirections/))

```shell
# udp
echo ping > /dev/udp/127.0.0.1/1234

# tcp
echo ping > /dev/tcp/127.0.0.1/1234
```

---

## Readline bindings

| Binding         | Command                    | Meaning                                                 |
|---              |---                         |---                                                      |
| `ctrl-x ctrl-e` | `edit-and-execute-command` | Open current command in `$EDITOR`                       |
| `ctrl-x ctrl-u` | `undo`                     | Undo last edit of this command line                     |
| `esc-{`         | `complete-into-braces`     | Put all possible bracket expansions at cursor           |
| `esc-.`         | `insert-last-argument`     | Put the last argument of the previous command at cursor |
| `esc-t`         | `transpose-words`          | Swap word at cursor and the previous word               |

---
