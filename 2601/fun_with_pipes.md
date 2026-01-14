I had the task to analyze a performance value from log files located in
multiple zip archives like logs1.zip and logs2.zip. The log lines I were
interested in looks like this:

```
000038 ms   Some operation
```

This tells me, that the operation took 38ms.

`ugrep` is a great tool to search in one ore multiple archives. The following operation searches for a specific string inside compressed
archives (`-z`) in the current directory.

```bash
ug -z "Some operation"
```

This returns something like this:

```
...
{LogFolder\LogFile.txt.txt}:461463:      000030 ms   Some operation
{LogFolder\LogFile.txt.txt}:465224:      000012 ms   Some operation
{LogFolder\LogFile.txt.txt}:465452:      000019 ms   Some operation
{LogFolder\LogFile.txt.txt}:465482:      000024 ms   Some operation
{LogFolder\LogFile.txt.txt}:465512:      000029 ms   Some operation
{LogFolder\LogFile.txt.txt}:465693:      000018 ms   Some operation
{LogFolder\LogFile.txt.txt}:495161:      000038 ms   Some operation
{LogFolder\LogFile.txt.txt}:502826:      000022 ms   Some operation
{LogFolder\LogFile.txt.txt}:503902:      000008 ms   Some operation
{LogFolder\LogFile.txt.txt}:504038:      000025 ms   Some operation
{LogFolder\LogFile.txt.txt}:504168:      000020 ms   Some operation
...
```

This result can be piped to the "ordinary" grep to extract only the numbers:

```bash
ug -z "Some operation" | grep -oP '\d+(?=\s+ms)'
```

The output will look like this:

```
...
000048
000019
000025
000025
000037
000077
000021
000023
000033
000029
000047
000092
000024
...
```

In the grep statement `(?=\s+ms)` is used for a positive lookahead to ensure the number is followed by " ms" (but don’t include " ms" in the match).

Having now all that numbers we can use `datamash` to calculate the percentiles:

```bash
ug -z "Some operation" | grep -oP '\d+(?=\s+ms)' | datamash perc:10 1 perc:50 1 perc:90 1
```

```
4       25      68
```

Now I know that 90% of all operations are equal or faster then 68 ms.

And for sorting the numbers and remove the duplicates:

```bash
ug -z "Some operation" | grep -oP '\d+(?=\s+ms)' | sort -n | uniq
```

```
000002
000003
000004
...
000372
000394
000444
```

All these tools should be available in your favorite Linux distribution. Here
are the links:

- [ugrep](https://github.com/Genivia/ugrep)
- [grep](https://man7.org/linux/man-pages/man1/grep.1.html)
- [datamesh](https://www.gnu.org/software/datamash/)
- [sort](https://man7.org/linux/man-pages/man1/sort.1.html)
- [uniq](https://man7.org/linux/man-pages/man1/uniq.1.html)

**[back](../index.md)**