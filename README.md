[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/skx/critical)
[![Go Report Card](https://goreportcard.com/badge/github.com/skx/critical)](https://goreportcard.com/report/github.com/skx/critical)


# criTiCaL

I had a bit of fun reading [TCL the Misunderstood](http://antirez.com/articoli/tclmisunderstood.html), and hacked up a simple TCL-like evaluator.


## Building/Using

Build in the usual way, for example:

```sh
$ go build .
```

Execute with the name of a TCL-file to execute:

```sh
    $ ./critical input.tcl
    A is set to: 4.000000
    Hello World
    4
    4
    ..
```


## Example

The following is a simple example program which shows what the code here
looks like:

* **NOTE**: Currently we've not implemented `return`, so the output of a procedure is the last value evaluated.  That's why we use `$ret`:

```tcl
//
// Fibonacci sequence, in the naive recursive way
//
proc fib {x} {
    if { expr $x <= 1 } {
        set ret 1
    } else {
        set ret [expr [fib [expr $x - 1]] + [fib [expr $x - 2]]]
    }
    $ret
}


//
// Lets run this in a loop
//
set i 0
set max 20

while { expr $i <= $max } {
   puts "Fib $i is [fib $i]"
   incr i
}

```

## Built In Commands

The following commands are available, and work as you'd expect:

* `decr`, `expr`, `if`, `incr`, `proc`, `puts`, `set`, `while`.

In the near future we'll add `break`, `cd`, `continue`, `return`, & `pwd`.

The complete list if [TCL commands](https://www.tcl.tk/man/tcl/TclCmd/contents.html) will almost certainly never be implemented, but pull-request to add omissions you need will be applied with thanks.



## Features

Read the file [input.tcl](input.tcl) to get a feel for the language, but in-brief you've got the following facilities available:

* Mathematical operations for `expr`
  * `+` `-` `/` `*` `<` `>` `<=` `>=`
  * Integer support only.  Sigh.
* Output to STDOUT via `puts`.
* Inline command expansion.
  * Including inside strings.
* Inline variable expansion.
  * Including inside strings.
* The ability to define procedures, via `proc`.

Badly implemented features:

* This fails as the spaces around `+` are necessary:
  * `puts [expr $a+$b]`
* Inline expansion swallows a character
  * `puts "[expr 3 + 3]ab` shows `3b` - where did `a` go?



## Testing

Most of the internal packages have high test-coverage, which can be exercised as you would expect:

```sh
$ go test ./...
```

In addition to the standard/static testing there are fuzz-based testers for the lexer and parser.  To run these run one of the following two sets of commands:

* **NOTE**: The fuzz-tester requires the use of golang version 1.18 or higher.


```sh
cd parser
go test -fuzztime=300s -parallel=1 -fuzz=FuzzParser -v
```

```sh
cd lexer
go test -fuzztime=300s -parallel=1 -fuzz=FuzzLexer -v

```
## Bugs?

Yeah I'm not surprised.  Please feel free to open a new issue **with your example** included so I can see how to fix it.


Steve
