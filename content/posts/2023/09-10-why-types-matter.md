---
title: "Why types in programming languages matter."
date: 2023-09-10
---

Loosely typed programming languages have been around for a very, very long time.
And they’ve been the center of many problems for about as long.

One of the first modern “typeless” programming languages was Ken Thompson’s B: a
machine independent language who’s primary use was for very early Unix systems
development at the legendary Bell laboratory in 1969. Before Dennis Ritchie
(along with Ken) went on to invent the ground breaking, seminal C programming
language, they both used, designed, and developed B.

B sort of looks like typical C mixed in with modern Go (and knowing that, you
wouldn’t be surprised to find that Ken Thompson years latter had a big hand in
designing Go at Google in the early 2000s).

Let’s take a quick peak at a “Hello, World!” program in B:

```c
main() {
    /* use an external stdlib putchar to print to the screen */
    extern putchar;

    /* declare and assign variables with automatic storage duration */
    auto msg, i;

    msg = "Hello, World!\\n";

    /* iterate each char within the msg
       until 0 (or in more modern terms, null) is reached */
    i = 0;
    while (msg[i] != 0) {
        putchar(msg[i]);
        i = i + 1;
    }
}
```

To say that B is “typeless” isn’t necessarily correct. It has just one type: the
“word”.

For those unfamiliar with lower level systems programming, a word is typically
an abstraction for a processor’s standard memory format. So, on modern 64-bit
systems, a word would be 64 bits wide (or more often just referred to as 8
bytes). Within that word, you can store just about anything: integers, strings,
address pointers, stack references, etc. In the end, it all gets dereferenced as
raw memory and it’s sort of up to the programmer to know what to do with those
bits.

B also came with some nice quality of life features that would latter resurface
in C and Go: the auto keyword automatically allocates and manages memory for the
duration of the scope it’s being held within. Modern operands like `++` and `--`.
Function declaration. And much more.

In Ken Thompson’s own words:

> "B and the old old C were very very similar languages except for all the types [in C]”.

Early on, B compiled down to “threaded code”: essentially a script that called
many other subroutines and system calls. This made sense at the time since it
worked well with the rudimentary operating systems and small bit architectures
Ken was working within. But eventually, as year over year leaps and bounds
continued within Bell labs computer systems, Richie converted the compiler to
produce raw machine code that resulted in safer data typing for variables (see
where we might be going here?).

What’s important to understand is that B was a high level programming language
that initially used an extremely loose type system: accessing a variable usually
meant you would be dereferencing the underlying memory within that word without
any checks to its bounds.

You can imagine the problems this produced: even in our rudimentary “hello
world” program above, there’s a huge assumption that `msg` is indeed a string (and
not a number or function address).

These “type assumptions” by programmers can create some really nasty bugs. A
similar example of these dangerous assumptions is the classic C buffer overflow:

```c
#include <stdio.h>

int main() {
    // A character buffer array of only 10 chars
    char buffer[10];
		
    // A null terminated string array
    // (notice it's abit longer than 10 chars)
    char message[] = "Hello, World!";

    // Copy the message directly into the buffer.
    // Note that there is no effort to check the bounds of the buffer
    // which results in a buffer overflow.
    //
    // C allows for writing past buffer[9] which enters the realm of
    // "undefined behavior": It might work. It might overwrite
    // other variables in the stack. It might crash the running program.
    // Or, depending on the system, it may even cause a hardware
    // exception if some important system memory is overwritten.
    for (int i = 0; i < sizeof(message); i++) {
        buffer[i] = message[i];
    }

    // This is very similar to the loop in B we wrote:
    // Iterate the buffer array until the null terminator is found
    //
    // Again, depending on the system and user permissions running this,
    // we enter the world of "undefined behavior": reading bytes in
    // memory far beyond the original allocation may crash the program
    // or cause an exception to be thrown.
    int j = 0;
    while (buffer[j] != '\\0') {
        putchar(buffer[j]);
        j++;
    }

    return 0;
}
```

This is a classic buffer overflow: a program is accessing and writing memory
that it really shouldn’t. It demonstrates a few things: sensible checks by the
programmer on the “kind” of memory being accessed are ignored which leads to
buggy and undefined behavior. It also shows how a very strong typing system
would prevent something like this: copy from one buffer to another in languages
like Go are relatively safe because of the compiler’s strongly typed interfaces
and boundaries.

Using a modern “loose” or dynamic typing system is a bit different but
replicating similar unintended behavior is not difficult: accessing portions of
memory (or the _**“shapes”**_ of that memory) deep within a language’s systems can
have very unintended consequences.

Let’s look at a JavaScript example now:

```js
function printchars(str) {
    for(let i = 0; i < str.length; i++) {
        console.log(str[i]);
    }
}

// Yikes! This is the wrong type and should have been a string!!!
//
// Thanks to type coercion in js, this will inevitably become "12345"
// and adopt the "length" property and not throw an error.
//
// But the real question remains: 
// what was the intent here? Why did the caller assume they could use
// a number here? What confusion occurred? Or maybe they embraced the
// type coercion and forced the function to print a string 
// of their number. Which in itself is confusing and abit obtuse.
let myData = 12345;

printchars(myData);
```

I anticipate that JavaScript enthusiasts will not appreciate that example. And I
can sympathize! Type coercion can be a very powerful tool and helps to prevent
the types of crashes that usually occur in a more rigid type system. But I hope
you see the danger here: human communication. Not only between co-workers (I
can’t imagine shipping the above code and assuming my co-workers would all
understand why I used a `printchars` function with a number) but also with
yourself (I’ve come back to “clever” solutions in personal projects pretty
confused why I wrote something the way I did).

---

Taking an even bigger step back, throughout history, these sorts of “type”
problems have had catastrophic results.

In 1996, the European Space Agency was slated to launch their new Ariane 5
rocket: a cutting edge engine that would provide heavy lift space launches for
satellites, orbital missions, and much more. Like the Falcon 9 rocket, it was
intended primarily for communication satellite missions. Maybe one of it’s most
famous flights was in December 2021 when it launched the James Webb Space
Telescope and was a huge part in the international effort.

The Ariane 5 originally inherited its software from the Ariane 4 rocket, a
system that had worked for years without problem. But during the first launch,
carrying a payload of satellites, a problem occurred: a 64-bit floating point
number that represented the rocket's horizontal velocity was converted into a
16-bit integer. Since the Ariane 5 had different flight dynamics compared to
it’s younger sibling, the value was much much larger than anticipated and could
not be converted.

This failed conversion resulted in an overflow (sound familiar?), which was not
caught and caused a hardware exception. Unfortunately, the rocket’s flight
software wasn't designed to handle this kind of exception and the fault-tolerant
design of the system switched to a backup system, but that subsequent system
failed in the same way, leading the rocket to go off course. This cascading
effect of failed type conversions continued until the rocket was so off course
that the self-destruct mechanism was triggered within the mechanical failsafes.
This ultimately resulted in a complete loss of the rocket and its satellite
payload.

At the time, some estimates put the cost of this extraordinary failure at over
$300 million USD.

By this point, you probably realize that this is a late, veiled critique of
DHH’s recent declaration that “Turbo 8 is dropping TypeScript” in favor of
regular, vanilla JavaScript:

> TypeScript just gets in the way of that for me. Not just because it requires
> an explicit compile step, but because it pollutes the code with type
> gymnastics that add ever so little joy to my development experience, and
> quite frequently considerable grief. Things that should be easy become hard,
> and things that are hard become any. No thanks!

I’ve personally never heard of a project going from a strongly typed system to a
loosely typed one. But essentially what this introduces is an insurmountable
number of possible bugs and potential catastrophic failures that would be caught
by a typing system.

People (and thought leaders) are all entitled to their own opinion. But when it
gets dangerous is when you ship a library (like Turbo) that is in use by others.
The true tragedy here is not the bad opinions: it’s the rug pull performed in
one fell swoop through a single PR. I encourage everyone using Turbo to think
deeply about the potential side effects this decision might have on your product
and your users: DHH didn’t consider this effect for their users.

Humans are bad at programming. Our brains weren’t really built to think so
rigidly and consider all the possibilities when building systems. Strongly typed
systems are tools. And like any good tool, it helps us go further, safer.

---

If you’re a space nerd like me and interested in the Ariane 5 maiden voyage,
flight V88, I highly recommend this made for TV documentary from the late 90s.
It’s also abit of fascinating TV history and was a great watch while researching
this piece.

{{< youtube DGPwHq8J7_s >}}

{{< youtube 3bujoNtjgTU >}}
