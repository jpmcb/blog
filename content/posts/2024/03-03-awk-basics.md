---
title: "Awk: A beginners guide for humans"
date: 2024-03-03T09:00:00-00:00
---

Earlier this week, I had a file of names, each delimited by a newline:

```text
john
jack
jill
```

But really, I needed this file to be in the form:

```text
{
    "full_name": "name"
},
```

This file wasn't absolutely huge, but it was big enough that editing it manually
would have been annoying. I thought to myself, "instead of editing this file manually
or generating it correctly, how can I spend the maximum amount of time
using a bespoke tool to get it in the right format?
A neovim macro? Sed? Write some python? Why not awk!"

In the end, here's the awk command I used:

```bash
awk '{print "{\n    \"full_name\": \"" $0 "\"\n},"}' names.txt
```

This printed each line surrounded by the appropriate curly braces and whitespace.

---

Let's break down how I did this and build the command one bit at a time:

1. Awk is a Linux command line utility just like any other.
But, similar to something like like python or lua,
it's a special program interpreter that is especially
good at scanning and processing inputs with small (or big) one liner programs you give it.

```bash
awk '<an-awk-program>' some-input-file
```

2. Let's start simple and just print the names from the file directly to stdout:

```bash
awk '{print $0}' names.txt
```

```text
john
jack
jill
```

within the `''`, we provide awk with a small program it will execute.
This is basically the "hello world" of awk: it just takes each line and prints it out
just like it is, unedited, in the file.

But what is `$0`?
Awk has the concept of "columns" in a file: these are typically space delimited.
So a file like:

```text
1 2 3
4 5 6 
```

has 3 columns and 2 rows.
The `$0` variable is a special one and represents the entire row of arguments.
Then, each `$N` is the N-th (where 1 is the first column) argument in that row.

So, if we only wanted the 1st column in the above file with 3 columns,
we could run the following awk program:

```bash
awk '{print $1}' numbers.txt
```

```txt
1
4
```

If we only wanted the 2nd and 3rd columns, we could run:

```bash
awk '{print $2 " " $3}' numbers.txt
```

```text
2 3
5 6
```

(notice the blank `" "` we provide as a string to force some whitespace formatting
so the columns are closer to what exists in the original file.

3. Next, lets add in some additional text to print out:

```bash
awk '{print "{\"full_name\": \"" $0 "\"},"}' names.txt
```

First thing you'll notice is a confusing array of `"`
- the first `"` denotes the beginning of a string output for awk to print.
The subsequent `\"` are literal escaped quotes which we _want_ to appear in the output.
We eventually end the first string with a standalone `"` to then print the line with the `$0` variable
and then we enter a string again to add the trailing bracket `}` and comma `,`

When run, this outputs:


```text
{"full_name": "john"},
{"full_name": "jack"},
{"full_name": "jill"},
```

4. Now we're getting somewhere! Let's finish this off by adding the additional white spacing:

```bash
awk '{print "{\n    \"full_name\": \"" $0 "\"\n},"}' names.txt
```

```text
{
    "full_name": "john"
},
{
    "full_name": "jack"
},
{
    "full_name": "jill"
},
```

The added whitespace within the strings (by including the literal escaped newlines `\n`)
are printed to give the correct, desired output!

5. Bonus: what if we wanted to remove the trailing comma?
What if we wanted to wrap this all in `[...]` to be closer to valid json?
Yeah, yeah, I know, `jq` exists, but by the power of our lord and savior awk,
all things possible!!

To remove the trailing comma, we can use a sliding window technique:

```bash
awk 'NR > 1 {print prev ","} {prev = "{\n    \"full_name\": \"" $0 "\"\n}"} END {print prev}' names.txt
```

This introduces abit more complexity.

First, we add the `NR` concept: `NR` is the "number of records".
This can be really useful for checking progress,
doing different things based on number of records processed, etc.

So, after the first record, we print the comma.
We also always store the "previous" chunk in a `prev` variable:
this is the N + 1 sliding window. Nothing actually happens when the first record is processed,
it's line output is simply stored in the `prev` variable to be printed on the next iteration.
This way, we're always one behind the current record
and when we reach the very end (using the `END` keyword),
we can print the previous chunk without the trailing comma!

To wrap it up the entire output in a square bracket and give it the correct spacing,
we can use this awk program:

```awk
BEGIN {
    # Print the opening bracket for the JSON array
    print "["
}

NR > 1 {
    # after the first line, print the previously stored chunk
    print prev ","
}

{
    # Store the current line in a JSON object format
    prev = "    {\n        \"full_name\": \"" $0 "\"\n    }"
}

END {
    # Print the last line stored in prev and close the JSON array
    print prev "\n]"
}
```

We can run this awk program via a file instead of doing all of that on the command line directly.
This greatly helps with readability, maintainability, etc.

```bash
awk -f format_names.awk names.txt
```

```text
[
    {
        "full_name": "john"
    },
    {
        "full_name": "jack"
    },
    {
        "full_name": "jill"
    }
]
```

Just like the previous awk program, we are printing each segment and then at the end,
leaving off the trailing comma. But this time, at the beginning of the program,
using `BEGIN` and `END`, we print an opening and closing bracket.

---

Happy awk-ing and good luck!

---

{{< comments >}}
