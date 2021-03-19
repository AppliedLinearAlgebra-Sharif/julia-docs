# شروع به کار

نصب جولیا ساده است و می‌توانید این کار را با استفاده از باینری‌های از پیش کامپایل شده و یا با کامپایل کد انجام دهید.با دنبال کردن دستورالعمل های [این صفحه](https://julialang.org/downloads/) جولیا را بارگیری و نصب کنید.

اگر از یکی از زبان های MATLAB، R، Python، C/C++ یا Common Lisp ‌به جولیا می‌آیید، پیشنهاد می‌شود ابتدا بخش
[تفاوت های مهم با این زبان‌ها](https://julia-docs.readthedocs.io/fa/latest/manual/noteworthy-differences.html) را مطالعه کنید. این کار به شما کمک می کند با تفاوت‌های اصلی جولیا با آن زبان‌ها آشنا شوید و از اشتباهات متداول جلوگیری می‌کند.

ساده ترین راه برای یادگیری و آزمایش کار با جولیا با شروع یک جلسه تعاملی (که به عنوان ‍‍`REPL` شناخته می شود) با دوبار کلیک کردن روی فایل اجرایی جولیا یا اجرای `julia` از خط فرمان است:


<div dir="ltr">

```@eval
io = IOBuffer()
Base.banner(io)
banner = String(take!(io))
import Markdown
Markdown.parse("```\n\$ julia\n\n$(banner)\njulia> 1 + 2\n3\n\njulia> ans\n3\n```")
```

</div>

برای خروج از اجرا، از کلید `CTRL-D` استفاده کنید یا دستور `exit()` را اجرا کنید.
When run in interactive mode, `julia` displays a banner and prompts the user for input.
Once the user has entered a complete expression, such as `1 + 2`, and hits enter, the interactive
session evaluates the expression and shows its value. If an expression is entered into an interactive
session with a trailing semicolon, its value is not shown. The variable `ans` is bound to the
value of the last evaluated expression whether it is shown or not. The `ans` variable is only
bound in interactive sessions, not when Julia code is run in other ways.

To evaluate expressions written in a source file `file.jl`, write `include("file.jl")`.

To run code in a file non-interactively, you can give it as the first argument to the `julia`
command:

<div dir="ltr">

```
$ julia script.jl arg1 arg2...
```

</div>

As the example implies, the following command-line arguments to `julia` are interpreted as
command-line arguments to the program `script.jl`, passed in the global constant `ARGS`. The
name of the script itself is passed in as the global `PROGRAM_FILE`. Note that `ARGS` is
also set when a Julia expression is given using the `-e` option on the command line (see the
`julia` help output below) but `PROGRAM_FILE` will be empty. For example, to just print the
arguments given to a script, you could do this:

<div dir="ltr">

```
$ julia -e 'println(PROGRAM_FILE); for x in ARGS; println(x); end' foo bar

foo
bar
```

</div>

Or you could put that code into a script and run it:

<div dir="ltr">

```
$ echo 'println(PROGRAM_FILE); for x in ARGS; println(x); end' > script.jl
$ julia script.jl foo bar
script.jl
foo
bar
```

</div>

The `--` delimiter can be used to separate command-line arguments intended for the script file from arguments intended for Julia:

<div dir="ltr">

```
$ julia --color=yes -O -- foo.jl arg1 arg2..
```

</div>

See also [Scripting](@ref man-scripting) for more information on writing Julia scripts.

Julia can be started in parallel mode with either the `-p` or the `--machine-file` options. `-p n`
will launch an additional `n` worker processes, while `--machine-file file` will launch a worker
for each line in file `file`. The machines defined in `file` must be accessible via a password-less
`ssh` login, with Julia installed at the same location as the current host. Each machine definition
takes the form `[count*][user@]host[:port] [bind_addr[:port]]`. `user` defaults to current user,
`port` to the standard ssh port. `count` is the number of workers to spawn on the node, and defaults
to 1. The optional `bind-to bind_addr[:port]` specifies the IP address and port that other workers
should use to connect to this worker.

If you have code that you want executed whenever Julia is run, you can put it in
`~/.julia/config/startup.jl`:

<div dir="ltr">

```
$ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.julia/config/startup.jl
$ julia
Greetings! 你好! 안녕하세요?

...
```

</div>

Note that although you should have a `~/.julia` directory once you've run Julia for the
first time, you may need to create the `~/.julia/config` folder and the
`~/.julia/config/startup.jl` file if you use it.

There are various ways to run Julia code and provide options, similar to those available for the
`perl` and `ruby` programs:

<div dir="ltr">

```
julia [switches] -- [programfile] [args...]
```

</div>

A detailed list of all the available switches can be found at [Command-line Options](@ref
command-line-options).

## منابع

A curated list of useful learning resources to help new users get started can be found on the [learning](https://julialang.org/learning/) page of the main Julia web site.