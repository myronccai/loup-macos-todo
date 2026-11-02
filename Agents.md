# Agents.md — loup-macos-todo

A to-do list manager for macOS written in Loup on the `appkit` library: a
table with done check boxes, titles and priorities; add / delete / clear
completed; a "Hide completed" filter; a status line; JSON open / save. See
[README.md](README.md) for what it does, the file format, the structure and
the self-test; its "Getting started" section has the requirements and the
clone / build / test commands.

## Where things are

- `loup-macos-todo.lppkg` — the manifest (`host.native`, native delivery, the
  `appkit` path dependency, `-framework AppKit`).
- `src/main.lphc` — the UI and the `--self-test` driver (`host.native`).
- `src/todolist.loup` — the model, the filter and the status line
  (`host.dual`).
- `src/todofile.loup` — the JSON writer and reader (`host.dual`).
- `src/*_test.lpscr` — `loup test --strict src`.
- `selftest.out` — the golden self-test output; `screenshot.png` — taken by
  the self-test.
- The library: [loup-macos-appkit](https://github.com/myronccai/loup-macos-appkit),
  a separate repository that must be checked out next to this one, at
  `../loup-macos-appkit/` (the manifest's path dependency). Each module's
  header comment is its reference;
  [docs/table.md](https://github.com/myronccai/loup-macos-appkit/blob/main/docs/table.md)
  and [docs/dialogs.md](https://github.com/myronccai/loup-macos-appkit/blob/main/docs/dialogs.md)
  (`../loup-macos-appkit/docs/table.md`, `../loup-macos-appkit/docs/dialogs.md`
  in the sibling checkout) cover the parts this app leans on most: check-box
  columns, `onSort`, the table test hooks, canned dialog answers. The
  collection overview:
  [README.md](https://github.com/myronccai/loup-macos-appkit/blob/main/README.md)
  and [Agents.md](https://github.com/myronccai/loup-macos-appkit/blob/main/Agents.md)
  in the appkit repository.
- The Loup macOS developer skill, bundled with the library:
  `../loup-macos-appkit/skills/loup-macos-developer-skill/SKILL.md` (in the
  sibling checkout; on GitHub:
  [skills/loup-macos-developer-skill/SKILL.md](https://github.com/myronccai/loup-macos-appkit/blob/main/skills/loup-macos-developer-skill/SKILL.md)).
  Read it before changing the UI code.

## How to work here

- Keep logic in the `host.dual` modules and test it there; `main.lphc` only
  shows the model and forwards events to it.
- `TodoList`'s changing methods are `modifier`s: call them in `safe { }`.
  todofile's reader functions take the `Reader` as `edits`, which makes them
  safe contexts themselves.
- Use the appkit API; the app has no raw Objective-C (`appkit.objc`) at all.
  If the library lacks something, add the raw call with a comment saying why
  and list it in README.md.
- Verify with `bash ../loup-macos-appkit/run_all.sh loup-macos-todo` (both
  drivers, `loup test`, self-test against `selftest.out`); `--screenshots`
  refreshes the picture. It needs a Loup toolchain of newer than 1.27.0 and the
  appkit checkout beside this one (see "Getting started" in README.md).
  Update `selftest.out` when the self-test's output changes on purpose.
- Never launch the app without `--self-test` from an automated session, and
  queue an answer (`setTestAnswers`) before anything in the self-test that
  shows a dialog.
- Build output (`.temp/`, `.loup-cache/`, `loup-macos-todo`, `*.loupbuild.fp`) is
  ignored; `run_all.sh` cleans it.
