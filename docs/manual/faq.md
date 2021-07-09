# Frequently Asked Questions

## General

### Is Julia named after someone or something?

No.

### Why don't you compile Matlab/Python/R/… code to Julia?

Since many people are familiar with the syntax of other dynamic languages, and lots of code has already been written in those languages, it is natural to wonder why we didn't just plug a Matlab or Python front-end into a Julia back-end (or “transpile" code to Julia) in order to get all the performance benefits of Julia without requiring programmers to learn a new language.  Simple, right?

The basic issue is that there is *nothing special about Julia's compiler*: we use a commonplace compiler (LLVM) with no “secret sauce" that other language developers don't know about.  Indeed, Julia's compiler is in many ways much simpler than those of other dynamic languages (e.g. PyPy or LuaJIT).   Julia's performance advantage derives almost entirely from its front-end: its language semantics allow a [well-written Julia program](@ref man-performance-tips) to *give more opportunities to the compiler* to generate efficient code and memory layouts.  If you tried to compile Matlab or Python code to Julia, our compiler would be limited by the semantics of Matlab or Python to producing code no better than that of existing compilers for those languages (and probably worse).  The key role of semantics is also why several existing Python compilers (like Numba and Pythran) only attempt to optimize a small subset of the language (e.g. operations on Numpy arrays and scalars), and for this subset they are already doing at least as well as we could for the same semantics.  The people working on those projects are incredibly smart and have accomplished amazing things, but retrofitting a compiler onto a language that was designed to be interpreted is a very difficult problem.

Julia's advantage is that good performance is not limited to a small subset of “built-in" types and operations, and one can write high-level type-generic code that works on arbitrary user-defined types while remaining fast and memory-efficient.  Types in languages like Python simply don't provide enough information to the compiler for similar capabilities, so as soon as you used those languages as a Julia front-end you would be stuck.

For similar reasons, automated translation to Julia would also typically generate unreadable, slow, non-idiomatic code that would not be a good starting point for a native Julia port from another language.

On the other hand, language *interoperability* is extremely useful: we want to exploit existing high-quality code in other languages from Julia (and vice versa)!  The best way to enable this is not a transpiler, but rather via easy inter-language calling facilities.  We have worked hard on this, from the built-in `ccall` intrinsic (to call C and Fortran libraries) to [JuliaInterop](https://github.com/JuliaInterop) packages that connect Julia to Python, Matlab, C++, and more.

## Sessions and the REPL

### How do I delete an object in memory?

Julia does not have an analog of MATLAB's `clear` function; once a name is defined in a Julia
session (technically, in module `Main`), it is always present.

If memory usage is your concern, you can always replace objects with ones that consume less memory.
 For example, if `A` is a gigabyte-sized array that you no longer need, you can free the memory
with `A = nothing`.  The memory will be released the next time the garbage collector runs; you can force
this to happen with [`GC.gc()`](@ref Base.GC.gc). Moreover, an attempt to use `A` will likely result in an error, because most methods are not defined on type `Nothing`.

### How can I modify the declaration of a type in my session?

Perhaps you've defined a type and then realize you need to add a new field.  If you try this at
the REPL, you get the error:

```
ERROR: invalid redefinition of constant MyType
```

Types in module `Main` cannot be redefined.

While this can be inconvenient when you are developing new code, there's an excellent workaround.
 Modules can be replaced by redefining them, and so if you wrap all your new code inside a module
you can redefine types and constants.  You can't import the type names into `Main` and then expect
to be able to redefine them there, but you can use the module name to resolve the scope.  In other
words, while developing you might use a workflow something like this:

```julia
include("mynewcode.jl")              # this defines a module MyModule
obj1 = MyModule.ObjConstructor(a, b)
obj2 = MyModule.somefunction(obj1)
# Got an error. Change something in "mynewcode.jl"
include("mynewcode.jl")              # reload the module
obj1 = MyModule.ObjConstructor(a, b) # old objects are no longer valid, must reconstruct
obj2 = MyModule.somefunction(obj1)   # this time it worked!
obj3 = MyModule.someotherfunction(obj2, c)
...
```

## Scripting

### How do I check if the current file is being run as the main script?

When a file is run as the main script using `julia file.jl` one might want to activate extra
functionality like command line argument handling. A way to determine that a file is run in
this fashion is to check if `abspath(PROGRAM_FILE) == @__FILE__` is `true`.

### How do I catch CTRL-C in a script?

Running a Julia script using `julia file.jl` does not throw
`InterruptException` when you try to terminate it with CTRL-C
(SIGINT).  To run a certain code before terminating a Julia script,
which may or may not be caused by CTRL-C, use `atexit`.
Alternatively, you can use `julia -e 'include(popfirst!(ARGS))'
file.jl` to execute a script while being able to catch
`InterruptException` in the `try` block.

### How do I pass options to `julia` using `#!/usr/bin/env`?

Passing options to `julia` in so-called shebang by, e.g.,
`#!/usr/bin/env julia --startup-file=no` may not work in some
platforms such as Linux.  This is because argument parsing in shebang
is platform-dependent and not well-specified.  In a Unix-like
environment, a reliable way to pass options to `julia` in an
executable script would be to start the script as a `bash` script and
use `exec` to replace the process to `julia`:

```julia
#!/bin/bash
#=
exec julia --color=yes --startup-file=no "${BASH_SOURCE[0]}" "$@"
=#

@show ARGS  # put any Julia code here
```

In the example above, the code between `#=` and `=#` is run as a `bash`
script.  Julia ignores this part since it is a multi-line comment for
Julia.  The Julia code after `=#` is ignored by `bash` since it stops
parsing the file once it reaches to the `exec` statement.

```eval_rst

.. note::

    In order to [catch CTRL-C](@ref catch-ctrl-c) in the script you can use

    .. code-block:: julia

        #!/bin/bash
        #=
        exec julia --color=yes --startup-file=no -e 'include(popfirst!(ARGS))' \
            "${BASH_SOURCE[0]}" "$@"
        =#

        @show ARGS  # put any Julia code here

    instead. Note that with this strategy `PROGRAM_FILE` will not be set.

```

## Functions

### I passed an argument `x` to a function, modified it inside that function, but on the outside, the variable `x` is still unchanged. Why?

Suppose you call a function like this:

```julia
julia> x = 10
10

julia> function change_value!(y)
           y = 17
       end
change_value! (generic function with 1 method)

julia> change_value!(x)
17

julia> x # x is unchanged!
10
```

In Julia, the binding of a variable `x` cannot be changed by passing `x` as an argument to a function.
When calling `change_value!(x)` in the above example, `y` is a newly created variable, bound initially
to the value of `x`, i.e. `10`; then `y` is rebound to the constant `17`, while the variable
`x` of the outer scope is left untouched.

However, if `x` is bound to an object of type `Array`
(or any other *mutable* type). From within the function, you cannot "unbind" `x` from this Array,
but you *can* change its content. For example:

```julia
julia> x = [1,2,3]
3-element Vector{Int64}:
 1
 2
 3

julia> function change_array!(A)
           A[1] = 5
       end
change_array! (generic function with 1 method)

julia> change_array!(x)
5

julia> x
3-element Vector{Int64}:
 5
 2
 3
```

Here we created a function `change_array!`, that assigns `5` to the first element of the passed
array (bound to `x` at the call site, and bound to `A` within the function). Notice that, after
the function call, `x` is still bound to the same array, but the content of that array changed:
the variables `A` and `x` were distinct bindings referring to the same mutable `Array` object.

### Can I use `using` or `import` inside a function?

No, you are not allowed to have a `using` or `import` statement inside a function.  If you want
to import a module but only use its symbols inside a specific function or set of functions, you
have two options:

1. Use `import`:

   ```julia
   import Foo
   function bar(...)
       # ... refer to Foo symbols via Foo.baz ...
   end
   ```

   This loads the module `Foo` and defines a variable `Foo` that refers to the module, but does not
   import any of the other symbols from the module into the current namespace.  You refer to the
   `Foo` symbols by their qualified names `Foo.bar` etc.
2. Wrap your function in a module:

   ```julia
   module Bar
   export bar
   using Foo
   function bar(...)
       # ... refer to Foo.baz as simply baz ....
   end
   end
   using Bar
   ```

   This imports all the symbols from `Foo`, but only inside the module `Bar`.

### What does the `...` operator do?

### The two uses of the `...` operator: slurping and splatting

Many newcomers to Julia find the use of `...` operator confusing. Part of what makes the `...`
operator confusing is that it means two different things depending on context.

### `...` combines many arguments into one argument in function definitions

In the context of function definitions, the `...` operator is used to combine many different arguments
into a single argument. This use of `...` for combining many different arguments into a single
argument is called slurping:

```julia
julia> function printargs(args...)
           println(typeof(args))
           for (i, arg) in enumerate(args)
               println("Arg #$i = $arg")
           end
       end
printargs (generic function with 1 method)

julia> printargs(1, 2, 3)
Tuple{Int64, Int64, Int64}
Arg #1 = 1
Arg #2 = 2
Arg #3 = 3
```

If Julia were a language that made more liberal use of ASCII characters, the slurping operator
might have been written as `<-...` instead of `...`.

### `...` splits one argument into many different arguments in function calls

In contrast to the use of the `...` operator to denote slurping many different arguments into
one argument when defining a function, the `...` operator is also used to cause a single function
argument to be split apart into many different arguments when used in the context of a function
call. This use of `...` is called splatting:

```julia
julia> function threeargs(a, b, c)
           println("a = $a::$(typeof(a))")
           println("b = $b::$(typeof(b))")
           println("c = $c::$(typeof(c))")
       end
threeargs (generic function with 1 method)

julia> x = [1, 2, 3]
3-element Vector{Int64}:
 1
 2
 3

julia> threeargs(x...)
a = 1::Int64
b = 2::Int64
c = 3::Int64
```

If Julia were a language that made more liberal use of ASCII characters, the splatting operator
might have been written as `...->` instead of `...`.

### What is the return value of an assignment?

The operator `=` always returns the right-hand side, therefore:

```julia
julia> function threeint()
           x::Int = 3.0
           x # returns variable x
       end
threeint (generic function with 1 method)

julia> function threefloat()
           x::Int = 3.0 # returns 3.0
       end
threefloat (generic function with 1 method)

julia> threeint()
3

julia> threefloat()
3.0
```

and similarly:

```julia
julia> function threetup()
           x, y = [3, 3]
           x, y # returns a tuple
       end
threetup (generic function with 1 method)

julia> function threearr()
           x, y = [3, 3] # returns an array
       end
threearr (generic function with 1 method)

julia> threetup()
(3, 3)

julia> threearr()
2-element Vector{Int64}:
 3
 3
```

## انواع، تعریف نوع و سازنده‌ها

### معنی type-stable چیست؟

این عبارت به این معنی است که جنس خروجی را می‌توان از طریق جنس ورودی‌ها پیش‌بینی کرد.
 به عبارتی دیگر، جنس خروجی‌ها مستقل از
 *مقدار*
 ورودی‌ها می‌باشد. برای مثال کد زیر
 type-stable
 *نمی‌باشد*:

```julia
julia> function unstable(flag::Bool)
           if flag
               return 1
           else
               return 1.0
           end
       end
unstable (generic function with 1 method)
```

کد بالا، با توجه به مقدار آرگومان‌های ورودی، خروجی‌اش از جنس  
`Int`
یا
`Float64`
می‌باشد.
از آنجایی که جولیا نمی‌تواند جنس خروجی این تابع را در زمان کامپایل پیش‌بینی کند،
هر محاسباتی که از این تابع استفاده می‌کند باید با مقادیر از هر دو نوع جنس
سازگار باشد و این موضوع باعث می‌شود تا نوشتن یک کد با سرعت بالا، سخت شود.

### چرا جولیا برای برخی از عملیات‌های به ظاهر معقول خطای `DomainError` می‌دهد؟

برخی از عملیات‌ها در ریاضیات
معقول و معنادار هستند
اما در جولیا با خطا مواجه می‌شوند.
مانند مثال زیر:

```julia
julia> sqrt(-2.0)
ERROR: DomainError with -2.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

این رفتار یکی از نتیجه‌های ناراحت کننده‌ی
type-stability
می‌باشد.
در این مثال خاص،
بسیاری از افراد می‌خواهند تا خروجی تابع
`sqrt(2.0)`
یک عدد حقیقی باشد و دوست ندارند تا خروجی این تابع به صورت
 عدد مختلط
 `1.4142135623730951 + 0.0im`
 باشد.
 شاید یک ایده‌ای که به ذهن بیاید این باشد که
 تابع
 `sqrt`
 را طوری بنویسیم که وقتی آرگومان ورودی آن یک عدد منفی بود، خروجی تابع از جنس یک عدد مختلط باشد
 (مانند کاری که در زبان‌های برنامه‌نویسی دیگر انجام می‌شود).
 اما در این صورت خروجی تابع
 [type-stable](@ref man-type-stability)
 نمی‌باشد و
 تابع
 `sqrt`
 دارای عملکرد ضعیفی می‌شود.

 در این حالت‌ها، جنس ورودی‌ها باید به صورتی باشد که اگر جنس خروجی نیز

 در این حالت‌ها، جنس ورودی
 باید به صورتی باشد که اگر
 جنس خروجی
 نیز به همان شکل باشد، خواسته شما براورده شود و خروجی مد نظرتان در آن
 جنس خروجی
 قابل نمایش باشد. برای
 نمونه مشکل ذکر شده در بالا را می‌توان به این صورت رفع کرد:

```julia
julia> sqrt(-2.0+0im)
0.0 + 1.4142135623730951im
```

### چگونه پارامترهای نوع را محدود یا محاسبه کنیم؟

پارامتر‌های یک نوع پارامتری (
[parametric type](@ref Parametric-Types)
) می‌توانند یا نوع و یا مقدار‌های بیتی را نگهداری کنند و خود تایپ تصمیم می‌گیرد که چگونه از این پارامترها استفاده کنند. به عنوان مثال، به `Array{Float64, 2}` پارامتر نوع `Float64` داده شده است که نوع المنت‌هایش را نشان دهد، و مقدار صحیح `2` که تعداد ابعادش را نشان دهد. وقتی نوع پارامتری خود را تعریف می‌کنید، می‌توانید از محدودیت‌های زیر نوع(subtype) استفاده کنید که اعلام کنید یک پارامتر باید زیرنوع (`<:`) یک نوع abstract یا یک پارامتر قبلی نوع باشد. با این حال، هیچ سینتکس مخصوصی برای اعلام اینکه یک پارامتر باید یک مقدار از یک نوع تعیین‌شده داشته باشد، وجود ندارد. برای مثال در تعریف `struct` نمی‌توانید برای یک پارامتر مثل تعداد بعدها، به طور مستقیم از `isa` `Int` استفاده کنید.

به طور مشابه نمی‌توانید بر روی پارامترهای نوع، محاسبات (شامل عملیات‌های ساده مثل جمع یا تفریق) کنید. در عوض، این نوع محدودیت‌ها و روابط را می‌توانید از طریق پارامترهای نوع دیگری که در سازنده(@ref man-constructors) آورده می‌شوند، اعمال کنید.

به عنوان مثال کد زیر را در نظر بگیرید:

```julia
struct ConstrainedType{T,N,N+1} # NOTE: INVALID SYNTAX
    A::Array{T,N}
    B::Array{T,N+1}
end
```

در این مثال کاربر می‌خواهد همواره پارامتر سوم یک واحد بیشتر از پارامتر دوم باشد. این کار را می‌توان با استفاده از یک پارامتر نوع که در سازنده درونی(@ref man-inner-constructor-methods) چک می‌شود، اعمال کرد (می‌توانید آن را با شروط دیگر نیز ترکیب کنید):

```julia
struct ConstrainedType{T,N,M}
    A::Array{T,N}
    B::Array{T,M}
    function ConstrainedType(A::Array{T,N}, B::Array{T,M}) where {T,N,M}
        N + 1 == M || throw(ArgumentError("second argument should have one more axis" ))
        new{T,N,M}(A, B)
    end
end
```

این کار معمولا هزینه‌ای ندارد زیرا کامپایلر می‌تواند تست‌های معتبر بودن نوع‌های غیر انتزاعی را ترکیب کند. اگر چندتا از پارامترها نیاز به محاسبه داشته باشند، بهتر است تا از یک سازنده‌ی خارجی(@ref man-outer-constructor-methods) برای این محاسبات استفاده کنید:

```julia
ConstrainedType(A) = ConstrainedType(A, compute_B(A))
```

### چرا جولیا از محاسبات ماشینی صحیح استفاده می‌کند؟

جولیا برای محاسبات اعداد صحیح، از محاسبات ماشینی استفاده می‌کند. این به این معنی است که دامنه‌ی مقدارهای `Int` کران‌دار می‌باشد و دو طرف بازه به یکدیگر متصل هستند، به طوری که جمع و تفریق و ضرب کردن اعداد صحیح می‌تواند باعث سرریز یا زیرریز شدن شود و نتایج غیرمنتظره‌ای به وجود بیاورد:

```julia
julia> x = typemax(Int)
9223372036854775807

julia> y = x+1
-9223372036854775808

julia> z = -y
-9223372036854775808

julia> 2*z
0
```

واضحا، این تفاوت زیادی با رفتار اعداد صحیح در ریاضیات دارد و به همین خاطر ممکن است فکر کنید برای یک زبان سطح بالا ایده‌آل نیست که این نتیجه را خروجی دهد. اما، در کارهای عددی که سرعت و دقت اولویت دارند، روش‌های جایگزین بدتر می‌باشند.

یک راه جایگزین این است که هر محاسبات عددی را برای سرریز شدن چک کنیم و در صورت نیاز نتایج را در نوع‌های بزرگتر عدد صحیح، مانند `Int128` و `BigInt` ذخیره کنیم.

متاسفانه، انجام این کار برای تمام محاسبات عددی (برای مثال افزایش مقدار یک شمارنده در یک حلقه) باعث انجام تعداد قابل توجهی از عملیات‌های اضافی می‌شود؛ زیرا نیاز به نوشتن کد برای چک کردن سرریز در هنگام اجرای هر عملیات عددی و همینطور شاخه‌هایی برای رفع کردن مشکل سرریز می‌باشد. مشکل‌ بزرگتر این است که این باعث می‌شود هر عملیات بر روی اعداد صحیح، type-unstable باشد. همان‌طور که در بالا ‌ذکر شده،
type-stability(@ref man-type-stability)
 برای نوشتن کد بهینه ضروری است. اگر نتوانیم روی صحیح بودن نتایج عملیات‌ اعداد صحیح مطمئن باشیم، ایجاد کد سریع و ساده (مشابه کامپایلرهای C و Fortran)‌ غیرممکن است.

یک روش دیگر برای انجام همین کار، که ظاهر type instability را ندارد، این است که `Int` و `BigInt` را در یک نوع عدد صحیح ترکیب کنیم؛ که وقتی نتیجه در بازه‌ی اعداد صحیح ماشینی نمی‌گنجد، به طور درونی روش نگهداری اعداد در خود را تغییر دهد.
در حالی که این روش به طور ظاهری مشکل type-instability را در کد جولیا ندارد، اما در واقعیت فقط همان مشکلات را به کد C-ای که این نوع عدد صحیح را پیاده سازی می‌کند، منتقل می‌کند. پیاده‌سازی خوب و بهینه‌ی این روش شدنی است،‌ ولی چند عیب دارد. یک مشکل این است که روش نگهداری اعداد صحیح و آرایه‌های اعداد صحیح در حافظه، دیگر با روش طبیعی نگه‌داری آن‌ها در
C و Fortran
  و زبان‌های دیگر با اعداد‌ صحیح ماشینی تطابق ندارد. در نتیجه، برای تعامل با این زبان‌ها، در نهایت هنوز نیاز به پیاده‌سازی اعداد صحیحی ماشینی می‌باشد.
هر پیاده‌سازی بدون کران از اعداد صحیح، نمی‌تواند تعداد ثابتی بیت داشته باشد و در نتیجه نمی‌تواند به طور در خط  در یک آرایه با جایگاه‌هایی با اندازه‌ی ثابت ذخیره شود؛ بلکه اعداد صحیح بزرگتر همیشه نیاز به یک ساختار هرمی مجزا دارند. همچنین، هر چقدر پیاده‌سازی ما برای این کار، هوشمندانه باشد، همیشه حالت‌هایی وجود دارند که عملکرد کد ما در این حالت‌ها به صورت غیرقابل پیش‌بینی‌ای بسیار بد باشد.
نمایش پیچیده، عدم امکان تعامل با C و Fortran، عدم امکان نمایش آرایه‌های اعداد صحیح بدون حافظه‌ی هرمی اضافه، و عملکرد غیرقابل پیش‌بینی، باعث می‌شوند که حتی هوشمندانه‌ترین پیاده‌سازی‌ها برای این کار، انتخاب ضعیفی برای کار عددی با عملکرد بالا باشند.

یک جایگزین برای استفاده از اعداد صحیح ترکیبی یا تغییر نوع نتایج به
`BigInt`
، این است که از محاسبات اشباعی‌(Saturating integer arithmetic)‌ برای اعداد صحیح استفاده کنیم؛ یعنی افزودن به بزرگترین مقدار عدد صحیح و یا کاهیدن از کوچکترین مقدار صحیح، مقدار آن‌ها را تغییر نمی‌دهد. این دقیقا روشی‌است که متلب استفاده می‌کند:

```
>> int64(9223372036854775807)

ans =

  9223372036854775807

>> int64(9223372036854775807) + 1

ans =

  9223372036854775807

>> int64(-9223372036854775808)

ans =

 -9223372036854775808

>> int64(-9223372036854775808) - 1

ans =

 -9223372036854775808
```

در نگاه اول، این روش نسبتا منطقی به نظر می‌آید، زیرا 9223372036854775807 بسیار به 9223372036854775808 نسبت به -9223372036854775808 نزدیک‌تر است؛ و اعداد صحیح همچنان به روشی طبیعی با اندازه ثابت نمایش داده می‌شوند که با C و Fortran تطابق دارد. اما، محاسبات اشباعی اعداد صحیح، مشکلات عمیقی دارند.
اولین و واضح‌ترین مشکل این است که این روشی نیست که محاسبات ماشینی اعداد صحیح طبق آن عمل می‌کنند، پس پیاده سازی عملکردهای اشباعی هنوز نیاز به نوشتن کد بعد از هر محاسبه برای چک کردن سرریز و زیرریز و در صورت نیاز جایگزینی نتیجه با
`typemin(Int)`
 یا
`typemax(Int)`
دارد. همین به تنهایی، هر عملیات محاسباتی را از یک عملکرد سریع به چندین عملکرد، که احتمالا شامل شاخه‌های مختلف هستند، تبدیل می‌کند. اما بدتر هم می‌شود! محاسبات اشباعی اعداد صحیح، قابلیت شرکت‌پذیری ندارد. این محاسبات متلب را در نظر بگیرید:

```
>> n = int64(2)^62
4611686018427387904

>> n + (n - 1)
9223372036854775807

>> (n + n) - 1
9223372036854775806
```

این باعث‌ ‌می‌شود نوشتن خیلی از الگوریتم‌های ساده اعداد صحیح به مشکل بخورد، زیرا بسیاری از تکنیک‌های معمول، به ویژگی شرکت‌پذیری جمع و تفریق ماشینی (با سرریز و زیرریز) وابسته هستند. فرض کنید می‌خواهیم از طریق عبارت
`(lo + hi) >>> 1`
، مقدار میانی بین دو عدد صحیح
`lo`
 و
`hi`
 را در جولیا پیدا کنیم:

```julia
julia> n = 2^62
4611686018427387904

julia> (n + 2n) >>> 1
6917529027641081856
```

همانطور که مشاهده می‌کنید، نتیجه درست می‌باشد. با وجود اینکه
`n + 2n`
 برابر
-4611686018427387904
 است، نتیجه‌ی این محاسبات برابر با مقدار میانی صحیح بین دو عدد
62^2
 و
63^2
 است. حالا این کار را در متلب انجام می‌دهیم:

```
>> (n + 2*n)/2

ans =

  4611686018427387904
```

این‌‌بار، جواب درست نمی‌باشد. استفاده کردن از عملگر
`>>>`
 به متلب نیز کمکی به این موضوع نمی‌کند، زیرا اشباعی که هنگام جمع کردن
`n`
 و
`2n`
 اتفاق می‌افتد، اطلاعات لازم برای به دست آوردن مقدار میانی دو عدد را از بین برده است.

عدم ‌شرکت‌پذیری، نه تنها برای برنامه‌نویس‌هایی که نمی‌توانند روی آن برای تکنیک‌های این‌چنینی تکیه کنند مشکل ساز است، که تقریبا از هر روشی که کامپیالر‌ها ممکن است برای بهینه‌سازی محاسبات عددی استفاده کنند، جلوگیری می‌کند. به عنوان مثال، از ‌آن‌جایی که جولیا از محاسبات عددی ماشینی استفاده می‌کند، LLVM آزاد است که تابع‌های ساده و کوتاهی مثل
`f(k) = 5k-1`
 را به خوبی بهینه‌سازی کند. کد ماشینی برای این تابع به صورت زیر می‌باشد:

```julia
julia> code_native(f, Tuple{Int})
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 1
  leaq  -1(%rdi,%rdi,4), %rax
  popq  %rbp
  retq
  nopl  (%rax,%rax)
```

بدنه‌ی اصلی این تابع تنها یک عملیات
`leaq`
است، که ضرب و جمع صحیح را همزمان محاسبه می‌کند. وقتی که
`f`
در یک تابع دیگر به صورت در خط استفاده شود، این کد حتی مفیدتر است:

```julia
julia> function g(k, n)
           for i = 1:n
               k = f(k)
           end
           return k
       end
g (generic function with 1 methods)

julia> code_native(g, Tuple{Int,Int})
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 2
  testq %rsi, %rsi
  jle L26
  nopl  (%rax)
Source line: 3
L16:
  leaq  -1(%rdi,%rdi,4), %rdi
Source line: 2
  decq  %rsi
  jne L16
Source line: 5
L26:
  movq  %rdi, %rax
  popq  %rbp
  retq
  nop
```

از آن‌جایی که صدا زدن تابع
`f`
به صورت در خط انجام می‌شود، بدنه‌ی حلقه در نهایت تنها یک عملیات
`leaq`
 است. حالا در نظر بگیرید چه اتفاقی می‌افتد وقتی تعداد تکرارهای حلقه را ثابت در نظر بگیریم:

```julia
julia> function g(k)
           for i = 1:10
               k = f(k)
           end
           return k
       end
g (generic function with 2 methods)

julia> code_native(g,(Int,))
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 3
  imulq $9765625, %rdi, %rax    # imm = 0x9502F9
  addq  $-2441406, %rax         # imm = 0xFFDABF42
Source line: 5
  popq  %rbp
  retq
  nopw  %cs:(%rax,%rax)
```

چون که کامپایلر می‌داند که جمع و ضرب شرکت‌پذیرند و ضرب روی جمع توزیع‌پذیری دارد (هیچ کدام از این دو در محاسبات اشباعی برقرار نیستند)، می‌تواند کل حلقه را بهینه‌سازی کند و تبدیل به تنها یک جمع و یک ضرب کند. محاسبات اشباعی، کاملا جلوی این نوع بهینه‌سازی را میگیرد، زیرا در هر تکرار، شرکت‌پذیری و توزیع‌پذیری می‌توانند برقرار نباشند و در نتیجه بر اساس اینکه این مشکل در کدام تکرار اتفاق می‌افتد، نتایج مختلفی ایجاد کنند. کامپایلر می‌تواند حلقه را باز کند
(Loop Unrolling)
، ولی نمی‌تواند به طور جبری چند عملگر را با تعداد کمتری عملگر معادل‌سازی کند.

منطقی‌ترین جایگزین برای رفع کردن مشکل  سرریز در محاسبات عددی، این است که همه‌جا محاسبات چک‌شده انجام دهیم و هنگامی که جمع، تفریق یا ضرب سرریز می‌کنند و مقدار‌های اشتباه به وجود می‌آورند، یک خطا خروجی دهیم. در این
 [نوشته](https://danluu.com/integer-overflow/)
،
Dan Luu
این موضوع را بررسی می‌کند و نتیجه‌ می‌گیرد که با وجود هزینه ناچیزی که این روش در تئوری دارد، در واقعیت به دلیل عدم بهینه‌سازی چک‌های اضافه‌ برای سرریز توسط کامپایلرها
(LLVM و GCC)
، هزینه‌ی قابل توجهی دارد. اگر این مساله در آینده پیشرفت کند، می‌توانیم استفاده از محاسبات عددی چک‌شده در جولیا را در نظر بگیریم‌؛ ولی در حال حاضر، باید با امکان به وجود آمدن سرریز زندگی کنیم.

در عین حال، با استفاده از کتابخانه‌های خارجی‌ای مانند  [SaferIntegers.jl](https://github.com/JeffreySarnoff/SaferIntegers.jl)
 می‌توان عملیات‌های عددی‌ای انجام داد که در آن‌ها سرریز پیش نیاید. توجه کنید که همان‌طور که ذکر شد، استفاده از این کتابخانه‌ها، زمان اجرای کد را به دلیل استفاده از نوع‌های عددی چک‌شده، به میزان قابل توجهی زیاد می‌کند. با این حال، برای استفاده محدود، این بسیار مشکل کوچک‌تری است تا این که برای همه‌ی محاسبات عددی استفاده شود. شما می‌توانید وضعیت این بحث را از
[اینجا](https://github.com/JuliaLang/julia/issues/855)
دنبال کنید.

### دلایل ممکن یک `UndefVarError` در هنگام اجرای remote چه می‌باشند؟

همان‌طور که خود خطا بیان می‌کند، یک دلیل اصلی پیش آمدن `UndefVarError` روی یک نود remote این است که یک binding با آن نام وجود ندارد. حال بعضی دلایل ممکن آن را بررسی می‌کنیم.

```julia
julia> module Foo
           foo() = remotecall_fetch(x->x, 2, "Hello")
       end

julia> Foo.foo()
ERROR: On worker 2:
UndefVarError: Foo not defined
Stacktrace:
[...]
```

بستار `x->x` به `Foo` مرجعیت دارد، و از آن‌جایی که در نود ۲ `Foo` در دسترس نیست، یک `UndefVarError` برگردانده می‌شود.

Global ها در ماژول‌های به جز `Main` با مقدارشان به نود remote سریال‌سازی (serialize) نمی‌شوند، بلکه تنها یک مرجع فرستاده می‌شود. تابع‌هایی که binding های سراسری (global) درست می‌کنند (به جز در `Main`) ممکن است باعث شوند در آینده یک `UndefVarError` ایجاد و برگردانده شود.

```julia
julia> @everywhere module Foo
           function foo()
               global gvar = "Hello"
               remotecall_fetch(()->gvar, 2)
           end
       end

julia> Foo.foo()
ERROR: On worker 2:
UndefVarError: gvar not defined
Stacktrace:
[...]
```

در مثال بالا، `@everywhere module Foo` روی همه‌ی نودها، `Foo` را تعریف می‌کند. با این وجود، صدا کردن `Foo.foo()` یک binding سراسری `gvar` روی نود محلی می‌سازد، ولی این در نود ۲ پیدا نشد و باعث یک خطای `UndefVarError` شد.

توجه کنید که این درباره‌ی global های‌ ایجاد‌ شده در ماژول `Main` صدق نمی‌کند. Global های ماژول `Main` سریال‌سازی‌ می‌شوند و binding های جدید در `Main` روی نود remote درست می‌شوند.

```julia
julia> gvar_self = "Node1"
"Node1"

julia> remotecall_fetch(()->gvar_self, 2)
"Node1"

julia> remotecall_fetch(varinfo, 2)
name          size summary
––––––––– –––––––– –––––––
Base               Module
Core               Module
Main               Module
gvar_self 13 bytes String
```

این درباره‌ی تعریف‌های `function` یا `struct` صدق‌ نمی‌کند. با این وجود، همان‌طور که در زیر می‌بینید، تابع‌های ناشناس(anonymous) مقید شده به متغیرهای سراسری سریال‌سازی ‌می‌شوند:

```julia
julia> bar() = 1
bar (generic function with 1 method)

julia> remotecall_fetch(bar, 2)
ERROR: On worker 2:
UndefVarError: #bar not defined
[...]

julia> anon_bar  = ()->1
(::#21) (generic function with 1 method)

julia> remotecall_fetch(anon_bar, 2)
1
```

### چرا جولیا برای الحاق دو رشته از `*` استفاده می‌کند و از `+` یا چیزهای دیگر استفاده نمی‌کند؟

[علت اصلی](@ref man-concatenation)
 استفاده نکردن از نماد `+` این است که الحاق دو رشته خاصیت جابجایی ندارد در حالی که `+` در به طور کلی یک عملگر با خاصیت جابجایی می‌باشد. با اینکه جامعه‌ی جولیا می‌داند که زبان‌های برنامه‌نویسی دیگر از عملگرهای متفاوتی استفاده می‌کنند و احتمالا `*` برای بعضی از کاربران ناآشنا می‌باشد، اما این عملگر خاصیت‌های جبری خاصی را دارا می‌باشد.

همچنین برای الحاق رشته‌ها می‌توانید از
`string(...)`
استفاده کنید(تمامی آرگومان‌های ورودی این تابع ابتدا به رشته تبدیل می‌شوند و سپس به یکدیگر الحاق می‌شوند). به طور مشابه برای تکرار یک رشته، می‌توان از
`repeat`
به جای
`^`
استفاده کرد. همچنین سینتکس
[interpolation](@ref string-interpolation)
برای ساختن رشته‌ها مفید می‌باشد.

## بسته‌ها و ماژول‌ها

### تفاوت بین "using” و "import” چیست؟

فقط یک تفاوت وجود دارد و به طور سطحی (در سینتکس)‌ ممکن است تفاوت بسیار کوچکی به نظر بیاید. تفاوت استفاده از `using` و `import` این است که هنگام استفاده از `using`، برای ارث بری از تابع bar ماژول Foo و جایگزینی آن با یک تابع جدید باید از عبارت
 `function Foo.bar(..`
استفاده کنید؛‌ اما هنگام استفاده از
`import Foo.bar`
، برای ارث بری و جایگزینی تابع، کافی‌ است بگویید
`function bar(...`
و به طور خودکار از تابع bar ماژول Foo ارث بری می‌شود.

دلیلی که این تفاوت اهمیت کافی برای وجود دو سینتکس مجزا دارد، این است که نمی‌خواهید به طور تصادفی تابعی را ارث بری کنید که نمی‌دانستید وجود دارد، زیرا این ‌می‌تواند به سادگی خطا ایجاد کند. احتمال این اتفاق در متدهایی که یک نوع متداول مثل رشته یا عدد صحیح را ورودی می‌گیرند، بیشتر است. زیرا ممکن است هم شما و هم آن ماژول دیگر، یک متد تعریف کنید که یک نوع به این متداولی را پشتیبانی کند. اگر از
`import`
استفاده کنید، آن‌گاه پیاده‌سازی
 `bar(s::AbstractString)`
 در آن ماژول دیگر را با پیاده‌سازی جدید خود جایگزین می‌کنید که به راحتی ممکن است کاری کاملا متفاوت انجام دهد (و همه‌/بسیاری از استفاده‌های آینده‌ از تابع‌های دیگر که تابع bar ماژول Foo را صدا می‌کنند را خراب کند).

## مقدارهای nothingness و missing

### چگونه "null" و "nothingness" و "missingness" در جولیا کار می‌کنند؟

برخلاف بسیاری از زبان‌ها (مثل C و Java)، اشیا‌ء جولیا نمی‌توانند به طور پیشفرض "null" باشند. وقتی یک مرجع (متغیر، فیلد یک شیء، یا المنت یک آرایه) مقدار اولیه نداشته باشد، دسترسی به آن بلافاصله یک خطا ایجاد می‌کند. این موقعیت می‌تواند از طریق تابع‌های
`isdefined`
 یا
[`isassigned`](@ref Base.isassigned)
تشخیص داده شود.

بعضی تابع‌ها فقط برای اثرهای جانبی ‌آن‌ها استفاده می‌شوند، و نیازی به برگرداندن‌ یک مقدار ندارند. در این حالات، رسم این است که مقدار
`nothing`
 را برگردانیم، که تنها یک شی
singleton
 از نوع
`nothing`
 است. این یک نوع عادی بدون فیلد است و هیچ ویژگی خاصی جز این رسم، و اینکه REPL برای ‌آن چیزی چاپ نمی‌کند، ندارد. بعضی ساختار‌های زبان که در غیر این صورت مقداری نمی‌داشتند نیز `nothing` را برمی‌گردانند، به عنوان مثال
`if false; end`
.

در موقعیت‌هایی که یک مقدار `x` از نوع `T` فقط گاهی وجود دارد، نوع
`Union{T, Nothing}`
 می‌تواند برای آرگومان‌های تابع، فیلدهای شیء، یا نوع المنت آرایه استفاده شود. این معادل
[`Nullable`, `Option` یا `Maybe`](https://en.wikipedia.org/wiki/Nullable_type)
 در زبان‌های دیگر است. اگر خود مقدار می‌تواند
`nothing`
 باشد (مخصوصا‌ وقتی که
`T`
برابر با
`Any`
باشد)، نوع
`Union{Some{T}, Nothing}`
 مناسب‌تر است چون
`x == nothing`
 عدم وجود مقدار و
`x == Some(nothing)`
 وجود مقداری برابر با
`nothing`
 را نشان می‌دهد. تابع
`something`
 اجازه‌ی unwrap کردن شی‌های
`Some`
 و دادن یک مقدار پیش‌فرض به جای آرگومان‌های `nothing` می‌دهد. توجه کنید که کامپایلر توانایی تولید کد بهینه هنگام کار کردن با فیلدها یا آرگومان‌های
`Union{T, Nothing}`
 را دارد.

برای نمایش داده‌ی گم‌شده از نظر آماری (`Na` در R یا 'NULL' در SQL)، شی `missing` را استفاده کنید. بخش
[`Missing Values`](@ref missing)
را برای جزئیات بیشتر ببینید.

در بعضی زبان‌ها، برای بیان پوچی، از عبارت متعارف چندتایی خالی (`()`)‌ استفاده می‌شود. اما در جولیا، بهتر است به آن به عنوان یک چندتایی خالی که صرفا صفر مقدار دارد نگاه کنیم.

نوع خالی، که به صورت
`Union{}`
 (یک نوع اجتماع خالی) نوشته می‌شود، یک نوع با هیچ مقدار یا زیرنوع (به جز خودش) است. شما به طور کلی نیاز به استفاده از این نوع نخواهید داشت.

## حافظه

### وقتی `x` و `y` آرایه‌ هستند، چرا `x += y`  حافظه را اشغال می‌کند؟

در جولیا، هنگام خوانش اولیه کد (parse)، عبارت
`x += y`
 با
`x = x + y`
 جایگزین می‌شود. در مورد آرایه‌ها، نتیجه‌ی این موضوع این است که به جای ذخیره شدن نتیجه در همان مکان به عنوان `x`، برای ذخیره نتیجه، یک آرایه‌ی جدید در حافظه ایجاد می‌شود.

گرچه این رفتار ممکن است بعضی‌ها را متعجب کند، این انتخاب عمدی است. دلیل اصلی حضور اشیا غیرقابل تغییر (immutable) در جولیا است که پس از ایجاد نمی‌توان مقدارشان را عوض کرد. در حقیقت، یک عدد یک شی غیرقابل تغییر (immutable) است؛ عبارت های
`x = 5; x += 1`
 معنی `5` را عوض نمی‌کنند، بلکه مقداری که به متغیر `x` نسبت داده شده است را تغییر می‌دهند. برای یک شیء غیرقابل تغییر(immutable)، تنها روش عوض کردن مقدار آن این است که آن را از اول تعریف کنید.

برای درک بیشتر، تابع زیر را در نظر بگیرید:

```julia
function power_by_squaring(x, n::Int)
    ispow2(n) || error("This implementation only works for powers of 2")
    while n >= 2
        x *= x
        n >>= 1
    end
    x
end
```

بعد از صدا زدن عبارت  
`x = 5; y = power_by_squaring(x, 4)`
، نتیجه مورد نظر
`x == 5 && y == 625`
را می‌گیریم. اما فرض کنید که
`*=`
، هنگام استفاده با ماتریس‌ها، سمت چپ عبارت را قابل تغییر می‌کرد. این کار، دو مشکل را به وجود می‌آورد:

+ برای یک ماتریس مربعی در حالت کلی،
`A = A*b`
 نمی‌تواند بدون حافظه‌ی موقت پیاده‌سازی شود:
`A[1,1]`
 محاسبه می‌شود و مقدار آن قبل از اینکه استفاده شما از آن در سمت راست عبارت تمام شود، در سمت چپ عبارت ذخیره می‌شود.

+ فرض کنید که حاضر به تخصیص حافظه‌ی موقت برای این محاسبات باشیم (که البته باعث از بین رفتن فایده‌ی کار کردن درجای(in-place) `*=` می‌شود)؛ در صورت استفاده از ویژگی قابل تغییر بودن `x`، این تابع برای ورودی‌های قابل تغییر و غیرقابل تغییر رفتار‌های متفاوتی دارد. به طور دقیق‌تر، وقتی که `x` غیرقابل تغییر باشد، بعد از اجرای تابع به طور کلی داریم `x != y`، ولی اگر `x` قابل تغییر باشد، آنگاه داریم `y == x`.

به دلیل اینکه پشتیبانی از برنامه‌نویسی generic مهم‌تر از بهینه‌سازی‌های ممکن عملکرد (که از طریق‌های دیگر، مثل استفاده از حلقه‌های صریح، ممکن هستند) تلقی می‌شود، عملگرهایی مثل
`+=`
 و
`*=`
 از طریق مقداردهی کردن دوباه‌یر متغیر با مقدارهای جدید کار می‌کنند.

## ورودی و خروجی‌های غیرهمگام و نوشتن همزمان و همگام

### چرا نوشتن‌های همزمان در یک جریان یکسان (stream) باعث خروجی inter-mixed می‌شوند؟

با وجود اینکه جریان I/O API همگام است، پیاده‌سازی زیرین آن به طور کامل ناهمگام است.

خروجی چاپ‌شده زیر را در نظر بگیرید:

```julia
julia> @sync for i in 1:3
           @async write(stdout, string(i), " Foo ", " Bar ")
       end
123 Foo  Foo  Foo  Bar  Bar  Bar
```

دلیل این اتفاق این است که با وجود اینکه صدا کردن `write` همگام است، نوشتن هرکدام از آرگومان‌ها به بخش‌های دیگر داده می‌شود و هم‌زمان با آن بقیه‌ی بخش‌های I/O انجام می‌گیرد.

`print`
و
`println`
هنگامی که صدا زده می‌شوند، جریان (stream) را "قفل" می‌کنند. در نتیجه عوض کردن `write` به `println` در مثال بالا نتیجه زیر را دارد:

```julia
julia> @sync for i in 1:3
           @async println(stdout, string(i), " Foo ", " Bar ")
       end
1 Foo  Bar
2 Foo  Bar
3 Foo  Bar
```

شما می‌توانید `write` های خود را با یک
`ReentrantLock`
، به طور زیر قفل کنید:

```julia
julia> l = ReentrantLock();

julia> @sync for i in 1:3
           @async begin
               lock(l)
               try
                   write(stdout, string(i), " Foo ", " Bar ")
               finally
                   unlock(l)
               end
           end
       end
1 Foo  Bar 2 Foo  Bar 3 Foo  Bar
```

## آرایه‌ها

### تفاوت‌های بین آرایه‌های صفر بعدی و اسکالرها چیست؟

آرایه‌های صفر بعدی، آرایه‌های از فرم
`Array{T,0}`
 هستند. آن‌ها مشابه اسکالرها رفتار می‌کنند، اما تفاوت‌های مهمی نیز دارند. توجه خاص به این نوع از آرایه‌ها مهم است، زیرا این آرایه‌ها یک حالت خاص هستند که با توجه به تعریف کلی آرایه‌ها منطقی هستند، ولی در نگاه اول ممکن است مشهود نباشند. خط زیر یک آرایه صفر بعدی تعریف می‌کند:

```
julia> A = zeros()
0-dimensional Array{Float64,0}:
0.0
```

در مثال زیر، `A` یک نگهدارنده‌ی قابل تغییر (mutable container) است که یک المنت دارد. این المنت می‌تواند از طریق
`A[] = 1.0`
 مقداردهی شود و از طریق
`A[]`
 می‌توان به آن دسترسی داشت. همه آرایه‌های صفر بعدی سایز (
`size(A) == ()`
)‌ و طول (
`length(A) == 1`
) یکسانی دارند. توجه کنید که آرایه‌های صفر بعدی، خالی نیستند. اگر همچنان شهودی روی این موضوع ندارید، چند‌ ایده‌ی زیر ممکن است به درک تعریف جولیا به شما کمک کنند:

+ همان‌طور که بردار‌ها "خط" و ماتریس‌ها "صفحه" تلقی می‌شوند،‌ آرایه‌های صفر بعدی را نیز می‌توان "نقطه" در نظر گرفت. همان‌طور که یک خط مساحت ندارد (ولی هنوز یک مجموعه از اشیا را نمایش می‌دهد)، یک نقطه هم طول یا ابعاد ندارد (ولی هنوز یک شی را نمایش می‌دهد).
+ عبارت
`prod(())`
 را برابر با ۱ تعریف می‌کنیم، و تعداد کل المنت‌های یک آرایه برابر با حاصلضرب درایه‌های `size`  آن است. سایز یک آرایه صفر بعدی برابر با `()` است، و در نتیجه طول آن ۱ است.
+ آرایه‌های صفر بعدی به طور طبیعی هیچ ابعادی ندارند که بتوانیم با اندیس به آن‌ها دسترسی پیدا کنیم. تنها عضو آن ها
`A[]`
است. اما می‌توانیم برای آن‌ها، مشابه بقیه ابعاد آرایه‌ها، همان قانون "trailing one" را استفاده کنیم. پس می‌توانیم همچنان از اندیس‌های
 `A[1]`
و
`A[1, 1]`
 و غیره استفاده کنیم. بخش اندیس‌های حذف‌شده و اضافه را ببینید.

هم‌چنان لازم است تفاوت‌های آن‌ها را با اسکالرهای عادی بفهمیم. اسکالر‌ها نگهدارنده‌های تغییر پذیر (mutable containers) نیستند (هرچند که قابل پیمایش (iterable) هستند و چیز‌هایی مثل
`length`
و
`getindex`
 را تعریف می‌کنند، به عنوان مثال
`1[] == 1`
). به طور خاص، اگر
`x = 0.0`
 به طور اسکالر تعریف شده باشد، اگر برای تغییر مقدار آن بخواهیم از عبارت

`x[] = 1.0`
استفاده کنیم، به خطا می‌خوریم. یک اسکالر `x` می‌تواند از طریق
`fill(x)`
 به یک آرایه‌ی صفر بعدی تعریف شود. از آن طرف، یک آرایه‌ی صفر بعدی `a` می‌تواند از طریق `a[]` به اسکالر تبدیل شود. یک تفاوت دیگر این است که یک اسکالر میتواند در محاسبات جبر خطی مثل
`2 * rand(2,2)`
شرکت کند، ولی عملیات مشابه
`fill(2) * rand(2,2)`
 با یک آرایه‌ی صفر بعدی باعث خطا می‌شود.

### چرا benchmark های من در جولیا برای عملیات‌های جبرخطی با زبان‌های دیگر فرق دارند؟

ممکن است ببینید که benchmark های ساده‌ی الگوریتم‌های پایه‌ای جبرخطی مثل

```julia
using BenchmarkTools
A = randn(1000, 1000)
B = randn(1000, 1000)
@btime $A \ $B
@btime $A * $B
```

می‌توانند در مقایسه با زبان‌هایی مثل متلب یا R متفاوت باشند.

از آن‌جایی که عملیات‌هایی مثل این پوشش‌های خیلی نازکی روی تابع‌های BLAS مرتبط هستند، دلیل این تفاوت به احتمال خیلی زیاد موارد زیر است:

۱. کتابخانه BLAS ای که هر زبان استفاده می‌کند،

۲. تعداد تردهای هم‌زمان (concurrent threads).

جولیا کپی خودش از OpenBLAS را کامپایل و استفاده می‌کند، و تردهای آن در حال حاضر سقف ۸ (یا به تعداد هسته‌های شما) را دارند.

تغییر دادن تنظیمات OpenBLAS یا کامپایل کردن جولیا با یک کتابخانه دیگر BLAS، به عنوان مثال
[Intel MKL](https://software.intel.com/en-us/mkl)
، ممکن است در بازدهی پیشرفت ایجاد کند. شما میتوانید
[MKL.jl](https://github.com/JuliaComputing/MKL.jl)
 را استفاده کنید، این یک بسته (package) است که باعث میشود جبرخطی جولیا به جای OpenBLAS از
 Intel MKL BLAS
 و LAPACK استفاده کند. همین‌طور می‌توانید در انجمن تبادل نظر، پیشنهادهایی برای انجام این کار به طور دستی پیدا کنید. توجه کنید که Intel MKL نمی‌تواند با جولیا بسته‌بندی شود، زیرا متن باز (open source) نیست.

## خوشه محاسبات

### چگونه می‌توانم کش‌های پیش از کامپایل (precompilation) را در سیستم‌های پرونده‌ای توزیع شده (distributed file systems) مدیریت کنم؟

وقتی‌که از جولیا برای محاسبات با عملکرد بالا (HPC) استفاده می‌کنید، استفاده‌ی هم‌زمان از n فرآیند `julia`، به طور همزمان حداکثر n کپی موقت از فایل‌های کش پیش از کامپایل ایجاد می‌کند. اگر این یک مشکل است (سیستم‌های پرونده‌ای توزیع شده کند و یا کوچک)، می‌توانید:

۱. از `julia` با
flag
 `--compiled-modules=no` استفاده کنید تا پیش-کامپایل کردن را خاموش کنید.

۲. با استفاده از `pushfirst!(DEPOT_PATH, private_path)`  یک منبع (depot) قابل نوشتن خصوصی ایجاد کنید، به طوری که `private_path` یک مسیر یکتا به این فرآیند `julia` است. این می‌تواند از طریق عوض کردن متغیر محیطی  `JULIA_DEPOT_PATH` به `$private_path:$HOME/.julia` نیز انجام شود.

۳. یک symlink از `~/.julia/compiled` به یک پوشه در یک فضای scratch ایجاد کنید.

## نسخه‌های جولیا

### خوب است نسخه‌ی پایدار، LTS یا nightly جولیا را استفاده کنم؟

نسخه‌ی پایدار جولیا جدیدترین نسخه منتشر شده از جولیا است، و این نسخه‌ای است که برای اکثر افراد بهتر است. این نسخه جدیدترین قابلیت‌ها را دارد و شامل بهتر شدن عملکرد نیز می‌باشد. نسخه‌های پایدار جولیا،‌ بر اساس
[SemVer](https://semver.org/)
 به صورت v1.x.y شماره‌گذاری می‌شوند. پس از چند‌ هفته تست شدن به عنوان یک کاندیدای توزیع، هر ۴ الی ۵ ماه تغییرات کوچکی به نسخه‌ی پایدار جولیا اضافه می‌شود.
برخلاف نسخه‌ی LTS، نسخه‌ی پایدار به طور خودکار اصلاح‌های خطا ها را پس از توزیع یک نسخه‌ی پایدار جدید دریافت نمی‌کند.
با این حال، ارتقا به نسخه‌ی پایدار بعدی همیشه ممکن خواهد بود، چون که هر نسخه‌ی v1.x همچنان کدهایی که برای نسخه‌های قبلی نوشته شده‌اند را اجرا می‌کند.

اگر دنبال یک پایه‌ی بسیار پایدار برای کد خود هستید، ممکن است نسخه‌ی LTS (پشتیبانی بلند مدت) جولیا را ترجیح دهید. نسخه LTS کنونی طبق SemVer به صورت v1.0.x شماره‌دهی می‌شود. این شاخه ادامه به دریافت اصلاح‌های خطاها خواهد کرد، تا زمانی که یک شاخه LTS جدید انتخاب شود. از آن پس، سری v1.0.x دیگر اصلاح‌های خطاها را به طور معمول دریافت نخواهد کرد، و همه به استثنای محافظه‌کارترین کاربرها توصیه به استفاده از سری جدید نسخه‌های LTS می‌شوند.
به عنوان یک توسعه‌دهنده بسته، احتمالا ترجیح خواهید داد که نسخه LTS را توسعه دهید، تا تعداد کاربرانی که می‌توانند بسته‌ی شما را استفاده کنند را بیشینه کنید. طبق SemVer، کدهای نوشته شده برای نسخه v1.0، برای همه‌ی نسخه‌های LTS و پایدار آینده ادامه به کار خواهند کرد.
به طور کلی، حتی اگر هدف شما استفاده از LTS است، تا زمانی که از ویژگی‌های جدید (مثل تابع‌های اضافه‌شده به کتابخانه‌ها یا متدهای جدید) دوری کنید، می‌توانید برای استفاده از عملکرد ارتقا یافته، در جدیدترین نسخه‌ی پایدار کد خود را توسعه دهید و اجرا کنید.

اگر می‌خواهید از جدیدترین به‌روز‌رسانی‌ها به زبان استفاده کنید، و به این که نسخه‌ای که امروز در دسترس است ممکن است گاهی در حقیقت کار نکند اهمیت نمی‌دهید، ممکن است ترجیح دهید از نسخه nightly جولیا استفاده کنید.
همانطور که اسم آن اشاره می‌کند، تقریبا به طور شبانه (بسته به پایداری زیرساخت)،‌ نسخه‌ی nightly به‌روزرسانی می‌شود. به طور کلی نسخه‌های nightly به‌ میزان خوبی برای استفاده امن هستند، کد شما آتش نمی‌گیرد! با این حال، ممکن است گاه‌به‌گاه پسرفت‌ها یا مشکلاتی وجود داشته باشد که تا تست‌های پیش از توزیع بیشتری انجام شوند، تشخیص داده نمی‌شوند. ممکن است بخواهید کد خود را با نسخه‌ی دیگر چک کنید تا مطمئن شوید چنین پسرفت‌هایی که کاربرد شما را تحت تاثیر قرار می‌دهند، تشخیص داده شوند.

در نهایت، ممکن است بخواهید ساختن جولیا برای خودتان از منبع را در نظر قرار دهید. این گزینه، عمدتا برای افرادی هست که با خط فرمان (command line) راحت هستند، یا علاقه‌مند به یاد گرفتن آن هستند. اگر این شما را توصیف می‌کند، ممکن است بخواهید
[راهنمایی برای توسعه](https://github.com/JuliaLang/julia/blob/master/CONTRIBUTING.md)
 ما را بخوانید.

لینک‌های دانلود هر کدام از نوع‌های مختلف را می‌توانید از صفحه‌ی دانلود، در
[https://julialang.org/downloads/](https://julialang.org/downloads/)
مشاهده کنید. توجه کنید که همه‌ی نسخه‌های جولیا برای همه‌ی پلتفرم‌ها موجود نیستند.

### چگونه می‌توانم بعد از به روز‌رسانی نسخه‌ی جولیای خود، لیست بسته‌های نصب‌شده‌ام را انتقال دهم؟

هر نسخه‌ی جزئی جولیا،
[محیط](https://docs.julialang.org/en/v1/manual/code-loading/#Environments-1)
 پیش‌فرض خودش را دارد. در نتیجه، هنگامی که یک نسخه‌ی جزئی جولیا را نصب کنید، بسته‌هایی که به نسخه‌هایی جزئی قبلی جولیا اضافه کرده‌اید، به طور پیش‌فرض در دسترس نخواهند بود. محیط (environment) هر نسخه‌ی جولیا در پرونده‌های `Project.toml` و `Manifest.toml` در یک پوشه در `.julia/environments/` ذخیره می‌شوند که با شماره‌ی جولیا تطابق دارد، به عنوان مثال ` .julia/environments/v1.3` .

اگر یک نسخه‌ی جزئی جدید جولیا، مثلا `1.4` را نصب کنید، و بخواهید در محیط پیش‌فرض ‌آن از همان بسته‌هایی که در یک نسخه‌ی قبلی (مثلا `1.3`) استفاده کرده‌اید استفاده کنید، می‌توانید محتوای پرونده‌ی `Project.toml` را از پوشه‌ی `1.3` به `1.4` کپی کنید. سپس در یک نشست (session) نسخه‌ی جدید جولیا، با وارد کردن `]`، به "package management mode" وارد شوید و فرمان
[`instantiate`](https://julialang.github.io/Pkg.jl/v1/api/#Pkg.instantiate)
 را انجام دهید.

این عملیات یک مجموعه‌ از بسته‌ها در پرونده‌ی کپی‌شده که با نسخه‌ی مورد نظر جولیا تطابق دارند را انتخاب می‌کند، و در صورت امکان آن‌ها را نصب یا به‌روزرسانی می‌کند. اگر می‌خواهید نه تنها مجموعه‌ی بسته‌ها، که نسخه‌هایی از آن‌ها که در نسخه‌ی قدیم جولیا استفاده می‌کردید را بازسازی کنید، باید قبل از اجرای دستور `instantiate` در Pkg، پرونده‌ی `Manifest.toml` را نیز کپی کنید. اما توجه کنید که بسته‌ها ممکن است محدودیت‌های سازگاری‌ای تعریف کنند که با تغییر نسخه‌ی جولیا تحت‌تاثیر قرار بگیرند، پس مجموعه‌ی دقیق نسخه‌هایی که در `1.3` داشتید ممکن است برای `1.4` کار نکنند.
