tn stands for 'tiny'. It is such an experimental programming language.


### Syntax
```
file: *def
def: defHeader defBody
defHeader: "+" ?name *defParam
defBody: indent call *(indent def)
indent: "  " | "    " | "\t"
callParam: ?(name ":") object
call: name *(indent callParam)
object: call | def | literal
literal: arr| map | flt | int | str
arr: "[" *(indent object) "]"
map: "{" *(indent map_item) "}"
map_item: name ":" object
flt: +digit "." +digit
int: +digit
str: fmt_str | raw_str
fmt_str: "\"" *any_character "\""
raw_str: "'" *any_character "'"
newline: ";" | "\n"
comment: "#" any_character_except_newline newline

Lexer removes all the comments and 
"<" is indent begin and ">" is indent end

+main
    sum
        2
        5
    +.
        sum<2 5>

+same<sum<2 5> +.<sum<2 5>>>  # minified version
Types

UserData = trait {
    "smth": int
    "ok": string
}

+userData input/hash  # hash is an object that decorates Hash trait
    seq
        result = hash
        foreach
            input
            + elem
                result.set<elem.key elem.value>
        result  # result basically is a copy of input

    . <UserData>

// Type system is implicitly static (automatic type inference)
// that is traits of object parameters are infered at compile time

// example
+sumOfTwoNumbers a b
    seq
        result = sum<a b>  # a and b must satisfy sum parameters traits
        # that is compiler from this line knows that a and b must have
        # at least everythings that sum needs a and b to have
        a.sayHello
        # now compiler adds sayHello to trait of that 'a' parameter

literals
["arrayelem1"; "arrayelem2"; "arrayelem3";]
[
    "arrayelem1"
    "arrayelem2"
    "arrayelem3"
]
{
    "hashKey1": "hashKey1Value"
    "andSoOn": "..."
}

1.0
0.1
5
"fmt string #{var}"
'multiline raw
string'

# Traits are capitalized (always)
PlayerWSClientData = trait {
    "this": AnotherTrait
    "hello": SomeOtherTrait
    "anotherOne": AndSoOn
}

# concurrency
+incrementAndPrint var/AtomicMemory
    seq
        var.write<sum<var 1>>
        print<var>

+main
    seq
        m = atomicMemory 3 
        simult
            incrementAndPrint<m>
            incrementAndPrint<m>

# output might be like this:
4
5

# or like this:
5
4


# hello world 10 times
+main
    seq
        foreach
            range<10>
            + i
                print<"Hello World! #" i>

# just repeat
+main<repeat<10 print<"Hello World!">>

# ident goes after any ident

# fizz buzz

+main
    foreach
        range<5 15 2>
        + i
            if
                mod<i 2>.eq<0>.and
                    mod<i 3>.eq<0>
                print<'Fizz buzz ' i>

main.ice
+arraySum ...arr
    seq
        sumValue = memory  # same as +sumValue<+.<memory>>
        foreach
            arr
            + i
                sumValue.write
                    sum
                        sumValue
                        i
        sumValue

+arrayOfIntsFromStdin
    seq
        a = array
        while
            not<stdin.isAtEOF>
            arr.pushBack<int<stdin.skip<' '>.readUntil<' '>>>
        a

+main
    print
        arraySum
            arrayOfIntsFromStdin


# HWS 'html over websockets' (icepl web dev lib)
- state management on the server
- virtual DOM on the server
- when DOM node changed then changed are transfered to client
- client can query/mutate server (also make API calls)

button example

+imports
    [
        'hws'
    ]

+myButton ...children
    +.
        hws.button<...children>
    +class
        "p-2 m-4 hover:bg-slate-200"
    +onClick
        + event
            print<'Clicked'>

# state management would be like this
hws.state.value<'buttonText'>
hws.state.set<'buttonText' 'hello'>

+main
    seq
        server = hws.server
        server.addRoute<'/' 'GET' root>
        server.run<5000>

+root  # might receive smth like req,res
    myContainer  # like a jsx but not a DSL (but native language syntax)
        myButton1
        myButton2
        some
            else
            else2
    +.<hws.div>
    +class<'p-2 border border-slate-200 flex flex-col gap-3'>

# it is possible to subscribe to state mutation
hws.state.onSet<callback>
hws.state.onSet<'specificKey' callback>
```

