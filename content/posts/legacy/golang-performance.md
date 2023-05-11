---
title: "Leaky Go Channels"
date: 2020-03-30
draft: false
---

These simple go tests check the "leaky-ness" of using channels in Go.
There are two methods described here; one using both a local context, and the parent context.
When tests are run against both, the `LeakyAsync` method runs faster, but fails the leak checker
as goroutines are not resolved or cleaned up.

In a production system with possibly thousands of go routines being spun up,
this could result in massive memory leaks and a deadlock situation in the go binary.

it's recommended to use the `leakchecker` library to determine if goroutines get cleaned up.

```golang
package perform

import (
	"context"
	"math"
)

func Selecting(parent chan struct{}) {
	i := 0
	for {
		// Selecting within the infinite loop provides
		// control from the parent chan.
		// If the parent closes, we we can exit the loop and do any cleanup
		select {
		case <-parent:
			return
		default:
		}

		i++

		if i == math.MaxInt32 {
			// Simulate an error that exits the process loop
			break
		}
	}
}

func LeakyAsync(parent chan struct{}) {
	// Start a go routine to read and block off the parent chan.
	// If the parent chan closes, we can clean up within the go routine
	// without having to perform a "select" on each iteration
	// However, this go routine will never be garbage collected
	// if the parent chan does not close and any subsequent cleanup will
	// be left to leak
	go func(c <-chan struct{}) {
		<-c
	}(parent)

	i := 0
	for {
		i++

		if i == math.MaxInt32 {
			// Simulate an error that exits the process loop
			break
		}
	}
}

func ContextAsync(parentCtx context.Context) {
	// Generate a child context from a passed in parent context.
	// If the parent is closed or canceled,
	// the child will also be closed.
	// We can then safely start a go routine that will block on the
	// child's Done channel yet will still continue if the parent is canceled.
	ctx, cancel := context.WithCancel(parentCtx)
	defer cancel()

	go func(ctx context.Context) {
		<-ctx.Done()
	}(ctx)

	i := 0
	for {
		i++

		if i == math.MaxInt32 {
			// Simulate an error that exits the process loop
			break
		}
	}
}

```

```golang
package perform

import (
	"context"
	"testing"

	"github.com/fortytw2/leaktest"
)

func TestSelecting(t *testing.T) {
	leakChecker := leaktest.Check(t)

	c := make(chan struct{}, 1)
	Selecting(c)

	leakChecker()
	c <- struct{}{}
}

func BenchmarkSelecting(b *testing.B) {
	for n := 0; n < b.N; n++ {
		c := make(chan struct{})
		Selecting(c)
	}
}

func TestLeakyAsync(t *testing.T) {
	leakChecker := leaktest.Check(t)

	c := make(chan struct{}, 1)
	LeakyAsync(c)

	leakChecker()
	c <- struct{}{}
}

func BenchmarkLeakyAsync(b *testing.B) {
	for n := 0; n < b.N; n++ {
		c := make(chan struct{})
		LeakyAsync(c)
	}
}

func TestContextAsync(t *testing.T) {
	leakChecker := leaktest.Check(t)

	ctx, cancel := context.WithCancel(context.Background())
	ContextAsync(ctx)

	leakChecker()
	cancel()
}

func BenchmarkContextAsync(b *testing.B) {
	for n := 0; n < b.N; n++ {
		ctx, _ := context.WithCancel(context.Background())
		ContextAsync(ctx)
	}
}

```

Run the test suite with the leakchecker library
```
❯ go test -v
=== RUN   TestSelecting
done checking leak
--- PASS: TestSelecting (11.30s)
=== RUN   TestLeakyAsync
    TestLeakyAsync: leaktest.go:132: leaktest: timed out checking goroutines
    TestLeakyAsync: leaktest.go:150: leaktest: leaked goroutine: goroutine 25 [chan receive]:
        perform.LeakyAsync.func1(0xc00008c1e0)
        	/Users/jmcbride/workspace/channels-testing/perform.go:37 +0x34
        created by perform.LeakyAsync
        	/Users/jmcbride/workspace/channels-testing/perform.go:36 +0x3f
--- FAIL: TestLeakyAsync (5.57s)
=== RUN   TestContextAsync
--- PASS: TestContextAsync (0.57s)
```

Run the benchmarks with `bench` and `benchmem` to see performance
```
❯ go test -v -bench=.  -benchmem -run "Bench*"
goos: darwin
goarch: amd64
pkg: perform
BenchmarkSelecting
BenchmarkSelecting-8      	       1	10114375732 ns/op	     104 B/op	       2 allocs/op
BenchmarkLeakyAsync
BenchmarkLeakyAsync-8     	       2	 585489776 ns/op	     704 B/op	       3 allocs/op
BenchmarkContextAsync
BenchmarkContextAsync-8   	       2	 570398894 ns/op	     976 B/op	       9 allocs/op
PASS
ok  	perform	13.655s
```

`LeakyAsync` is roughly 2 times faster. But fails the leak checker test as the goroutine is not resolved.

`Selecting` is slow because it performs a `select` on _every_ iteration of the for loop.

`ContextAsync` is the best of both worlds. We don't have to do a select within the `for` loop, yet we avoid a go routine
leak.

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
