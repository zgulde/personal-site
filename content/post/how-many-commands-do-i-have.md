+++
date = "2016-08-19T21:24:28-05:00"
draft = false
title = "How many commands do I have?"
+++

# How many different commands can I run?

tl;dr

```bash
echo $PATH | tr ':' '\n' | sort | uniq | xargs ls -1 |\
grep -ve '^$' -e '^/' | sort | uniq | wc -l
```

What commands can I execute? What commands can I autocomplete with <tab>? How do I even
Linux?

Let's find out by exploring some common linux command line tools and IO
redirection!

#### Assumptions

- You are mildly comfortable with the terminal.

## The `$PATH` environment variable

There is a special [environment
variable](https://en.wikipedia.org/wiki/Environment_variable) called `PATH`
that, simply put, contains the list of directories that your shell will search
in for commands, sepated by `:`s. 

For example, if I have a command named `ls`, in order to run it, to location of
the `ls` command must be inside of a directory in my `$PATH`. The `which`
command will tell us where a command is located (or nothing if the command
doesn't exist), so we can use that to find the location of `ls`.

```bash
which ls # outputs /bin/ls
```

To see the contents of an environment variable we can `echo` it, and sure
enough if we echo our `$PATH`, we will see `/bin` as one of the directories.

```bash
echo $PATH
```

That output is a little hard to process, however, let's clean it up!

## Piping

Piping is a core concep in unix. Basically, we can take the output of one
command and give it as the input to another through the `|` operator.

```bash
echo $PATH | tr ':' '\n'
```

That is what we are doing with the translate, or `tr` command. The `tr` command
will translate a given string to something else. Here we are using it to
translate the `:`s to newlines, `\n`, so that each directory will be displayed
on a separate line.

The final output of our command at this point is each directory in our `PATH`
on a newline.

```bash
echo $PATH | tr ':' '\n' | sort | uniq
```

Next we will take our output and give it to `sort`, which, as the name implies,
sorts our list of directories. This in combination with the `uniq` command will
remove any duplicates we might have.

At this point we will have a list of sorted, non-duplicated, directories
separated by newlines.

## `xargs`

`xargs` will execute a command with whatever arguments are sent to it
(actually, it's [a little more nuanced than
that](https://en.wikipedia.org/wiki/Xargs), but that's how we can think of it
for now). Lets look at a simple example:

```bash
echo -e 'one\ntwo\nthree'
```

This will output

```
one
two
three
```

If we pipe this to `xargs`, we can execute a command with whichever parameters
we pipe to `xargs`

```bash
# the -e flag allows echo to process special characters so that we will
# actually get a newline instead of a literal `\n`
echo -e 'one\ntwo\nthree' | xargs echo 'number: '
```

will print out 'number: ' plus whatever we pipe to it, in this case
'one,two,three', so our output will be

```
number: one two three
```

We can use this with `ls` as well.

```bash
echo -e 'dir_one\ndir_two' | xargs ls
```

This would run the `ls` command with `dir_one` and `dir_two` as arguments.

So the next step in our command 

```bash
# the -1 flag for ls will print the output in one column
echo $PATH | tr ':' '\n' | sort | uniq | xargs ls -1
```

will give us a list of all the files inside of each directory in our path,
along with some blank lines and the name of each directory before the list of
files in it.

## Let's continue.. onto a newline

The next step continues our command onto a newline:

```bash
\
dosomething
```

The escaping backslash immediately precedes the newline character, and this
tells our shell to ignore the newline and continue processing the command.

So this:

```bash
echo $PATH | tr ':' '\n' | sort | uniq | xargs ls -1 |\
```

just says that our command continues on the next line.

This is useful when we have longer commands to make them more readable, or
when writing shell scripts.

## grep

[Grep](https://www.gnu.org/software/grep/manual/grep.html) is a very powerful
tool that allows us to search through text for patterns. Grep can be used to
search for a pattern in a file, recursively through all files in a directory
and subdirectories, or even search for patterns in whatever is piped to it.

Here's a simple example

```bash
# the echo command will output
# > one
# > two
# > three
# each on a newline
echo -e 'one\ntwo\nthree' | grep 'two'
```

Will give us the output of just `two`.

If we search for something that is common to more than one line of the output
we will see all lines that match

```bash
echo -e 'one\ntwo\nthree' | grep 'o'
# will output
# > one
# > two
```

We can also use grep to show us lines that do *not* match a pattern.

```bash
# the -v flag will only output lines that do not match a pattern

echo -e 'one\ntwo\nthree' | grep -v 'o'
# will output
# > three

echo -e 'one\ntwo\nthree' | grep -v 'two'
# will output 
# > one
# > three
```

If we looked at our output from our command in it's current state, we will
notice that it is not just a list of files, but also some directory names and
blank lines. We want to get rid of those lines, so we will use grep to search
for those lines and only show lines that do not match to give us just a list of
files.

Let's take a look at the relevant part of the command

```bash
grep -v -e '^$' -e '^/'
```

We know the `-v` flag only gives us lines that do *not* match the pattern we
are searching for, and the `-e` flag lets us specify more than one pattern to
seach for. We can write this a little shorter by combining the first two flags.

```bash
grep -ve '^$' -e '^/'
```

Most unix command line tools let us combine the single letter flags together.
You've probably seen an example of this with `ls`.

```bash
ls -l -a -h
# same as 
ls -lah
```

The expressions inside the single quotes after each `-e` flag are [Regular
Expressions](https://en.wikipedia.org/wiki/Regular_expression). The focus of
this post is not explaining how to use regular expressions, but I will explain
what these two do.

`^$` looks for an empty line, and `^/` looks for a line that starts with a `/`.
By telling grep we only want to see lines that do *not* match those patterns,
we can effectively filter those lines out of our input.

```bash
echo $PATH | tr ':' '\n' | sort | uniq | xargs ls -1 |\
grep -ve '^$' -e '^/' 
```

At this point we will have just a list of files in our `$PATH`.

## Wrapping up

Next we'll add on another `sort | uniq` to remove any duplicate files and then
pass along that list to `wc`.

```bash
echo $PATH | tr ':' '\n' | sort | uniq | xargs ls -1 |\
grep -ve '^$' -e '^/' | sort | uniq | wc -l
```

`wc` is a nice little tool that lets us see the
number of words, lines, characters, or bytes in a file (or input piped to it).
By specifying the `-l` flag we are saying we only want the number of lines.
Since each file is on a separate line, this will give us the number of commands
that are in out `$PATH`!
