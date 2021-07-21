# Control Flow

Julia provides a variety of control flow constructs:

  * [عبارات مرکب]: `begin` و `;`.
  * [ارزیابی شرطی]: `if`-`elseif`-`else` و `?:` (عملگر سه تایی).
  * ارزیابی اتصال کوتاه: عملگر های منطقی `&&` (“and”) و `||` (“or”) و همچنین مقایسه های زنجیری.
  * [ارزیابی تکراری : حلقه ها]: `while` و `for`.
  * مدیریت استثنا ها: `try`-`catch`، `error` و `throw`.
  * کار ها : `yieldto`.

پنج تای اول از مکانیزم های کنترل جریان که گفته شد در زمره استاندارد های زبان های سطح بالا قرار دارند. اما برای مورد آخر اینگونه نیست:این مورد یک کنترل جریان غیر محلی فراهم می کند به طوری که تغییر وضعیت بین محاسبات به طور موقت معلق را امکان پذیر می کند. این یک ساختار قدرتمند است: به طوری که مدیریت استثنا ها و cooperative multitasking توسط آن پیاده سازی شده اند. در برنامه نویسی روزمره نیازی به استفاده مستقیم از tasks نیست اما بعضی مسيله ها را می توان با استفاده از tasks بسیار راحت تر حل کرد. 

## عبارت های مرکب

بعضی اوقات مناسب است که یک عبارت واحد داشته باشید که چندین زیر عبارت را به ترتیب ارزیابی کند و مقدار آخرین زیر عبارت را به عنوان مقدار خود برگرداند. دو ساختار در جولیا وجود دارند که این را انجام می دهند: قطعه های 'begin' و زنجیر های ';'. مقدار هر دو ساختار برابر است با مقدار آخرین زیر عبارت. اینجا یک مثال از قطعه 'begin' می بینیم:

```julia
julia> z = begin
           x = 1
           y = 2
           x + y
       end
3
```
همین طور چون این عبارت ها کوتاه و ساده هستند می توان آن ها را در یک خط نوشت.این جاست که ساختار زنجیری ';' استفاده می شود:

```julia
julia> z = (x = 1; y = 2; x + y)
3
```
به ویژه این سینتکس مفید است زمانی که با فرم تعریف تابع در یک خط استفاده شود که در قسمت های قبل معرفی شد. اگر چه که این مرسوم است ولی نیازی نیست حتما قطعه های 'begin' چند خطه باشند و یا زنجیر های ';' یک خط باشد:

```julia
julia> begin x = 1; y = 2; x + y end
3

julia> (x = 1;
        y = 2;
        x + y)
3
```

## ارزیابی شرطی

ارزیابی شرطی با توجه به مقدار بولی عبارت اجازه می دهد که یک قسمت از کد ارزیابی بشود یا نشود. اینجا مثالی از ساختار بدنه `if`-`elseif`-`else` است:  

```julia
if x < y
    println("x is less than y")
elseif x > y
    println("x is greater than y")
else
    println("x is equal to y")
end
```

  اگر عبارت شرطی`x < y`  صحیح باشد، بلوک وابسته به آن ارزیابی می شود در غیر این صورت عبارت `x > y`ارزیابی می شود اگر صحیح باشد بلوک وابسته به آن ارزیابی می شود و در غیر این صورت عبارت  بلوک `else` ارزیابی می شود.  در عمل به صورت زیر است:

```julia
julia> function test(x, y)
           if x < y
               println("x is less than y")
           elseif x > y
               println("x is greater than y")
           else
               println("x is equal to y")
           end
       end
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```

قطعه های `elseif` و `else` اختیاری هستند همچنین از هر تعداد از `elseif` می توان استفاده کرد.عبارت های شرطی در ساختار `if`-`elseif`-`else` ارزیابی می شوند تا اینکه یکی از آن ها صحیح شود سپس بلوک مربوط به آن عبارت ارزیابی می شود و عبارت های شرطی و بلوک های دیگر بررسی نمی شوند.

بلوک `if` به صورت "leaky" است به این معنا که یک محدوده محلی نیست. این به این معناست که اگر متغیری درون بلوک `if` تعریف شود می توان بعد از بلوک `if`  استفاده کرد حتی اگر قبل از آن تعریف نشده باشد. پس ما می توانیم تابع `test` را به صورت زیر تعریف کنیم:

```julia
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           else
               relation = "greater than"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(2, 1)
x is greater than y.
```

متغیر `relation` داخل بلوک `if`  ظاهر شده است اما بیرون آن استفاده شده است. با این حال، اطمینان حاصل کنید که در تمام مسیر های ممکنی که کد ممکن است اجرا شود مقداری برای متغیر در نظر گرفته شده باشد. تغییر زیر در تعریف تابع بالا موجب بروز خطای زمان اجرا می شود

```julia
julia> function test(x,y)
           if x < y
               relation = "less than"
           elseif x == y
               relation = "equal to"
           end
           println("x is ", relation, " y.")
       end
test (generic function with 1 method)

julia> test(1,2)
x is less than y.

julia> test(2,1)
ERROR: UndefVarError: relation not defined
Stacktrace:
 [1] test(::Int64, ::Int64) at ./none:7
```

بلوک `if` همچنین یک خروجی بر می گرداند که ممکن است برای کسانی که از زبان های دیگر آمده اند غیر شهودی به نظر برسد. این مقدار برابر است با مقداری که آخرین دستور اجرا شده در شاخه ای که انتخاب شده است، بنابراین

```julia
julia> x = 3
3

julia> if x > 0
           "positive!"
       else
           "negative..."
       end
"positive!"
```

توجه کنید که عبارت های شرطی کوتاه که به صورت پر تکرار هستند با استفاده از ارزیابی اتصال کوتاه پیاده سازی می شوند که در قسمت بعدی توضیح داده می شود.

بر خلاف زبان هایی مثل C، MATLAB، Perl، Pythonو Ruby و مثل زبان های Javaو تعداد دیگری از زبان ها این یک خطاست که مقدار یک عبارت شرطی هر چیزی باشد به جز `true`و `false`: 

```julia
julia> if 1
           println("true")
       end
ERROR: TypeError: non-boolean (Int64) used in boolean context
```

این خطا می گوید که عبارت شرط نوع اشتباهی داشته است : نوعش `Int64` بوده در صورتی که `Bool` نیاز بوده است.

عملگر ۳ تایی `?:` خیلی نزدیک و مرتبط است به سینتکس `if`-`elseif`-`else`، اما جایی استفاده می شود که یک انتخاب شرطی روی مقدار یک عبارت نیاز است بر خلاف اجرای شرطی بلوک های طولانی کد. اسمش از آن جایی آمده است که در اکثر زبان ها تنها عملگری است که فقط ۳ عملوند می گیرد:

```julia
a ? b : c
```

عبارت `a` قبل از `?` یک عبارت شرطی است و عملگر سه تایی اختصاص می دهد عبارت `b` قبل از `:` اگر `a` درست باشد یا `c` بعد از `:` اگر غلط باشد. توجه کنید که فضا های خالی دور `?` و `:` اجباری است به طوری که یک عبارت به شکل `a?b:c` یک عبارت معتبر سه تایی نیست ( اما یک خط جدید بعد از هر دو `?` و `:` قابل قبول است).

راحت ترین راه برای فهم درست رفتار این عملگر دیدن مثالی از آن است. در مثال قبلی، `println` مشترک بود در همه شاخه ها و تنها تفاوت در مقدار رشته های ورودی بود. می توان این مورد را با استفاده از عملگر سه تایی پیاده سازی کرد. به خاطر وضوح بیشتر، ابتدا دو حالت را امتحان می کنیم:

```julia
julia> x = 1; y = 2;

julia> println(x < y ? "less than" : "not less than")
less than

julia> x = 1; y = 0;

julia> println(x < y ? "less than" : "not less than")
not less than
```

اگر عبارت `x < y` صحیح باشد، داخل عملگر سه تایی `"less than"` را اختصاص می دهد و در غیر این صورت `"not less than"`. برای صورت اصلی مثال راه ۳ تایی نیازمند استفاده پیاپی و زنجیری از عملگر سه تایی هستیم: 

```julia
julia> test(x, y) = println(x < y ? "x is less than y"    :
                            x > y ? "x is greater than y" : "x is equal to y")
test (generic function with 1 method)

julia> test(1, 2)
x is less than y

julia> test(2, 1)
x is greater than y

julia> test(1, 1)
x is equal to y
```

برای ساده سازی زنجیری نوشتن، عملگر به صورت راست به چپ کار می کند. 

این قابل ملاحظه است که مثل `if`-`elseif`-`else`، عبارت قبل و بعد از `:` ارزیابی می شود اگر عبارت شرطی مقدار `true` یا `false` داشته باشد:

```julia
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> 1 < 2 ? v("yes") : v("no")
yes
"yes"

julia> 1 > 2 ? v("yes") : v("no")
no
"no"
```

## ارزیابی اتصال کوتاه

به ترتیب عملگر های `&&` و `||` درجولیا برابرند با ٬و٬ و ٬یا٬ منطقی. آن ها یک ویژگی اضافه ای دارند به نام ارزیابی اتصال کوتاه به طوری که در آن لزوما ورودی دوم مورد بررسی قرار نمی گیرد همچنان که در پایین توضیح داده شده است ( همچنین عملگر های بیتی `&` و `|` وجود دارند که از آن ها در به عنوان با ٬و٬ و ٬یا٬ منطقی بدون اتصال کوتاه می توان استفاده کرد اما هشیار باشد که `&` و `|` اولویت بیشتری نسبت به `&&` و `||` دارند).

ارزیابی اتصال کوتاه کاملا شبیه ارزیابی شرطی است. این رفتار در اکثر زبان های برنامه نویسی که  عملگر های  `&&` و `||` را دارند پیدا می شود به طوری که یک سری عبارت بولی با استفاده از این عملگر ها به هم متصل شده اند و فقط کمترین تعداد از عبارت ها که نیاز به بررسی جهت ارزیابی عبارت کل دارد مورد بررسی قرار می گیرد. به طور صریح معنی اش این است که:

  * در عبارت `a && b`، زیر عبارت `b` فقط زمانی ارزیابی می شود که `a` صحیح باشد.
  * در عبارت `a || b`، زیر عبارت `b` فقط زمانی ارزیابی می شود که `a` غلط باشد.

دلیلش این است که `a && b` باید غلط باشد اگر `a` غلط باشد صرف نظر از مقدار `b`. مشابها مقدار `a || b` باید صحیح باشد اگر `a` صحیح باشد صرف نظر از مقدار `b`. هر دو عملگر `&&` و `||` از راست اعمال می شوند، اما `&&` الویت بالاتری نسبت به `||` دارد. آسان است این رفتار را آزمایش کنیم:

```julia
julia> t(x) = (println(x); true)
t (generic function with 1 method)

julia> f(x) = (println(x); false)
f (generic function with 1 method)

julia> t(1) && t(2)
1
2
true

julia> t(1) && f(2)
1
2
false

julia> f(1) && t(2)
1
false

julia> f(1) && f(2)
1
false

julia> t(1) || t(2)
1
true

julia> t(1) || f(2)
1
true

julia> f(1) || t(2)
1
2
true

julia> f(1) || f(2)
1
2
false
```

شما می توانید به سادگی مانند بالا وابستگی و اولویت ترکیب های مختلف از `&&` و `||` را بررسی کنید.

این رفتار به صورت پر تکرار در جولیا استفاده می شود که یک فرم جایگزین برای `if` های کوتاه است. به جای `if <cond> <statement> end`، می توان نوشت `<cond> && <statement>`(که خوانده می شود <cond> و <statement>). به طور مشابه به جای `if ! <cond> <statement> end` می توان نوشت `<cond> || <statement>`(که خوانده می شود <cond> یا <statement>).

برای مثال تابع بازگشتی فاکتوریل را می توان به صورت زیر تعریف کرد

```julia
julia> function fact(n::Int)
           n >= 0 || error("n must be non-negative")
           n == 0 && return 1
           n * fact(n-1)
       end
fact (generic function with 1 method)

julia> fact(5)
120

julia> fact(0)
1

julia> fact(-1)
ERROR: n must be non-negative
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fact(::Int64) at ./none:2
 [3] top-level scope
```

 
Boolean operations *without* short-circuit evaluation can be done with the bitwise boolean operators
introduced in Mathematical Operations and Elementary Functions: `&` and `|`. These are
normal functions, which happen to support infix operator syntax, but always evaluate their arguments:

```julia
julia> f(1) & t(2)
1
2
false

julia> t(1) | t(2)
1
2
true
```

Just like condition expressions used in `if`, `elseif` or the ternary operator, the operands of
`&&` or `||` must be boolean values (`true` or `false`). Using a non-boolean value anywhere except
for the last entry in a conditional chain is an error:

```julia
julia> 1 && true
ERROR: TypeError: non-boolean (Int64) used in boolean context
```

On the other hand, any type of expression can be used at the end of a conditional chain. It will
be evaluated and returned depending on the preceding conditionals:

```julia
julia> true && (x = (1, 2, 3))
(1, 2, 3)

julia> false && (x = (1, 2, 3))
false
```

## Repeated Evaluation: Loops

There are two constructs for repeated evaluation of expressions: the `while` loop and the `for`
loop. Here is an example of a `while` loop:

```julia
julia> i = 1;

julia> while i <= 5
           println(i)
           global i += 1
       end
1
2
3
4
5
```

The `while` loop evaluates the condition expression (`i <= 5` in this case), and as long it remains
`true`, keeps also evaluating the body of the `while` loop. If the condition expression is `false`
when the `while` loop is first reached, the body is never evaluated.

The `for` loop makes common repeated evaluation idioms easier to write. Since counting up and
down like the above `while` loop does is so common, it can be expressed more concisely with a
`for` loop:

```julia
julia> for i = 1:5
           println(i)
       end
1
2
3
4
5
```

Here the `1:5` is a range object, representing the sequence of numbers 1, 2, 3, 4, 5. The `for`
loop iterates through these values, assigning each one in turn to the variable `i`. One rather
important distinction between the previous `while` loop form and the `for` loop form is the scope
during which the variable is visible. If the variable `i` has not been introduced in another
scope, in the `for` loop form, it is visible only inside of the `for` loop, and not
outside/afterwards. You'll either need a new interactive session instance or a different variable
name to test this:

```julia
julia> for j = 1:5
           println(j)
       end
1
2
3
4
5

julia> j
ERROR: UndefVarError: j not defined
```

See [Scope of Variables](@ref scope-of-variables) for a detailed explanation of variable scope and how it works in
Julia.

In general, the `for` loop construct can iterate over any container. In these cases, the alternative
(but fully equivalent) keyword `in` or `∈` is typically used instead of `=`, since it makes
the code read more clearly:

```julia
julia> for i in [1,4,0]
           println(i)
       end
1
4
0

julia> for s ∈ ["foo","bar","baz"]
           println(s)
       end
foo
bar
baz
```

Various types of iterable containers will be introduced and discussed in later sections of the
manual (see, e.g., [Multi-dimensional Arrays](@ref man-multi-dim-arrays)).

It is sometimes convenient to terminate the repetition of a `while` before the test condition
is falsified or stop iterating in a `for` loop before the end of the iterable object is reached.
This can be accomplished with the `break` keyword:

```julia
julia> i = 1;

julia> while true
           println(i)
           if i >= 5
               break
           end
           global i += 1
       end
1
2
3
4
5

julia> for j = 1:1000
           println(j)
           if j >= 5
               break
           end
       end
1
2
3
4
5
```

Without the `break` keyword, the above `while` loop would never terminate on its own, and the `for` loop would iterate up to 1000. These loops are both exited early by using `break`.

In other circumstances, it is handy to be able to stop an iteration and move on to the next one
immediately. The `continue` keyword accomplishes this:

```julia
julia> for i = 1:10
           if i % 3 != 0
               continue
           end
           println(i)
       end
3
6
9
```

This is a somewhat contrived example since we could produce the same behavior more clearly by
negating the condition and placing the `println` call inside the `if` block. In realistic usage
there is more code to be evaluated after the `continue`, and often there are multiple points from
which one calls `continue`.

Multiple nested `for` loops can be combined into a single outer loop, forming the cartesian product
of its iterables:

```julia
julia> for i = 1:2, j = 3:4
           println((i, j))
       end
(1, 3)
(1, 4)
(2, 3)
(2, 4)
```

With this syntax, iterables may still refer to outer loop variables; e.g. `for i = 1:n, j = 1:i`
is valid.
However a `break` statement inside such a loop exits the entire nest of loops, not just the inner one.
Both variables (`i` and `j`) are set to their current iteration values each time the inner loop runs.
Therefore, assignments to `i` will not be visible to subsequent iterations:

```julia
julia> for i = 1:2, j = 3:4
           println((i, j))
           i = 0
       end
(1, 3)
(1, 4)
(2, 3)
(2, 4)
```

If this example were rewritten to use a `for` keyword for each variable, then the output would
be different: the second and fourth values would contain `0`.

Multiple containers can be iterated over at the same time in a single `for` loop using `zip`:

```julia
julia> for (j, k) in zip([1 2 3], [4 5 6 7])
           println((j,k))
       end
(1, 4)
(2, 5)
(3, 6)
```

Using `zip` will create an iterator that is a tuple containing the subiterators for the containers passed to it.
The `zip` iterator will iterate over all subiterators in order, choosing the ``i``th element of each subiterator in the
``i``th iteration of the `for` loop. Once any of the subiterators run out, the `for` loop will stop.

## Exception Handling

When an unexpected condition occurs, a function may be unable to return a reasonable value to
its caller. In such cases, it may be best for the exceptional condition to either terminate the
program while printing a diagnostic error message, or if the programmer has provided code to handle
such exceptional circumstances then allow that code to take the appropriate action.

### Built-in `Exception`s

`Exception`s are thrown when an unexpected condition has occurred. The built-in `Exception`s listed
below all interrupt the normal flow of control.

| `Exception`                   |
|:----------------------------- |
| `ArgumentError`       |
| `BoundsError`         |
| `CompositeException`  |
| `DimensionMismatch`   |
| `DivideError`         |
| `DomainError`         |
| `EOFError`            |
| `ErrorException`      |
| `InexactError`        |
| `InitError`           |
| `InterruptException`  |
| `InvalidStateException`       |
| `KeyError`            |
| `LoadError`           |
| `OutOfMemoryError`    |
| `ReadOnlyMemoryError` |
| `RemoteException`     |
| `MethodError`         |
| `OverflowError`       |
| `Meta.ParseError`     |
| `SystemError`         |
| `TypeError`           |
| `UndefRefError`       |
| `UndefVarError`       |
| `StringIndexError`    |

For example, the `sqrt` function throws a `DomainError` if applied to a negative
real value:

```julia
julia> sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

You may define your own exceptions in the following way:

```julia
julia> struct MyCustomException <: Exception end
```

### The `throw` function

Exceptions can be created explicitly with `throw`. For example, a function defined only
for nonnegative numbers could be written to `throw` a `DomainError` if the argument
is negative:

```julia
julia> f(x) = x>=0 ? exp(-x) : throw(DomainError(x, "argument must be nonnegative"))
f (generic function with 1 method)

julia> f(1)
0.36787944117144233

julia> f(-1)
ERROR: DomainError with -1:
argument must be nonnegative
Stacktrace:
 [1] f(::Int64) at ./none:1
```

Note that `DomainError` without parentheses is not an exception, but a type of exception.
It needs to be called to obtain an `Exception` object:

```julia
julia> typeof(DomainError(nothing)) <: Exception
true

julia> typeof(DomainError) <: Exception
false
```

Additionally, some exception types take one or more arguments that are used for error reporting:

```julia
julia> throw(UndefVarError(:x))
ERROR: UndefVarError: x not defined
```

This mechanism can be implemented easily by custom exception types following the way `UndefVarError`
is written:

```julia
julia> struct MyUndefVarError <: Exception
           var::Symbol
       end

julia> Base.showerror(io::IO, e::MyUndefVarError) = print(io, e.var, " not defined")
```

```eval_rst

.. note::
    When writing an error message, it is preferred to make the first word lowercase. For example,

    `size(A) == size(B) || throw(DimensionMismatch("size of A not equal to size of B"))`

    is preferred over

    `size(A) == size(B) || throw(DimensionMismatch("Size of A not equal to size of B"))`.

    However, sometimes it makes sense to keep the uppercase first letter, for instance if an argument
    to a function is a capital letter:

    `size(A,1) == size(B,2) || throw(DimensionMismatch("A has first dimension..."))`.
```

### Errors

The `error` function is used to produce an `ErrorException` that interrupts
the normal flow of control.

Suppose we want to stop execution immediately if the square root of a negative number is taken.
To do this, we can define a fussy version of the `sqrt` function that raises an error
if its argument is negative:

```julia
julia> fussy_sqrt(x) = x >= 0 ? sqrt(x) : error("negative x not allowed")
fussy_sqrt (generic function with 1 method)

julia> fussy_sqrt(2)
1.4142135623730951

julia> fussy_sqrt(-1)
ERROR: negative x not allowed
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fussy_sqrt(::Int64) at ./none:1
 [3] top-level scope
```

If `fussy_sqrt` is called with a negative value from another function, instead of trying to continue
execution of the calling function, it returns immediately, displaying the error message in the
interactive session:

```julia
julia> function verbose_fussy_sqrt(x)
           println("before fussy_sqrt")
           r = fussy_sqrt(x)
           println("after fussy_sqrt")
           return r
       end
verbose_fussy_sqrt (generic function with 1 method)

julia> verbose_fussy_sqrt(2)
before fussy_sqrt
after fussy_sqrt
1.4142135623730951

julia> verbose_fussy_sqrt(-1)
before fussy_sqrt
ERROR: negative x not allowed
Stacktrace:
 [1] error at ./error.jl:33 [inlined]
 [2] fussy_sqrt at ./none:1 [inlined]
 [3] verbose_fussy_sqrt(::Int64) at ./none:3
 [4] top-level scope
```

### The `try/catch` statement

The `try/catch` statement allows for `Exception`s to be tested for, and for the
graceful handling of things that may ordinarily break your application. For example,
in the below code the function for square root would normally throw an exception. By
placing a `try/catch` block around it we can mitigate that here. You may choose how
you wish to handle this exception, whether logging it, return a placeholder value or
as in the case below where we just printed out a statement. One thing to think about
when deciding how to handle unexpected situations is that using a `try/catch` block is
much slower than using conditional branching to handle those situations.
Below there are more examples of handling exceptions with a `try/catch` block:

```julia
julia> try
           sqrt("ten")
       catch e
           println("You should have entered a numeric value")
       end
You should have entered a numeric value
```

`try/catch` statements also allow the `Exception` to be saved in a variable. The following
contrived example calculates the square root of the second element of `x` if `x`
is indexable, otherwise assumes `x` is a real number and returns its square root:

```julia
julia> sqrt_second(x) = try
           sqrt(x[2])
       catch y
           if isa(y, DomainError)
               sqrt(complex(x[2], 0))
           elseif isa(y, BoundsError)
               sqrt(x)
           end
       end
sqrt_second (generic function with 1 method)

julia> sqrt_second([1 4])
2.0

julia> sqrt_second([1 -4])
0.0 + 2.0im

julia> sqrt_second(9)
3.0

julia> sqrt_second(-9)
ERROR: DomainError with -9.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

Note that the symbol following `catch` will always be interpreted as a name for the exception,
so care is needed when writing `try/catch` expressions on a single line. The following code will
*not* work to return the value of `x` in case of an error:

```julia
try bad() catch x end
```

Instead, use a semicolon or insert a line break after `catch`:

```julia
try bad() catch; x end

try bad()
catch
    x
end
```

The power of the `try/catch` construct lies in the ability to unwind a deeply nested computation
immediately to a much higher level in the stack of calling functions. There are situations where
no error has occurred, but the ability to unwind the stack and pass a value to a higher level
is desirable. Julia provides the `rethrow`, `backtrace`, `catch_backtrace`
and `Base.catch_stack` functions for more advanced error handling.

### `finally` Clauses

In code that performs state changes or uses resources like files, there is typically clean-up
work (such as closing files) that needs to be done when the code is finished. Exceptions potentially
complicate this task, since they can cause a block of code to exit before reaching its normal
end. The `finally` keyword provides a way to run some code when a given block of code exits, regardless
of how it exits.

For example, here is how we can guarantee that an opened file is closed:

```julia
f = open("file")
try
    # operate on file f
finally
    close(f)
end
```

When control leaves the `try` block (for example due to a `return`, or just finishing normally),
`close(f)` will be executed. If the `try` block exits due to an exception, the exception will
continue propagating. A `catch` block may be combined with `try` and `finally` as well. In this
case the `finally` block will run after `catch` has handled the error.

## Tasks (aka Coroutines)

Tasks are a control flow feature that allows computations to be suspended and resumed in a flexible
manner. We mention them here only for completeness; for a full discussion see
[Asynchronous Programming](@ref man-asynchronous).
