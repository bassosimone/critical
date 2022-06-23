[![GoDoc](https://img.shields.io/static/v1?label=godoc&message=reference&color=blue)](https://pkg.go.dev/github.com/skx/critical)
[![Go Report Card](https://goreportcard.com/badge/github.com/skx/critical)](https://goreportcard.com/report/github.com/skx/critical)
[![license](https://img.shields.io/github/license/skx/critical.svg)](https://github.com/skx/critical/blob/master/LICENSE)


# criTiCaL - A TCL interpreter written in golang

After re-reading [TCL the Misunderstood](http://antirez.com/articoli/tclmisunderstood.html), I decided to create a simple TCL evaluator of my own.  This project is the result, and it has feature-parity with the two existing "small TCL" projects, written in C, which I examined:

* [picol](http://oldblog.antirez.com/page/picol.html)
  - By @antirez.
* [partcl](https://zserge.com/posts/tcl-interpreter/)
  - By @zserge.

There is a simple introduction to this project, and TCL syntax, on my blog here:

* [Writing a simple TCL interpreter in golang](https://blog.steve.fi/writing_a_simple_tcl_interpreter_in_golang.html)


> The name of this project was generated by looking for words containing the letters "T", "C", and "L", in order.  I almost chose `arTiCLe`, `TreaCLe`, `myThiCaL`, or `mysTiCaL`.
> Perhaps somebody else can write their own version of this project with one of those names!



## Building & Usage

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

The interpreter contains an embedded "standard-library", which you can view at [stdlib/stdlib.tcl](stdlib/stdlib.tcl), which is loaded along with any file that you specify.

To disable the use of the standard library run:

```sh
   $ ./critical -no-stdlib path/to/file.tcl
```



## Examples

The following is a simple example program which shows what the code here looks like:


```tcl
//
// Fibonacci sequence, written in the naive/recursive fashion.
//
proc fib {x} {
    if { <= $x 1 } {
        return 1
    } else {
        return [expr [fib [expr $x - 1]] + [fib [expr $x - 2]]]
    }
}


//
// Lets run this in a loop
//
set i 0
set max 20

while {<= $i $max } {
   puts "Fib $i is [fib $i]"
   incr i
}

```

Another example is the test-code which @antirez posted with his [picol writeup](http://oldblog.antirez.com/page/picol.html) which looks like this:

```tcl
proc square {x} {
    * $x $x
}

set a 1
while {<= $a 10} {
    if {== $a 5} {
        puts {Missing five!}
        set a [+ $a 1]
        continue
    }
    puts "I can compute that $a*$a = [square $a]"
    set a [+ $a 1]
}
```

This example is contained within this repository as [picol.tcl](picol.tcl), so you can run it directly:

```sh
go build . && ./critical ./picol.tcl
I can compute that 1*1 = 1
I can compute that 2*2 = 4
..
```


## Available Commands

The following commands are available, and work as you'd expect:

* `append`, `break`, `continue`, `decr`, `eval`, `exit`, `expr`, `for`, `if`, `incr`, `proc`, `puts`, `regexp`, `return`, `set`, `while`.

The complete list of standard [TCL commands](https://www.tcl.tk/man/tcl/TclCmd/contents.html) will almost certainly never be implemented, but pull-request to add omissions you need will be applied with thanks.



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



## Missing Features

The biggest missing feature is the complete absence of support for lists of any kind.  This is common in the more minimal-TCL interpreters I examined.

The other obvious missing feature is support for the `upvalue` command, which means we're always a little at risk of scope-related issues.

Adding `upvalue` would be possible, but adding list-processing would be more work than I'd prefer to carry out at this time.



## Testing

Our code has near 100% test-coverage, which you can exercise via the standard golang facilities:

```sh
$ go test ./...
```

There are also fuzz-based testers supplied for the [lexer](lexer/) and [parser](parser/) packages, to run these run one of the following two sets of commands:

```sh
cd parser
go test -fuzztime=300s -parallel=1 -fuzz=FuzzParser -v
```

```sh
cd lexer
go test -fuzztime=300s -parallel=1 -fuzz=FuzzLexer -v

```


## Bugs?

Please feel free to open a new issue **with your example** included so I can see how to fix it.


Steve
