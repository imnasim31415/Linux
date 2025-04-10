
# Mastering Vim on Ubuntu: A Beginner-to-Intermediate Guide

Vim (Vi Improved) is a powerful text editor used widely for programming and system administration. This guide is focused on **Ubuntu users**, from installing Vim to mastering essential commands.

---

## 📦 1. Installing Vim on Ubuntu

Open a terminal and run:

```bash
sudo apt update
sudo apt install vim
```

---

## 🚀 2. Launching Vim

```bash
vim filename
```

- If the file doesn’t exist, it will be created upon saving.

---

## 🧠 3. Vim Modes

| Mode        | Description                            | Entering Mode          |
|-------------|----------------------------------------|------------------------|
| Normal      | Default mode for navigation/commands   | Press `Esc`            |
| Insert      | For inserting text                     | Press `i`, `a`, or `o` |
| Visual      | Select text                            | Press `v` or `V`       |
| Command     | Run commands like save, quit, search   | Press `:` from Normal  |

---

## ✍️ 4. Insert Mode Commands

| Key        | Action                          |
|------------|----------------------------------|
| `i`        | Insert before the cursor        |
| `I`        | Insert at beginning of line     |
| `a`        | Append after the cursor         |
| `A`        | Append at end of line           |
| `o`        | Open new line below             |
| `O`        | Open new line above             |
| `Esc`      | Return to Normal mode           |

---

## 🕹️ 5. Navigation in Normal Mode

| Command    | Action                            |
|------------|------------------------------------|
| `h`        | Move left                          |
| `l`        | Move right                         |
| `j`        | Move down                          |
| `k`        | Move up                            |
| `0`        | Move to beginning of line          |
| `^`        | Move to first non-blank character  |
| `$`        | Move to end of line                |
| `w`        | Jump to next word                  |
| `b`        | Jump to previous word              |
| `G`        | Go to last line                    |
| `gg`       | Go to first line                   |
| `:n`       | Go to line number `n`              |

---

## 💾 6. Saving and Quitting

| Command    | Action                             |
|------------|-------------------------------------|
| `:w`       | Save file                           |
| `:q`       | Quit                                |
| `:wq`      | Save and quit                       |
| `:q!`      | Quit without saving                 |
| `ZZ`       | Save and quit (normal mode shortcut)|

---

## ✂️ 7. Editing Text

| Command    | Action                             |
|------------|-------------------------------------|
| `x`        | Delete character                    |
| `dd`       | Delete line                         |
| `yy`       | Copy (yank) line                    |
| `p`        | Paste below                         |
| `P`        | Paste above                         |
| `u`        | Undo                                |
| `Ctrl + r` | Redo                                |
| `r<char>`  | Replace single character            |
| `cw`       | Change word                         |
| `cc`       | Change whole line                   |

---

## 🔍 8. Search and Replace

| Command               | Action                                 |
|-----------------------|-----------------------------------------|
| `/word`               | Search forward for 'word'              |
| `?word`               | Search backward for 'word'             |
| `n`                   | Repeat last search forward             |
| `N`                   | Repeat last search backward            |
| `:%s/old/new/g`       | Replace all 'old' with 'new' globally  |
| `:%s/old/new/gc`      | Confirm each replacement               |

---

## 🔄 9. Useful Visual Mode Shortcuts

| Command    | Action                              |
|------------|--------------------------------------|
| `v`        | Start characterwise selection        |
| `V`        | Start linewise selection             |
| `d`        | Delete selection                     |
| `y`        | Yank (copy) selection                |
| `>` / `<`  | Indent/Un-indent selection           |

---

## 🧰 10. Miscellaneous Tips

- Use `:set number` to show line numbers.
- Use `:syntax on` for syntax highlighting.
- Use `:set tabstop=4` to change tab width.

---

## 🏁 Conclusion

Vim is incredibly powerful once mastered. While the learning curve can be steep, it's worth the effort. Start with basic movements and gradually expand your skill set.

Happy Vimming! 🚀
