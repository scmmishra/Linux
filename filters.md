## Linux Filters

Linux Filter commands accept input data from stdin (standard input) and produce output on stdout (standard output). It transforms plain-text data into a meaningful way and can be used with pipes to perform higher operations.

### `cat`

The `cat` utility reads files sequentially, writing them to the standard output.

### `cut`

The cut utility cuts out selected portions of each line (as specified by list) from each file and writes them to the standard output. If no file arguments are specified, or a file argument is a single dash (`-'), cut reads from the standard input. The items specified by list can be in terms of column position or in terms of fields delimited by a special character. Column numbering starts from 1.

SYNOPSIS
cut -b list [-n][file ...]
cut -c list [file ...]
cut -f list [-d delim][-s] [file ...]

### `grep`

The grep utility searches any given input files, selecting lines that match one or more patterns. By default, a pattern matches an input line if the regular expression (RE) in the pattern matches the input line without its trailing newline. An empty expression matches every line. Each input line that matches at least one of the patterns is written to the standard output.

```
command | grep <searchWord>
grep <searchWord> <file name>
```

#### Options

*   grep -vM: The 'grep -v' command displays lines not matching to the specified word.
*   grep -i: The 'grep -i' command filters output in a case-insensitive way.
*   grep -A command is used to display the line after the result.
*   grep -B command is used to display the line before the result.
*   grep -C command is used to display the line after and line before the result.

### `sed`

The sed utility reads the specified files, or the standard input if no files are specified, modifying the input as specified by a list of commands. The input is then written to the standard output. This editing is not permanent, it remains only in display, but in actual, file content remains same.

```
command | sed 's/<oldWord>/<newWord>/'
```

#### Global Replacement

To edit every word we have to use a global replacement 'g'. It will edit all the specified word in a file or string.

```
command | sed 's/<oldWord>/<newWord>/g'
```

#### Removing A Line

The 'd' option will let you to remove a complete line from a file. You only need to specify a word from that line with 'd' option and that line will be deleted. But please note that all the lines having that same word will be deleted.

```
cat <fileName> | sed '/<Word>/d'
```

### `tee`

The tee utility copies standard input to standard output, making a copy in zero or more files. The output is unbuffered.

```
cat or tac <fileName> | tee <newFile>
```

### `sort`

The sort utility sorts text and binary files by lines. A line is a record separated from the subsequent record by a newline (default) or NUL '\0' character (-z option). A record can contain any printable or unprintable characters. Comparisons are based on one or more sort keys extracted from each line of input, and are performed lexicographically, according to the current locale's collating rules and the specified command-line options that can tune the actual sorting behavior. By default, if keys are not given, sort uses entire lines for comparison.

### `awk`

Awk scans each input file for lines that match any of a set of patterns specified literally in prog or in one or more files specified as -f progfile. With each pattern there can be an associated action that will be performed when a line of a file matches the pattern. Each line is matched against the pattern portion of every pattern-action statement; the associated action is performed for each matched pattern. The file name - means the standard input. Any file of the form var=value is treated as an assignment, not a filename, and is executed at the time it would have been opened if it were a filename. The option -v followed by var=value is an assignment to be done before prog is executed; any number of -v options may be present. The -F fs option defines the input field separator to be the regular expression fs.

The basic format of an awk command looks like this:
```
awk 'pattern {action}' input-file > output-file
```
This means: take each line of the input file; if the line contains the pattern apply the action to the line and write the resulting line to the output-file.