[//]: # "NOTE: THIS FILE WAS AUTOGENERATED FROM README.md"

# Installation and usage

## Installation

_Black_ can be installed by running `pip install black`. It requires Python 3.6.0+ to
run but you can reformat Python 2 code with it, too.

### Install from GitHub

If you can't wait for the latest _hotness_ and want to install from GitHub, use:

`pip install git+git://github.com/psf/black`

## Usage

To get started right away with sensible defaults:

```sh
black {source_file_or_directory}
```

You can run _Black_ as a package if running it as a script doesn't work:

```sh
python -m black {source_file_or_directory}
```

## Command line options

_Black_ doesn't provide many options. You can list them by running `black --help`:

```text
Usage: black [OPTIONS] [SRC]...

  The uncompromising code formatter.

Options:
  -c, --code TEXT                 Format the code passed in as a string.
  -l, --line-length INTEGER       How many characters per line to allow.
                                  [default: 88]

  -t, --target-version [py27|py33|py34|py35|py36|py37|py38]
                                  Python versions that should be supported by
                                  Black's output. [default: per-file auto-
                                  detection]

  --pyi                           Format all input files like typing stubs
                                  regardless of file extension (useful when
                                  piping source on standard input).

  -S, --skip-string-normalization
                                  Don't normalize string quotes or prefixes.
  --check                         Don't write the files back, just return the
                                  status.  Return code 0 means nothing would
                                  change.  Return code 1 means some files
                                  would be reformatted. Return code 123 means
                                  there was an internal error.

  --diff                          Don't write the files back, just output a
                                  diff for each file on stdout.

  --color / --no-color            Show colored diff. Only applies when
                                  `--diff` is given.

  --fast / --safe                 If --fast given, skip temporary sanity
                                  checks. [default: --safe]

  --include TEXT                  A regular expression that matches files and
                                  directories that should be included on
                                  recursive searches.  An empty value means
                                  all files are included regardless of the
                                  name.  Use forward slashes for directories
                                  on all platforms (Windows, too).  Exclusions
                                  are calculated first, inclusions later.
                                  [default: \.pyi?$]

  --exclude TEXT                  A regular expression that matches files and
                                  directories that should be excluded on
                                  recursive searches.  An empty value means no
                                  paths are excluded. Use forward slashes for
                                  directories on all platforms (Windows, too).
                                  Exclusions are calculated first, inclusions
                                  later.  [default: /(\.eggs|\.git|\.hg|\.mypy
                                  _cache|\.nox|\.tox|\.venv|\.svn|_build|buck-
                                  out|build|dist)/]

  --force-exclude TEXT            Like --exclude, but files and directories
                                  matching this regex will be excluded even
                                  when they are passed explicitly as arguments.

  -q, --quiet                     Don't emit non-error messages to stderr.
                                  Errors are still emitted; silence those with
                                  2>/dev/null.

  -v, --verbose                   Also emit messages to stderr about files
                                  that were not changed or were ignored due to
                                  --exclude=.

  --version                       Show the version and exit.
  --config FILE                   Read configuration from FILE path.
  -h, --help                      Show this message and exit.
```

_Black_ is a well-behaved Unix-style command-line tool:

- it does nothing if no sources are passed to it;
- it will read from standard input and write to standard output if `-` is used as the
  filename;
- it only outputs messages to users on standard error;
- exits with code 0 unless an internal error occurred (or `--check` was used).

## Using _Black_ with other tools

While _Black_ enforces formatting that conforms to PEP 8, other tools may raise warnings
about _Black_'s changes or will overwrite _Black_'s changes. A good example of this is
[isort](https://pypi.org/p/isort). Since _Black_ is barely configurable, these tools
should be configured to neither warn about nor overwrite _Black_'s changes.

Actual details on _Black_ compatible configurations for various tools can be found in
[compatible_configs](https://github.com/psf/black/blob/master/docs/compatible_configs.md).

## Migrating your code style without ruining git blame

A long-standing argument against moving to automated code formatters like _Black_ is
that the migration will clutter up the output of `git blame`. This was a valid argument,
but since Git version 2.23, Git natively supports
[ignoring revisions in blame](https://git-scm.com/docs/git-blame#Documentation/git-blame.txt---ignore-revltrevgt)
with the `--ignore-rev` option. You can also pass a file listing the revisions to ignore
using the `--ignore-revs-file` option. The changes made by the revision will be ignored
when assigning blame. Lines modified by an ignored revision will be blamed on the
previous revision that modified those lines.

So when migrating your project's code style to _Black_, reformat everything and commit
the changes (preferably in one massive commit). Then put the full 40 characters commit
identifier(s) into a file.

```
# Migrate code style to Black
5b4ab991dede475d393e9d69ec388fd6bd949699
```

Afterwards, you can pass that file to `git blame` and see clean and meaningful blame
information.

```console
$ git blame important.py --ignore-revs-file .git-blame-ignore-revs
7a1ae265 (John Smith 2019-04-15 15:55:13 -0400 1) def very_important_function(text, file):
abdfd8b0 (Alice Doe  2019-09-23 11:39:32 -0400 2)     text = text.lstrip()
7a1ae265 (John Smith 2019-04-15 15:55:13 -0400 3)     with open(file, "r+") as f:
7a1ae265 (John Smith 2019-04-15 15:55:13 -0400 4)         f.write(formatted)
```

You can even configure `git` to automatically ignore revisions listed in a file on every
call to `git blame`.

```console
$ git config blame.ignoreRevsFile .git-blame-ignore-revs
```

**The one caveat is that GitHub and GitLab do not yet support ignoring revisions using
their native UI of blame.** So blame information will be cluttered with a reformatting
commit on those platforms. (If you'd like this feature, there's an open issue for
[GitLab](https://gitlab.com/gitlab-org/gitlab/-/issues/31423) and please let GitHub
know!)

## NOTE: This is a beta product

_Black_ is already [successfully used](#used-by) by many projects, small and big. It
also sports a decent test suite. However, it is still very new. Things will probably be
wonky for a while. This is made explicit by the "Beta" trove classifier, as well as by
the "b" in the version number. What this means for you is that **until the formatter
becomes stable, you should expect some formatting to change in the future**. That being
said, no drastic stylistic changes are planned, mostly responses to bug reports.

Also, as a temporary safety measure, _Black_ will check that the reformatted code still
produces a valid AST that is equivalent to the original. This slows it down. If you're
feeling confident, use `--fast`.