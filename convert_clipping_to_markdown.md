# Convert kindle clippings to markdown

From time to time I copy the clippings file from my two kindles (one scribe and an ordinary one). To utilize them, I thought it would be a good idea to join them, remove duplicate entries, and convert them into the markdown format.

This calls for a little go program. While checking the format I stumbled over a quote from the "Software Engineering at Google" book.

*“If you’re writing it from scratch, you’re doing it wrong!”*

OK, no go program. I already googled in the past for tools which can processes these clippings files. I could not find one which seems to do what I want. But starting from scratch is also not the best idea. So why not use `awk`. This is a scripting language especially created for text processing. It is named after its creators `Aho`, `Weinberger` and `Kernighan`. 

A clipping file (from a German kindle) looks like this:

```
Software Engineering at Google (Titus Winters)
- Ihre Markierung auf Seite 39 | bei Position 595-597 | Hinzugefügt am Dienstag, 7. Juni 2022 22:33:20

This is reminiscent of Jevons Paradox: consumption of a resource may increase as a response to greater efficiency in its use.
==========
Software Engineering at Google (Titus Winters)
- Ihre Markierung auf Seite 44 | bei Position 660-661 | Hinzugefügt am Freitag, 10. Juni 2022 12:23:39

Hyrum’s Law: with a sufficient number of users of an API, it does not matter what you promise in the contract: all observable behaviors of your system will be depended on by somebody.
==========
Software Engineering at Google (Titus Winters)
- Deine Markierung auf Seite 248 | bei Position 3793-3794 | Hinzugefügt am Sonntag, 10. September 2023 14:13:27

“If you’re writing it from scratch, you’re doing it wrong!”
==========
```

The different entries are separated with `==========`.

Each entry contains 4 lines:

1. The name and the author of the book.
2. Some details, separated by a dash: `|`.
3. A blank line.
4. The actual quote, on a single line.

The good thing is, it is a human readable text based format. The bad thing is, that it is hard to process further because each entry is spread across multiple lines. This little script converts the content into a `CSV` with the columns book title, page number and quote.

```awk
# This block is called only once.
BEGIN {
  # This is the record separator:
  RS="==========\r\n";
  # This is the field separator:
  FS="\r\n";
  # The separator for the out file:
  OFS=";";
}
# This block is called for each entry.
# $1 to $4 are the differnt "columns".
{
  # Only process entries which are quotes. Ignore e.g. bookmarks.
  if(index($2, "Markierung")) {
    # Split the line which contains the page number at '|'.
    split($2, parts, "|")
    pageString = parts[1]

    # Split now the page number string a ' ' to extract only the number.
    split(pageString, parts, " ")
    pageNumber = parts[6]

    # Print the book name; page number; quote
    print $1,pageNumber,$4
  }
}
```

`RS` specifies the record separator. Here the line ending is important. At first I was not aware that the clipping file uses `\r\n`. You can verify it using this command:

```bash
$ file "My\ Clippings.txt"
My Clippings.txt: Unicode text, UTF-8 (with BOM) text, with very long lines (1884), with CRLF line terminators
```

Having the `CSV`as input we can use another `awk` script to create a markdown file.

```awk
BEGIN {
  FS=";";
  # This variable stores the last book title.
  # So we can print it only once.
  title="";
}
{
  # Print the book title only if it had changed.
  if (title != $1) {
    title = $1
    # There is a new title. Print it as the header.
    printf "# %s\n\n", title
  }

  # Print the quote in italic.
  printf "*%s*\n\n", $3
}
```

Now we can do some magic with pipes:

```bash
cat /path_to_your_clippings/* | # Concat all files
awk -f clippings2csv.awk | # Execute the awk script to create the CSV
sort -t ';' -k 1,2n | # Sort the lines
uniq | # Remove duplicates
awk -f csv2markdown.awk > clippings2.md # Create the markdow and save to a file.
```

Please note that it is important to call `sort` before `uniq`, because `uniq` only removes subsequent duplicates. I sort for the book title first following by the page number. This produces a markdown file where each book title is a heading, following by the different quotes.

Maybe these scripts are still are still a bit wrong or clumsy, but it was quite fast to achieve my goal. Much faster than writing it in `go`.