# Environment Variables

Julia can be configured with a number of environment variables, set either in
the usual way for each operating system, or in a portable way from within Julia.
Supposing that you want to set the environment variable `JULIA_EDITOR` to `vim`,
you can type `ENV["JULIA_EDITOR"] = "vim"` (for instance, in the REPL) to make
this change on a case by case basis, or add the same to the user configuration
file `~/.julia/config/startup.jl` in the user's home directory to have a
permanent effect. The current value of the same environment variable can be
determined by evaluating `ENV["JULIA_EDITOR"]`.

The environment variables that Julia uses generally start with `JULIA`. If
`InteractiveUtils.versioninfo` is called with the keyword `verbose=true`, then the
output will list any defined environment variables relevant for Julia,
including those which include `JULIA` in their names.

```eval_rst

.. note::

    Some variables, such as `JULIA_NUM_THREADS` and `JULIA_PROJECT`, need to be set before Julia
    starts, therefore adding these to `~/.julia/config/startup.jl` is too late in the startup process.
    In Bash, environment variables can either be set manually by running, e.g.,
    `export JULIA_NUM_THREADS=4` before starting Julia, or by adding the same command to
    `~/.bashrc` or `~/.bash_profile` to set the variable each time Bash is started.
```

## File locations

### `JULIA_BINDIR`

The absolute path of the directory containing the Julia executable, which sets
the global variable `Sys.BINDIR`. If `$JULIA_BINDIR` is not set, then
Julia determines the value `Sys.BINDIR` at run-time.

The executable itself is one of

```
$JULIA_BINDIR/julia
$JULIA_BINDIR/julia-debug
```

by default.

The global variable `Base.DATAROOTDIR` determines a relative path from
`Sys.BINDIR` to the data directory associated with Julia. Then the path

```
$JULIA_BINDIR/$DATAROOTDIR/julia/base
```

determines the directory in which Julia initially searches for source files (via
`Base.find_source_file()`).

Likewise, the global variable `Base.SYSCONFDIR` determines a relative path to the
configuration file directory. Then Julia searches for a `startup.jl` file at

```
$JULIA_BINDIR/$SYSCONFDIR/julia/startup.jl
$JULIA_BINDIR/../etc/julia/startup.jl
```

by default (via `Base.load_julia_startup()`).

For example, a Linux installation with a Julia executable located at
`/bin/julia`, a `DATAROOTDIR` of `../share`, and a `SYSCONFDIR` of `../etc` will
have `JULIA_BINDIR` set to `/bin`, a source-file search path of

```
/share/julia/base
```

and a global configuration search path of

```
/etc/julia/startup.jl
```

### `JULIA_PROJECT`

A directory path that indicates which project should be the initial active project.
Setting this environment variable has the same effect as specifying the `--project`
start-up option, but `--project` has higher precedence. If the variable is set to `@.`
then Julia tries to find a project directory that contains `Project.toml` or
`JuliaProject.toml` file from the current directory and its parents. See also
the chapter on [Code Loading](@ref code-loading).

```eval_rst

.. note::

    `JULIA_PROJECT` must be defined before starting julia; defining it in `startup.jl`
    is too late in the startup process.
```

### `JULIA_LOAD_PATH`

The `JULIA_LOAD_PATH` environment variable is used to populate the global Julia
`LOAD_PATH` variable, which determines which packages can be loaded via
`import` and `using` (see [Code Loading](@ref code-loading)).

Unlike the shell `PATH` variable, empty entries in `JULIA_LOAD_PATH` are expanded to
the default value of `LOAD_PATH`, `["@", "@v#.#", "@stdlib"]` when populating
`LOAD_PATH`. This allows easy appending, prepending, etc. of the load path value in
shell scripts regardless of whether `JULIA_LOAD_PATH` is already set or not. For
example, to prepend the directory `/foo/bar` to `LOAD_PATH` just do
```sh
export JULIA_LOAD_PATH="/foo/bar:$JULIA_LOAD_PATH"
```
If the `JULIA_LOAD_PATH` environment variable is already set, its old value will be
prepended with `/foo/bar`. On the other hand, if `JULIA_LOAD_PATH` is not set, then
it will be set to `/foo/bar:` which will expand to a `LOAD_PATH` value of
`["/foo/bar", "@", "@v#.#", "@stdlib"]`. If `JULIA_LOAD_PATH` is set to the empty
string, it expands to an empty `LOAD_PATH` array. In other words, the empty string
is interpreted as a zero-element array, not a one-element array of the empty string.
This behavior was chosen so that it would be possible to set an empty load path via
the environment variable. If you want the default load path, either unset the
environment variable or if it must have a value, set it to the string `:`.

### `JULIA_DEPOT_PATH`

The `JULIA_DEPOT_PATH` environment variable is used to populate the global Julia
`DEPOT_PATH` variable, which controls where the package manager, as well
as Julia's code loading mechanisms, look for package registries, installed
packages, named environments, repo clones, cached compiled package images,
configuration files, and the default location of the REPL's history file.

Unlike the shell `PATH` variable but similar to `JULIA_LOAD_PATH`, empty entries in
`JULIA_DEPOT_PATH` are expanded to the default value of `DEPOT_PATH`. This allows
easy appending, prepending, etc. of the depot path value in shell scripts regardless
of whether `JULIA_DEPOT_PATH` is already set or not. For example, to prepend the
directory `/foo/bar` to `DEPOT_PATH` just do
```sh
export JULIA_DEPOT_PATH="/foo/bar:$JULIA_DEPOT_PATH"
```
If the `JULIA_DEPOT_PATH` environment variable is already set, its old value will be
prepended with `/foo/bar`. On the other hand, if `JULIA_DEPOT_PATH` is not set, then
it will be set to `/foo/bar:` which will have the effect of prepending `/foo/bar` to
the default depot path. If `JULIA_DEPOT_PATH` is set to the empty string, it expands
to an empty `DEPOT_PATH` array. In other words, the empty string is interpreted as a
zero-element array, not a one-element array of the empty string. This behavior was
chosen so that it would be possible to set an empty depot path via the environment
variable. If you want the default depot path, either unset the environment variable
or if it must have a value, set it to the string `:`.

```eval_rst

.. note::

    On Windows, path elements are separated by the `;` character, as is the case with
    most path lists on Windows.
```

### `JULIA_HISTORY`

The absolute path `REPL.find_hist_file()` of the REPL's history file. If
`$JULIA_HISTORY` is not set, then `REPL.find_hist_file()` defaults to

```
$(DEPOT_PATH[1])/logs/repl_history.jl
```

## External applications

### `JULIA_SHELL`

The absolute path of the shell with which Julia should execute external commands
(via `Base.repl_cmd()`). Defaults to the environment variable `$SHELL`, and
falls back to `/bin/sh` if `$SHELL` is unset.

```eval_rst

.. note::

    On Windows, this environment variable is ignored, and external commands are
    executed directly.
```

### `JULIA_EDITOR`

The editor returned by `InteractiveUtils.editor()` and used in, e.g., `InteractiveUtils.edit`,
referring to the command of the preferred editor, for instance `vim`.

`$JULIA_EDITOR` takes precedence over `$VISUAL`, which in turn takes precedence
over `$EDITOR`. If none of these environment variables is set, then the editor
is taken to be `open` on Windows and OS X, or `/etc/alternatives/editor` if it
exists, or `emacs` otherwise.

## Parallelization

### `JULIA_CPU_THREADS`

Overrides the global variable `Base.Sys.CPU_THREADS`, the number of
logical CPU cores available.

### `JULIA_WORKER_TIMEOUT`

A `Float64` that sets the value of `Distributed.worker_timeout()` (default: `60.0`).
This function gives the number of seconds a worker process will wait for
a master process to establish a connection before dying.

### `JULIA_NUM_THREADS`

An unsigned 64-bit integer (`uint64_t`) that sets the maximum number of threads
available to Julia. If `$JULIA_NUM_THREADS` is not positive or is not set, or if
the number of CPU threads cannot be determined through system calls, then the number
of threads is set to `1`.

```eval_rst

.. note::

    `JULIA_NUM_THREADS` must be defined before starting julia; defining it in `startup.jl` is too late in the startup process.
```

```eval_rst

.. versionchanged:: 1.5
    In Julia 1.5 and above the number of threads can also be specified on startup
    using the `-t`/`--threads` command line argument.
```

### `JULIA_THREAD_SLEEP_THRESHOLD`

If set to a string that starts with the case-insensitive substring `"infinite"`,
then spinning threads never sleep. Otherwise, `$JULIA_THREAD_SLEEP_THRESHOLD` is
interpreted as an unsigned 64-bit integer (`uint64_t`) and gives, in
nanoseconds, the amount of time after which spinning threads should sleep.

### `JULIA_EXCLUSIVE`

If set to anything besides `0`, then Julia's thread policy is consistent with
running on a dedicated machine: the master thread is on proc 0, and threads are
affinitized. Otherwise, Julia lets the operating system handle thread policy.

## قالب بندی REPL

متغیر های محیطی که مشخص می کنند که چطور خروجی REPL باید در نرمینال قالب بندی شده باشد. به صورت عمومی این متغیر ها باید روی [ANSI terminal escape sequences](http://ascii-table.com/ansi-escape-sequences.php) قرار داده شوند. جولیا یک واسط سطح بالا با بیش تر شباهت از لحاظ کارکردی ارائه می کند. قسمت REPL جولیا (The Julia REPL) را مشاهده کنید.

### `JULIA_ERROR_COLOR`

قالب بندی `Base.error_color()` (default: light red, `"\033[91m"`) که ارورها باید در ترمینال داشته باشند.

### `JULIA_WARN_COLOR`

قالب بندی `Base.warn_color()` (default: yellow, `"\033[93m"`) که هشدار ها باید در ترمینال داشته باشند.

### `JULIA_INFO_COLOR`

قالب بندی `Base.info_color()` (default: cyan, `"\033[36m"`) که اطلاعات باشد در ترمینال داشته باشند.

### `JULIA_INPUT_COLOR`

قالب بندی `Base.input_color()` (default: normal, `"\033[0m"`) که ورودی ها باید در ترمینال داشته باشند.

### `JULIA_ANSWER_COLOR`

قالب بندی `Base.answer_color()` (default: normal, `"\033[0m"`) که خروجی ها باید در ترمینال داشته باشند.

## دیباگ کردن و بازرسی

### `JULIA_DEBUG`

امکان رویداد نگاری (logging) دیباگ کردن را برای یک قایل یا یک ماژول می دهد. برای اطلاعات بیش تر [`Logging`](@ref Logging) را مشاهده کنید.

### `JULIA_GC_ALLOC_POOL`, `JULIA_GC_ALLOC_OTHER`, `JULIA_GC_ALLOC_PRINT`

اگر قرار داده شوند این متغیر های محیطی رشته ها را که به صورت اختیاری با کاراکتر `'r'` شروع می شوند و در ادامه الحاق یک رشته از لیستی از سه عدد صحیح ۶۴ بیتی علامت دار (`int64_t`) که توسط دو نقطه (:) جدا شده اند را می گیرد. این سه گانه از اعداد صحیح `a:b:c` نشان دهنده دنباله حسابی `a`, `a + b`, `a + 2*b`, ... `c` هستند.

*   اگر این دفعه `n` ام است که `jl_gc_pool_alloc()` صدا زده شده است و `n` متعلق به دنباله حسابی که توسط `$JULIA_GC_ALLOC_POOL` نشان داده شده است باشد پس جمع آوری زباله (garbage collection)  اجبار شده است.

*   اگر این دفعه `n` ام باشد که `maybe_collect()` صدا زده شده است و `n` متعلق به دنباله حسابی ای باشد که توسط `$JULIA_GC_ALLOC_OTHER` نشان داده می شود پس زباله رویی (garbage collection) اجبار شده است.

*   اگر این دفعه `n` ام باشد که `jl_gc_collect()` صدا زده شده باشد و `n` متعلق به دنباله حسابی ای باشد که `$JULIA_GC_ALLOC_PRINT` نشان می دهد پس شمارش برای تعداد دا زدن های `jl_gc_pool_alloc()` و `maybe_collect()` نمایش داده شده اند.

اگر مقدار متغیر محیطی با کاراکتر `'r'` شروع شود پس  فاصله بین اتفاق های زباله رویی (garbage collection events) به صورت رندوم است.

```eval_rst

.. note::

    این متغیر های محیطی فقط وقتی تاثیر دارند که جولیا با دیباگ زباله رویی (garbage-collection debugging) اجرا شود. (یعنی اگر `WITH_GC_DEBUG_ENV` در پیکر بندی ساخت برابر با `1` قرار داده شده است )
    
```

### `JULIA_GC_NO_GENERATIONAL`

اگر به غیر از `0` روی هر چیزی قرار داده شده باشد پس زباله روب (garbage collector) جولیا هرگز جاروی سریع ("quick sweeps") حافظه را اجرا نمی کند.

```eval_rst

.. note::
    
    این متغیر محیطی تنها وقتی تاثیر دارد که جولیا همراه با دیباگ کردن توسط زیاله رویی (garbage-collection debugging) اجرا شده باشد. ( یعنی اگر `WITH_GC_DEBUG_ENV` در پیکر بندی ساخت برابر با `1` قرار داده شده باشد.)
    
```

### `JULIA_GC_WAIT_FOR_DEBUGGER`

اگر برابر با چیزی جز `0` قرار داده شده باشد پس زباله روب جولیا برای یک دیباگ کننده خواهد ایستاد تا وقتی که یک ارور مهم وجود دارد به جای حذف کردن متصل کند.

```eval_rst

.. note::
    
    این متغیر محیطی تنها وقتی تاثیر دارد که جولیا همراه با دیباگ کردن به صورت زباله رویی (garbage-collection debugging) اجرا شده باشد. (یعنی اگر `WITH_GC_DEBUG_ENV` در پیکر بندی ساخت برابر با `1` قرار داده شده باشد )
    
```

### `ENABLE_JITPROFILING`

اگر برابر با چیزی جز `0` قرار داده شده باشد پس کامپایلر یک listener  را برای بازرسی just-in-time (JIT) می سازد و قرار می دهد.

```eval_rst

.. note::

    این متغیر محیطی تنها وقتی تاثیر دارد که جولیا همراه با پشتیبانی بازرسی JIT اجرا شده باشد با استفاده از یکی از 
    
    * Intel's [VTune™ Amplifier](https://software.intel.com/en-us/vtune)
      (`USE_INTEL_JITEVENTS` برابر با `1` در پیکر بندی ساخت ), or

    * [OProfile](http://oprofile.sourceforge.net/news/) (`USE_OPROFILE_JITEVENTS` برابر با `1`
      در پیکر بندی ساخت).

    * [Perf](https://perf.wiki.kernel.org) (`USE_PERF_JITEVENTS` برابر با `1` در پیکر بندی ساخت). This integration is enabled by default.
    این ادغام به صورت دیفالت فعال شده است.

```

### `ENABLE_GDBLISTENER`

 اگر برابر با هر چیزی به غیر از `0` قرار داده شود ثبت GDB کد جولیا را در ساخته های منتشر شده امکان پذیر می کند. رو ساخته های دیباگ جولیا این متغیر همیشه فعال شده است و پیشنهاد می شود که همراه با `-g 2` استفاده شود.   

### `JULIA_LLVM_ARGS`

 آرگومان هایی که باید به بک اند LLVM منتقل شوند. 

