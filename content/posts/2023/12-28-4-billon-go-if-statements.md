---
title: "4 billion Go if statements"
date: 2023-12-28T09:00:00-00:00
---

I recently read [this _excellent_ little bit of programming horror](https://andreasjhkarlsson.github.io/jekyll/update/2023/12/27/4-billion-if-statements.html)
titled: _"4 billion if statements"_.

It chronicles how one could use an _insane_ number of hard coded if statements
to check if any given 32 bit number is even or odd. Instead of do this the normal
and efficient way with a modulus operator and `for` loop,
hard coding if statements requires some clever meta programming,
some custom assembly code, and a nearly 40 GB compiled binary for all the comparisons.

This all got me thinking:
_"Could you do this in Go? What sort of limitations are there with the Go compiler?"_

Much like the original, I started with a very simple Go program and 10 `if` comparisons:

```go
import (
        "fmt"
        "os"
        "strconv"
)

func main() {
        arg := os.Args[1]
        if arg == "" {
                panic("argument must be provided")
        }

        num, err := strconv.ParseUint(arg, 10, 64)
        if err != nil {
                panic("could not parse argument as int64")
        }

        if num == 1 {
                println(fmt.Sprintf("%d is odd", num))
        }
        if num == 2 {
                println(fmt.Sprintf("%d is even", num))
        }
        if num == 3 {
                println(fmt.Sprintf("%d is odd", num))
        }

        // etc. etc.
}
```

Pretty simple! It gets the argument to the program, parses it as an `uint64` integer,
and then goes through all the comparisons one by one.

And it works flawlessly:

```text
$ go run main.go 8
8 is even
```

In order to extend this _beyond_ what I am humanly capable of doing by hand and what I want to spend the rest of my life doing,
(if I was to write out each `if` statement by hand, and it took me 1/2 a second each time,
in order to write out all 32 bit numbers,
it would take me roughly 292471207.5 millennium to complete)
we should also take advantage of some meta programming. Let the computers do the boring stuff quickly!

Here's a simple bash script I came up with to drop in some Go code
for us to try and compile:

```bash
#!/usr/bin/env bash

# The initial boilerplate for the Go program in a heredoc
cat << EOF > main.go
package main

import (
        "fmt"
        "os"
        "strconv"
)

func main() {
        arg := os.Args[1]
        if arg == "" {
                panic("number argument must be provided")
        }

        num, err := strconv.ParseInt(arg, 10, 64)
        if err != nil {
                panic("could not parse argument as int64")
        }
EOF

# A few variables to control the meta programming flow
END=1000
ISEVEN=false

# Loop through all values, flipping a flag back and forth (since we're not
# using the modulus operator to make even/odd comparisons)
for ((i=1; i<=END; i++)); do
    if [[ $ISEVEN = true ]]; then
        cat << EOF >> main.go
        if num == $i {
                println(fmt.Sprintf("%d is even", num))
        }
EOF
        ISEVEN=false
    else
        cat << EOF >> main.go
        if num == $i {
                println(fmt.Sprintf("%d is odd", num))
        }
EOF
        ISEVEN=true
    fi
done

# Close out the main go program
echo "}" >> main.go
```

This uses one of my favorite bash features, the "heredoc", in order to drop
large string chunks (in this case, Go code) into a file.
Note that this only goes up to 1000 if statements _(for now ...)_: we'll slowly increase the number of if statements
to see if we can hit any kind of ceiling or limitation.

After running the bash script, let's build the generated, meta go code:

```bash
CGO_ENABLED=0 go build -gcflags="-N" -a main.go
```

This instructs the Go toolchain (which includes a `gc` compiler) to do the following:
- Disable `gc` optimizations with `-N`: we don't want the underlying compiler to make
  any changes to our meta code through compiler trickery.
- Disable `cgo` from require a locally linkable C toolchain: I.e., this builds a sole, statically linked binary.
- Always build the program, no matter if it's already been built with the `-a` flag

This produces a single `main` binary that we can run some tests against:

```text
$ ./main 500
500 is even

$ ./main 677
677 is odd
```

Great! Things seem to be working!!

Now, since we know 1000 if statements works, let's try to scale this up abit:
like the original post, let's go up to 16 bit integers
(which should be 65536 `if` statements):

```bash
((END=2**16))
```

This was _abit_ slower and took just under 2 mins:

```text
$time ./meta.bash
./meta.bash  18.39s user 65.09s system 78% cpu 1:45.79 total
```

and resulted in a `main.go` file that is over 100,000 lines of code and `5.6M` big.

After building, let's test it out:

```text
$ ./main 65536
65536 is even

$ ./main 6553
6553 is odd

$ ./main 32322
32322 is even
```

Excellent! Now, onward to the 32 bit integer holy grail and over 4 billion if statements!!

```bash
((END=2**32))
```

At first, I let this ignorantly run _all night_ only to come back and find that I had let my bash script
consume all available disc on my Macbook (it only has a 500 GB internal drive): by my estimates, the way I wrote the meta Go code boilerplate,
a single file would be over 300 GB in size. Things crashed around 1 billion `if` statements (since it couldn't write to disc anymore)
and I was left with nothing to do but delete the file and reclaim the disc space.

_There must be a better way!!_

Let's try using external storage: I had a spare 1TB external SSD laying around that I could run this experiment on.
Now, the only thing would be seeing if the read/write speeds on this external drive would be fast enough
resulting in a bottleneck.

Using the bash script, it took just under 10 minutes (conservatively) to write 1 million `if` statements to the drive:
to reach 4 billion, using this as an anchor point, it will take 40,000 minutes.
Or roughly 27 days to complete. _Yikes, the read/write on this old drive is really slow_.
For my internal mac storage to reach 1 million `if` statements, it takes less than 5 seconds.

_Foiled again!!_ I was definitely not anticipating disc space and read/write IO being the biggest hurdle here.

Bash is probably _not a wise choice_ at this point: if I want to make the writing to disc
fast and efficient, I probably need something more robust: like Go!

This Go program is more or less the same as the original bash script,
but, with some major improvements: we are using a buffered writer that lets us
make significantly fewer writes to disc with bigger chunks! This speeds
things up significantly:

```go
package main

import (
        "bufio"
        "fmt"
        "math"
        "os"
)

// The file on my attached "Dark-Star" SSD
const META_FILE = "/Volumes/Dark-Star/4-billion/main.go"

func main() {
        var err error

        // Delete/truncate existing bits within main.go file
        err = os.Truncate(META_FILE, 0)
        if err != nil {
                panic(err)
        }

        // open main.go file for writing
        f, err := os.OpenFile(META_FILE, os.O_WRONLY, os.ModeAppend)
        if err != nil {
                panic(err)
        }

        // close file on exit and check for its returned error
        defer func() {
                if err := f.Close(); err != nil {
                        panic(err)
                }
        }()

        // Use a buffered writer and periodically flush
        w := bufio.NewWriter(f)

        // Initial Go boilerplate
        w.Write([]byte(`
package main

import (
        "os"
        "strconv"
)

func main() {
        arg := os.Args[1]
        if arg == "" {
                panic("number argument must be provided")
        }

        num, err := strconv.ParseInt(arg, 10, 64)
        if err != nil {
                panic("could not parse argument as int64")
        }
`))

        err = w.Flush()
        if err != nil {
                panic(err)
        }

        // Since we're still not using modulous operators,
        // use a few flags for tracking the number of chunks
        // written to the buffered writer and for even/odd
        chunks := 0
        isEven := false

        // Go is nice since it carries constants in the math package
        // for max ints of varying bit width
        for i := 1; i < math.MaxUint32; i += 1 {
                println(i)

                // Every 10000 writes to the buffer, flush to the main.go file
                // Note: this is where the actual write to disc happens
                if chunks > 10000 {
                        err = w.Flush()
                        if err != nil {
                                panic(err)
                        }
                        chunks = 1
                }

                if isEven {
                        // chunk for an even number
                        _, err := fmt.Fprintf(w, string(`
        if num == %d {
                println("%d is even")
        }`), i, i)
                        if err != nil {
                                panic(err)
                        }

                        isEven = false
                } else {
                        // chunk for an odd number
                        _, err := fmt.Fprintf(w, string(`
        if num == %d {
                println("%d is odd")
        }`), i, i)
                        if err != nil {
                                panic(err)
                        }

                        isEven = true
                }

                chunks += 1
        }

        // Write the last closing bracket for the main function
        w.Write([]byte(`
}`))

        // flush out any remaining bits and finish up
        err = w.Flush()
        if err != nil {
                panic(err)
        }
}
```

In total, this took just over 3 hours to write to my external SSD! Much better!!

```text
./main  1722.63s user 4116.48s system 49% cpu 3:16:32.52 total
```

and the `main.go` file on the external SSD ended up being about 350GB:

```text
$ ll main.go
-rwxrwxrwx@ 1 jpmcb  staff   344G Dec 29 15:55 main.go
```

Now, let's compile it!

```text
$ CGO_ENABLED=0 go build -a main.go
command-line-arguments:
/opt/homebrew/Cellar/go/1.21.3/libexec/pkg/tool/darwin_arm64/compile:
signal: killed
```

... about an hour latter, it turns out, I don't have _quite_ enough ram to actually compile this monstrosity.

What's going on here? As the Go compiler (and the underlying `gc` compiler) consume the billions and billions
of lines of Go code, it loads those contexts into memory. I believe this is a similar limitation the original
author ran into when compiling their C code: there's just not enough memory on the system to consume and compile such a massive program.

I considered going down the assembly route:

```asm
cmp w1, 1     ; Compare number in the w1 registry with "1"
b.eg odd      ; Print "odd"

cmp w1, 2     ; Compare number in the w1 registry with "2"
b.eg even     ; Print "even"

; ... and many, many more comparisons
```

but this would:

1. Essentially replicate Andreas Karlsson's original experiment
2. Probably be very tedious to do on a Macbook since _"Darwin function numbers
   are considered private by Apple, and are subject to change."_
   I was able to piece together some of the syscalls through the [XNU bsd kernal syscalls header](https://github.com/apple-oss-distributions/xnu/blob/main/bsd/kern/syscalls.master),
   Apple's OSS distribution of the kernel for MacOS and iOS.
   But again, this seemed be a relatively fraught effort replicating what's already been done on x86.

### Lessons learned:

- Building massive Go projects requires an equally massive amount of RAM.
- External SSD io read/write times _can_ indeed be a scaling issue:
  I had to pivot to a more efficient, chunking strategy when writing the 300+ GB
  file to my external drive.
- Like any massive scale problem, abit of bubble-gum and duct tape is usually required.

---

### Sidebar: bash does not support 64 bit wide ints

At one point during this journey, I thought that _maybe_
I could keep these shenanigans going and scale this all
the way up to 64 bit wide ints.

Besides how absolutely _huge_ the source file would be
(the difference between the max 32 bit int and max 64 bit int
is roughly 4 billion times the size: so we can assume the
source file would be 400 GB * 4 billion == 16 million petabytes).
I found there was a tricky soft limit on ints in bash:

```bash
((NUM=(2**64)))
echo $NUM

# 0
```

```bash
((NUM=(2**64) - 1))
echo $NUM

# -1
# 2^64 truly results in zero, not just overflowed back around to 0
```

```bash
((NUM=(2**63)))
echo $NUM

# -9223372036854775808
# interesting! This seems to overflow
```

```bash
((NUM=(2**63) - 1))
echo $NUM

# 9223372036854775807
# This seems to be the upper limit of bit width ints in bash
```

---

{{< comments >}}
