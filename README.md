# lsg: ls with git information

## Introduction

The `lsg` tool is intended to give information on files in a directory
in the same style as the `ls` command - but adding more information about
the git status of those files. The idea is to make it possible to see the
git status in the same style as the `ls` command.

The tool is known to work on linux (Ubuntu), and Mac OS (10.12 to 10.14),
and probably works on other systems

## What it looks like

An example 'asciicast' can be seen here: https://asciinema.org/a/219091

[![asciicast](https://asciinema.org/a/219091.svg)](https://asciinema.org/a/219091)

## What it can show

Files within a git repository:

  * File changes are only shown in braces.
  * Untracked and ignored files are shown:
      - `{untracked}`                 - File is not known to git
      - `{ignored}`                   - File is listed as a pattern in .gitignore
  * Changed files in the working tree are shown:
      - `{modified locally, 3 lines}` - Changed in working tree, 3 lines affected (added or deleted)
      - `{deleted locally}`           - File has been deleted in working tree
  * Changed files in the cache are shown:
      - `{staged, 3 lines}`           - Changes have been staged, 3 lines affected (added or deleted)
      - `{deleted}`                   - File has been marked for deletion in cache
      - `{added}`                     - File has been marked for addition in cache
      - `{renamed}`                   - File has been renamed to this name in the cache
  * Merge status is show:
      - `{unmerged, both added}`      - File was added by merge, and we added it as well.

Directories within a git repository:

  * Only untracked or ignored state is shown (to make lists faster):
      - `{untracked}`                 - Directory is not known to git
      - `{ignored}`                   - Directory is listed as a pattern in .gitignore

Repositories and submodules:

  * Branch details are in brackets:
      - `(master)`            - directory is at the head of branch master
  * Relative location compared to upstream is shown with arrows after the name
      - `(master￪1)`          - 1 commits locally ready to be pushed
      - `(master￬7)`          - 8 commits available in fetched upstream
      - `(master⊗)`           - no upstream is configured
      - `(master~2○)`         - detached head
  * Submodule reference changes are described within the brackets:
      - `(master added)`      - Submodule has been added
      - `(master 8 forward)`  - Submodule has moved 8 changes forward in the same tree (in working tree)
      - `(master 2 back)`     - Submodule has moved 2 changes forward in the same tree (in working tree)
      - `(master new ref)`    - Submodule has changed to a ref unrelated to the old tree (in working tree)
      - `(master staged 1 forward)`   - Submodule has moved 1 changes forward in the same tree (in the cache)
  * Summary of changes to the files within the submodule are shown in braces:
      - `{1 modified}`        - 1 file has been changed in the working tree
      - `{2 staged}`          - 2 files have been changed and staged in the working tree
  * All the above options can be combined.


## Requirements
  * `bash` - version 3 or 4; although it'll be slower on 3 due to lack of associative arrays
  * `git` - obviously
  * `ls`
      - BSD version supplied with Mac OS or a GNU version
      - colour is supported (and explicitly forced)
  * `perl` - version 5 upward (although might be version 3 compatible)
  * `grep` - BSD version supplied with Mac OS or a GNU version
  * `sed` - BSD version supplied with Mac OS or a GNU version


## How to use it

The `lsg` command can be used almost exactly like the `ls` command.
The actual operation of the command is to invoke `ls` and then post-process
the output, so most of the `ls` switches and uses are exactly the same.
However, because it only handles single columns, the switches related to
multi-column, comma-separated and dired formats are not usable.

Because the command expects to use colour, colouring will be forced in the
ls output.

There is an extra switch that the `lsg` tool adds for showing nested
subdirectories. The `--nest` (or `--nest=<depth>`) switch can be used to
list subdirectories as well.


## Configuration

Colouring for the output is taken from git configuration.
If you wish to change the colours used, the following configurations are
read:

| config name            | usage    | default |
| ---------------------- | -------- | ------- |
| color.status.branch    | Branch name | cyan |
| color.status.added     | Added file | green |
| color.status.updated   | Staged file | green |
| color.status.changed   | Modified file | yellow |
| color.status.untracked | Not known to git | red |
| color.status.unmerged  | Not yet merged | red bold |
| color.status.ignored   | Ignored by .gitignore | dim |

Towards the top of the code, there are a number of constant symbols
assigned which define the symbols used to show some states. Only the
branch state and relative position in the git history use symbols;
otherwise words are used to describe the status.

Environment variables can be used to control the invocation of the
tool:

* `LSG_COMMAND`       - The command to call (defaults to `ls`); use `gls` on Mac OS to force the GNU version.
* `LSG_OPTIONS`       - Default options to supply to the `ls` command, eg `-l` would force the listing to always be long.
