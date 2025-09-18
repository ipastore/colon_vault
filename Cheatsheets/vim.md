
## Modes
- **Normal (default):** navigate & run commands.
- **Insert:** type text — press `i` (or `a`, `o`, etc.).
- **Visual:** select text — `v` (char), `V` (line), `Ctrl+v` (block).
- **Command-line:** type `:` from Normal to run ex-commands (`:wq`, `:q!`, …).

## Save / Exit
- `:w`  — write (save)
- `:q`  — quit
- `:wq` / `:x` — write & quit
- `ZZ`  — write & quit (no colon)
- `:q!` — quit **without** saving (abort)
- `:wqa` — write all & quit / `:qa!` — quit all, no save

## Using Vim for Git commit/merge messages
1. Press `i` to enter **Insert** and write the message.
2. Lines starting with `#` are **ignored by Git**.
3. Save & exit: `Esc` → `:wq` → `Enter`.
4. Abort commit: `Esc` → `:q!` → `Enter`.

## Insert mode quickies
- `i` insert before cursor   ·  `I` at start of line
- `a` insert after cursor    ·  `A` at end of line
- `o` new line below         ·  `O` new line above
- Exit insert: `Esc`

## Navigation (Normal mode)
- `h j k l` ← ↓ ↑ →
- `w` next word · `b` back a word · `e` end of word
- `0` start of line · `^` first non-blank · `$` end of line
- `gg` top of file · `G` bottom · `:<num>` go to line
- `{` / `}` paragraph up/down
- `%` jump to matching bracket/paren

## Edit / Delete / Copy–Paste
- `x` delete char  ·  `xp` swap two chars
- `dd` delete (cut) line  ·  `yy` yank (copy) line
- `p` paste after  ·  `P` paste before
- `d$` delete to end of line  ·  `y$` yank to end
- `diw` delete in word  ·  `ciw` change in word (then Insert)
- Indent: `>>` / `<<`  ·  Reflow paragraph: `gqap`

## Undo / Redo
- `u` undo  ·  `Ctrl+r` redo

## Search & Replace
- `/text` search forward · `?text` backward · `n`/`N` next/prev
- `*` search word under cursor
- `:%s/old/new/g`        replace all in file
- `:%s/old/new/gc`       replace with confirm
- add `i` for case-insensitive: `:%s/old/new/gi`

## Windows & Buffers (quick)
- Split: `:sp file` (horizontal), `:vs file` (vertical)
- Move: `Ctrl+w` then `h/j/k/l`
- Close split: `:q`

## Handy settings (optional)
- `:set number`     show line numbers
- `:set nowrap` / `:set wrap`  toggle wrapping
- Commit message helper: `:set textwidth=72` then select text & `gq` to wrap at 72 chars

## Panic button
- **Exit without saving:** `Esc` → `:q!` → `Enter`
- **Help:** `:help` or `:help <topic>` (e.g., `:help :wq`)
```