# عملیات ریاضی و توابع اولیه

زبان برنامه نویسی جولیا یک مجموعه کامل از عملگرهای محاسباتی و بیتی اولیه برای کار بر روی انواع تایپ‌های عددی اولیه را فراهم نموده است. همچنین پیاده‌سازی‌های بهینه از یک مجموعه همه جانبه از توابع استاندارد ریاضی ارائه می‌دهد.

## عملگرهای محاسباتی

[عملگرهای محاسباتی](https://en.wikipedia.org/wiki/Arithmetic#Arithmetic_operations) زیر برای تمامی تایپ‌های عددی اولیه پشتیبانی می‌شوند:

| نماد | نام           | توضیحات                            |
|:---------- |:-------------- |:-------------------------------------- |
| `+x`       | جمع یگانی     |  عمل همانی                |
| `-x`       | تفریق یگانی    | مقادیر را به وارون جمعی‌شان تبدیل می‌کند |
| `x + y`    | جمع باینری     | عمل جمع                      |
| `x - y`    | تفریق باینری   | عمل تفریق                   |
| `x * y`    | ضرب          | عمل ضرب                |
| `x / y`    | تقسیم         | عمل تقسیم                      |
| `x ÷ y`    | تقسیم صحیح | قسمت صحیح `x / y` را بر می‌گرداند         |
| `x \ y`    | تقسیم بالعکس | معادل با `x / y` است                  |
| `x ^ y`    | توان          | `x` را به توان `y` می‌رساند   |
| `x % y`    | باقیمانده      | معادل با `rem(x,y)` است      |

یک لیترال عددی که دقیقا قبل از یک متغیر یا پرانتز قرار گرفته است، برای مثال `2x`  یا  `2(x+y)`، به صوورت عمل ضرب در نظر گرفته می‌شود، که اولویت بالاتری از سایر عملیات باینری دارد. 

سیستم ارتقای جولیا به صورت طبیعی و خود به خودی(اتوماتیک) عملگرهای محاسباتی را برای مخلوطی از انواع تایپ‌ها مدیریت کرده و درست کار می‌کنند. برای اطلاع بیشتر در مورد سیستم ارتقاء، بخش [تبدیل و ارتقاء](@ref conversion-and-promotion) را ببیند.

یک سری مثال ساده را در زیر مشاهده می‌کنید:

```julia
julia> 1 + 2 + 3
6

julia> 1 - 2
-1

julia> 3*2/12
0.5
```


(طبق قرار داد، برای استحکام بیشتر در بیان عملگرها، ما آن‌ها را در نزدیکترین حالت به عملوندشان، قبل از عملوند قرار می‌دهیم. برای مثال، ما معمولاً باید بنویسیم `x+2-`تا ابتدا مقدار `x` را منفی کرده سپس `2` را به آن اضافه کنیم.)

در عمل ضرب، `false` به صورت یک *صفر قوی* عمل می‌کند.

```julia
julia> NaN * false
0.0

julia> false * Inf
0.0
```

این کار برای پیشگیری از انتشار مقادیر  `NaN` در کمیت‌هایی که می‌دانیم برابر صفر هستند، استفاده می‌شود. برای اطلاعات بیشتر [Knuth (1992)](https://arxiv.org/abs/math/9205211) را ببینید.


## عملگرهای بولی

[عملگر‌های بولی](https://en.wikipedia.org/wiki/Boolean_algebra#Operations) زیر روی تایپ `Bool`  اعمال می شوند:

| نماد | توضیحات                                                    |
|:---------- |:--------------------------------------------------------|
|     `!x`  |            منفی کردن                                  |
| `x && y`   | [اند اتصال کوتاه](@ref man-conditional-evaluation) |
| `x \|\| y` | [اور اتصال کوتاه](@ref man-conditional-evaluation)  |

عمل منفی کردن `true` را به `false` تبدیل می‌کند و برعکس. عملیات‌های اتصال کوتاه در صفحه‌ی لینک‌شده توضیح داده شده‌اند.

توجه کنید که  `Bool` یک تایپ عدد صحیح است و همه‌ روابط ارتقا و عملگر‌های عددی روی آن تعریف می‌شوند.


## عملگر‌های بیتی

[عملگرهای محاسباتی](https://en.wikipedia.org/wiki/Bitwise_operation#Bitwise_operators) زیر برای تمامی تایپ‌های صحیح اولیه پشتیبانی می‌شوند:

| توضیحات | نماد                                                                     |
|:---------- |:------------------------------------------------------------------------ |
| `~x`       | عمل not به صورت بیت به بیت                                                              |
| `x & y`    | عمل and به صورت بیتی                                                              |
| `x \| y`   | عمل اور به صورت بیتی                                                               |
| `x ⊻ y`    | عمل xor بیتی                                               |
| `x >>> y`  | [شیفت منطقی](https://en.wikipedia.org/wiki/Logical_shift) به سمت راست       |
| `x >> y`   | [شیفت محاسباتی](https://en.wikipedia.org/wiki/Arithmetic_shift) به سمت راست |
| `x << y`   | شیفت منطقی/محاسباتی به  چپ                                      |

مثال‌هایی از عملگرهای بیتی در جولیا:

```julia
julia> ~123
-124

julia> 123 & 234
106

julia> 123 | 234
251

julia> 123 ⊻ 234
145

julia> xor(123, 234)
145

julia> ~UInt32(123)
0xffffff84

julia> ~UInt8(123)
0x84
```


## عملگرهای بروزرسان

تمام عملگرهای باینری محاسباتی و بیتی یک حالت بروز رسانی دارند که نتایج عملیات را در عملوند سمت چپ خود می‌ریزند. حالت بروز رسانی عملگرهای باینری بوسیله استفاده بدون واسطه `=` پس از عملگر، به کار گرفته می‌شود. برای نمونه، نوشتن `x+=3` معادل است با نوشتن `x=x+3`:

```julia
julia> x = 1
1

julia> x += 3
4

julia> x
4
```


حالت بروزرسانی تمام عملگرهای محاسباتی و بیتی به صورت زیر اند:

```
+=  -=  *=  /=  \=  ÷=  %=  ^=  &=  |=  ⊻=  >>>=  >>=  <<=
```

```eval_rst

.. note::

    An updating operator rebinds the variable on the left-hand side. As a result, the type of the
    variable may change.

    .. code-block:: julia

        julia> x = 0x01; typeof(x)
        UInt8

        julia> x *= 2 # Same as x = x * 2
        2

        julia> typeof(x)
        Int64

```

## عملگرهای نقطه‌ای برداری

برای تمامی عملیات‌های باینری مانند `^`، یک عملیات متناظر نقطه‌ای `^.` وجود دارد که به صورت خود به خودی(اتوماتیک) عمل `^` را بر روی تک تک المان‌های یک آرایه اعمال می‌کند. برای مثال، بیان `3^[1,2,3]` تعریف نشده است زیرا مکعب یک ارایه‌ (ارایه غیر مربعی) تعریف استاندارد ریاضیاتی ندارد. اما `3^.[1,2,3]` به صورت محاسبه‌ی المانی(یا برداری) `[3^3, 3^2, 3^1]` تعریف شده است. به طور مشابه برای عملگرهای یگانی مانند `!` یا `√` نیز  `√.` وجود دارد که عملیات را به صورت المانی اجرا می‌کند.

```julia
julia> [1,2,3] .^ 3
3-element Vector{Int64}:
  1
  8
 27
```


به طور دقیق‌تر، `a.^b`(این بیان یعنی تک تک المان‌های آرایه `a` به توان المان متناظر آن در آرایه `b` برسد. یعنی المان اول `a` به توان المان اول `b` و المان دوم `a` به توان المان دوم `b` و…) به صورت [فراخوانی نقطه‌ای](@ref man-vectorized)
 `(a,b).(^)` شناخته می‌شود که یک عملیات [گسترش](@ref Broadcasting) را اجرا می‌کند: این عملیات می‌تواند آرایه‌ها و اسکالرها، آرایه‌های هم اندازه (انجام عملیات به صورت المانی) و  حتی میان آرایه‌هایی با اشکال مختلف(مثلاً ترکیب بردار‌های ستونی و ردیفی برای ساخت ماتریس). علاوه بر این، مانند تمام فراخوانی‌های نقطه‌ای برداری(المانی)، این عملگرهای نقطه‌ای نیز همپوش هستند. برای مثال، اگر شما `2 .* A.^2 .+ sin.(A)` (که معادل با `@. 2A^2 + sin(A)` است و ماکروی [`.@`](@ref @__dot__) macro) در آن استفاده شده است) را برای آرایه‌ای مانند `A` محاسبه کنید، یک تک حلقه روی `A` اجرا می‌شود که `2a^2 + sin(a)` را برای هر المان از `A` محاسبه می‌کند. به طور خاص، فراخوانی نقطه‌ای تودرتو مانند `f.(g.(x))` همپوشانی شده‌ است و این یعنی عملگرهای باینری مجاور مانند `x.+ 3.*x.^2` با فراخوانی نقطه تودرتوی `(+).(x, (*).(3, (^).(x, 2)))` معادل هستند.

علاوه بر این‌ موارد، عملگرهای بروز‌رسان نقطه‌ای مانند `a.+=b` (یا `a+=b.@`) به صورت `a.=a.+b` اجرا می‌شود، که در آن `=.` یک عملیات تخصیص در محل همپوشانی شده است (نوشته [نحو نقطه‌ای]((@ref man-vectorized)) را ببینید).

دقت کنید که نحو(سینتکس) نقطه‌ای برای عملگرهای تعریف شده توسط کاربر نیز قابل استفاده است. برای مثال اگر شما `⊗(A,B) = kron(A,B)` را به منظور عمل  `A ⊗ B` برای داشتن یک سینتکس راحت برای ضرب کرونر تعریف کنید، پس از آن بدون هیچ کد اضافه‌ای شما می‌توانید `[C,D]⊗.[A,B]` را برای محاسبه `[A⊗C,B⊗D]` بیان کنید. 

ترکیب عملگرهای نقطه‌ای با لیترال‌های عددی می‌تواند ابهام آور باشد. به طور مثال، معلوم نیست که منظور از عبارت `1.+x` به صورت `1. + x` است یا `1 .+ x`. در نتیجه این نوع نحوه بیان مجاز نیست و حتماً باید بوسیله فضای خالی میان عملگر نقطه و عملوندهایش در چنین مواردی فاصله ایجاد کنید.

## مقایسات عددی

عملگرهای مقایسه‌ای استاندارد برای تمام تایپ‌های عددی اولیه تعریف شده‌اند:

| عملگر                     | تووضیحات                     |
|:---------------------------- |:------------------------ |
| `==`                 | برابری                 |
| `!=`, [`≠`](@ref !=) | نابرابری               |
| `<`                  | کوچکتر                 |
| `<=`, [`≤`](@ref <=) | کوچتر یا مساوی    |
| `>`                  | بزرگتر              |
| `>=`, [`≥`](@ref >=) | بزرگتر یا مساوی |

در اینجا چند مثال ساده را مشاهده می‌کنید:

```julia
julia> 1 == 1
true

julia> 1 == 2
false

julia> 1 != 2
true

julia> 1 == 1.0
true

julia> 1 < 2
true

julia> 1.0 > 3
false

julia> 1 >= 1.0
true

julia> -1 <= 1
true

julia> -1 <= -1
true

julia> -1 <= -2
false

julia> 3 < -0.5
false
```


اعداد صحیح به صورت استاندارد با مقایسه‌ی بیت‌ها مورد سنجش قرار داده می‌شوند. اعداد اعشاری نیز با توجه به [استاندارد IEEE 754](https://en.wikipedia.org/wiki/IEEE_754-2008) مقایسه می شوند:

+ ترتیب اعداد محدود، به صورت معمولی است.
+ صفر مثبت برابر است با صفر منفی اما از آن بزرگتر نیست.
+ `Inf` با خودش برابر و از هر چیز دیگری بجز  `NaN` بزرگتر است.
+ `Inf` با خودش برابر و از هر چیز دیگری بجز `NaN` کوچکتر است.
+ `NaN` نه با چیزی برابر، نه از چیزی بزرگتر و نه کوچکتر است. این گزاره شامل خودش هم می‌شود.

نکته آخر بسیار شگفت انگیز است و ارزش دقت دارد:

```julia
julia> NaN == NaN
false

julia> NaN != NaN
true

julia> NaN < NaN
false

julia> NaN > NaN
false
```


این نکته در کار با [آرایه‌ها](@ref man-multi-dim-arrays) می‌تواند موجب کمی سردرد شود:

```julia
julia> [1 NaN] == [1 NaN]
false
```


جولیا توابعی اضافی برای آزمون اعداد در مقادیر خاص فراهم کرده است که می‌تواند در موقعیت‌هایی مانند مقایسه‌‌های کلید هش استفاده شود:

| تابع                | تست می‌کند که                  |
|:----------------------- |:------------------------- |
| `isequal(x, y)` | آیا `x` و `x` یکسان هستند؟ |
| `isfinite(x)`   | آیا `x` یک عدد متناهی است؟    |
| `isinf(x)`      | آیا `x` بی‌نهایت است؟           |
| `isnan(x)`      | آیا `x` یک عدد نیست؟       |

`isequal` متغیر `NaN` را برابر با خودش در نظر می‌گیرد:

```julia
julia> isequal(NaN, NaN)
true

julia> isequal([1 NaN], [1 NaN])
true

julia> isequal(NaN, NaN32)
true
```


`isequal` همچنین می‌تواند برای تمییز دادن صفرهای علامت‌دار استفاده شود:

```julia
julia> -0.0 == 0.0
true

julia> isequal(-0.0, 0.0)
false
```


مقایسات ترکیبی میان اعداد صحیح علامت‌دار، اعداد صحیح بدون علامت و اعداد اعشاری می‌تواند گول زننده باشد. مراقبت‌های زیادی انجام شده است تا اطمینان حاصل شود که جولیا آن‌ها را به درستی انجام می‌دهد.

برای بقیه تایپ‌ها، `isequal` به صورت پیشفرض `==` را صدا می‌زند؛ بنابراین اگر شما می‌خواهید برابری برای تایپ‌های خود را بسنجید، کافیست از متد `==` استفاده کنید. اگر شما می‌خواهید تابع سنجش برابری خودتان را تعریف کنید، بهتر است یک تابع `hash` متناظر تعریف کنید تا اطمینان شود که `(isequal(x,y` برابری `(hash(x)==hash(y` را می‌رساند.

### مقایسه‌های زنجیره‌ای

بر خلاف بسیاری از زبان ها [به استثنای پایتون](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators)، مقایسه‌ها می‌توانند به طور دلخواه به صورت زنجیره‌ای استفاده شوند:

```julia
julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
true
```


استفاده از مقایسه‌های زنجیره‌ای در کدهای عددی، اغلب باعث راحتی بسیاری می‌شود. این مقایسه‌ها از عملگر `&&` برای سنجش‌های اسکالر و از عملگر `&` برای سنجش‌های المانی استفاده می‌کنند که این کار اجازه می‌دهد تا بر روی آرایه‌ها نیز عمل کنند. برای مثال عبارت  `0 .< A .< 1` یک ارایه بولی نتیجه می‌دهد که درایه‌هایی که true هستند، نشان می‌دهند که درایه‌‌های متناظر آن در `A` بین صفر و یک هستند.

به نحوه‌ی رفتار ارزیابی در مقایسه‌های زنجیره‌ای توجه داشته باشید:

```julia
julia> v(x) = (println(x); x)
v (generic function with 1 method)

julia> v(1) < v(2) <= v(3)
2
1
3
true

julia> v(1) > v(2) <= v(3)
2
1
false
```


عبارتی که در وسط آمده است فقط یک بار ارزیابی می‌شود، که اگر عبارت به صورت  `v(1) < v(2) && v(2) <= v(3)` نوشته شده‌بود، دوبار ارزیابی می‌شد. با این حال، ترتیب ارزیابی‌ها در یک مقایسه زنجیره‌ای تعریف نشده است. به شدت توصیه می‌شود که از عباراتی با عوارض جانبی (مانند چاپ) در مقایسه‌های زنجیره‌ای بپرهیزید. اگر به عباراتی برای عوارض جانبی نیاز داشتید، از عملگر `&&` اتصال-کوتاه به طور صریح استفاده کنید (نوشته ارزیابی اتصال-کوتاه را ببینید).

### Elementary Functions

Julia provides a comprehensive collection of mathematical functions and operators. These mathematical
operations are defined over as broad a class of numerical values as permit sensible definitions,
including integers, floating-point numbers, rationals, and complex numbers,
wherever such definitions make sense.

Moreover, these functions (like any Julia function) can be applied in "vectorized" fashion to
arrays and other collections with the [dot syntax](@ref man-vectorized) `f.(A)`,
e.g. `sin.(A)` will compute the sine of each element of an array `A`.

## Operator Precedence and Associativity

Julia applies the following order and associativity of operations, from highest precedence to lowest:

| Category       | Operators                                                                                         | Associativity              |
|:-------------- |:------------------------------------------------------------------------------------------------- |:-------------------------- |
| Syntax         | `.` followed by `::`                                                                              | Left                       |
| Exponentiation | `^`                                                                                               | Right                      |
| Unary          | `+ - √`                                                                                           | Right[^1]                  |
| Bitshifts      | `<< >> >>>`                                                                                       | Left                       |
| Fractions      | `//`                                                                                              | Left                       |
| Multiplication | `* / % & \ ÷`                                                                                     | Left[^2]                   |
| Addition       | `+ - \| ⊻`                                                                                        | Left[^2]                   |
| Syntax         | `: ..`                                                                                            | Left                       |
| Syntax         | `\|>`                                                                                             | Left                       |
| Syntax         | `<\|`                                                                                             | Right                      |
| Comparisons    | `> < >= <= == === != !== <:`                                                                      | Non-associative            |
| Control flow   | `&&` followed by `\|\|` followed by `?`                                                           | Right                      |
| Pair           | `=>`                                                                                              | Right                      |
| Assignments    | `= += -= *= /= //= \= ^= ÷= %= \|= &= ⊻= <<= >>= >>>=`                                            | Right                      |

[^1]:
    The unary operators `+` and `-` require explicit parentheses around their argument to disambiguate them from the operator `++`, etc. Other compositions of unary operators are parsed with right-associativity, e. g., `√√-a` as `√(√(-a))`.
[^2]:
    The operators `+`, `++` and `*` are non-associative. `a + b + c` is parsed as `+(a, b, c)` not `+(+(a, b),
    c)`. However, the fallback methods for `+(a, b, c, d...)` and `*(a, b, c, d...)` both default to left-associative evaluation.

For a complete list of *every* Julia operator's precedence, see the top of this file:
[`src/julia-parser.scm`](https://github.com/JuliaLang/julia/blob/master/src/julia-parser.scm). Note that some of the operators there are not defined
in the `Base` module but may be given definitions by standard libraries, packages or user code.

You can also find the numerical precedence for any given operator via the built-in function `Base.operator_precedence`, where higher numbers take precedence:

```julia
julia> Base.operator_precedence(:+), Base.operator_precedence(:*), Base.operator_precedence(:.)
(11, 12, 17)

julia> Base.operator_precedence(:sin), Base.operator_precedence(:+=), Base.operator_precedence(:(=))  # (Note the necessary parens on `:(=)`)
(0, 1, 1)
```

A symbol representing the operator associativity can also be found by calling the built-in function `Base.operator_associativity`:

```julia
julia> Base.operator_associativity(:-), Base.operator_associativity(:+), Base.operator_associativity(:^)
(:left, :none, :right)

julia> Base.operator_associativity(:⊗), Base.operator_associativity(:sin), Base.operator_associativity(:→)
(:left, :none, :right)
```

Note that symbols such as `:sin` return precedence `0`. This value represents invalid operators and not
operators of lowest precedence. Similarly, such operators are assigned associativity `:none`.

[Numeric literal coefficients](@ref man-numeric-literal-coefficients), e.g. `2x`, are treated as multiplications with higher precedence than any other binary operation, with the exception of `^` where they have higher precedence only as the exponent.

```julia
julia> x = 3; 2x^2
18

julia> x = 3; 2^2x
64
```

Juxtaposition parses like a unary operator, which has the same natural asymmetry around exponents: `-x^y` and `2x^y` parse as `-(x^y)` and `2(x^y)` whereas `x^-y` and `x^2y` parse as `x^(-y)` and `x^(2y)`.

## Numerical Conversions

Julia supports three forms of numerical conversion, which differ in their handling of inexact
conversions.

  * The notation `T(x)` or `convert(T,x)` converts `x` to a value of type `T`.

      * If `T` is a floating-point type, the result is the nearest representable value, which could be
        positive or negative infinity.
      * If `T` is an integer type, an `InexactError` is raised if `x` is not representable by `T`.
  * `x % T` converts an integer `x` to a value of integer type `T` congruent to `x` modulo `2^n`,
    where `n` is the number of bits in `T`. In other words, the binary representation is truncated
    to fit.
  * The Rounding functions take a type `T` as an optional argument. For example, `round(Int,x)`
    is a shorthand for `Int(round(x))`.

The following examples show the different forms.

```julia
julia> Int8(127)
127

julia> Int8(128)
ERROR: InexactError: trunc(Int8, 128)
Stacktrace:
[...]

julia> Int8(127.0)
127

julia> Int8(3.14)
ERROR: InexactError: Int8(3.14)
Stacktrace:
[...]

julia> Int8(128.0)
ERROR: InexactError: Int8(128.0)
Stacktrace:
[...]

julia> 127 % Int8
127

julia> 128 % Int8
-128

julia> round(Int8,127.4)
127

julia> round(Int8,127.6)
ERROR: InexactError: trunc(Int8, 128.0)
Stacktrace:
[...]
```

See [Conversion and Promotion](@ref conversion-and-promotion) for how to define your own conversions and promotions.

### Rounding functions

| Function              | Description                      | Return type |
|:--------------------- |:-------------------------------- |:----------- |
| `round(x)`    | round `x` to the nearest integer | `typeof(x)` |
| `round(T, x)` | round `x` to the nearest integer | `T`         |
| `floor(x)`    | round `x` towards `-Inf`         | `typeof(x)` |
| `floor(T, x)` | round `x` towards `-Inf`         | `T`         |
| `ceil(x)`     | round `x` towards `+Inf`         | `typeof(x)` |
| `ceil(T, x)`  | round `x` towards `+Inf`         | `T`         |
| `trunc(x)`    | round `x` towards zero           | `typeof(x)` |
| `trunc(T, x)` | round `x` towards zero           | `T`         |

### Division functions

| Function                  | Description                                                                                               |
|:------------------------- |:--------------------------------------------------------------------------------------------------------- |
| `div(x,y)`, `x÷y` | truncated division; quotient rounded towards zero                                                         |
| `fld(x,y)`        | floored division; quotient rounded towards `-Inf`                                                         |
| `cld(x,y)`        | ceiling division; quotient rounded towards `+Inf`                                                         |
| `rem(x,y)`        | remainder; satisfies `x == div(x,y)*y + rem(x,y)`; sign matches `x`                                       |
| `mod(x,y)`        | modulus; satisfies `x == fld(x,y)*y + mod(x,y)`; sign matches `y`                                         |
| `mod1(x,y)`       | `mod` with offset 1; returns `r∈(0,y]` for `y>0` or `r∈[y,0)` for `y<0`, where `mod(r, y) == mod(x, y)`   |
| `mod2pi(x)`       | modulus with respect to 2pi;  `0 <= mod2pi(x) < 2pi`                                                      |
| `divrem(x,y)`     | returns `(div(x,y),rem(x,y))`                                                                             |
| `fldmod(x,y)`     | returns `(fld(x,y),mod(x,y))`                                                                             |
| `gcd(x,y...)`     | greatest positive common divisor of `x`, `y`,...                                                          |
| `lcm(x,y...)`     | least positive common multiple of `x`, `y`,...                                                            |

### Sign and absolute value functions

| Function                | Description                                                |
|:----------------------- |:---------------------------------------------------------- |
| `abs(x)`        | a positive value with the magnitude of `x`                 |
| `abs2(x)`       | the squared magnitude of `x`                               |
| `sign(x)`       | indicates the sign of `x`, returning -1, 0, or +1          |
| `signbit(x)`    | indicates whether the sign bit is on (true) or off (false) |
| `copysign(x,y)` | a value with the magnitude of `x` and the sign of `y`      |
| `flipsign(x,y)` | a value with the magnitude of `x` and the sign of `x*y`    |

### Powers, logs and roots

| Function                 | Description                                                                |
|:------------------------ |:-------------------------------------------------------------------------- |
| `sqrt(x)`, `√x`  | square root of `x`                                                         |
| `cbrt(x)`, `∛x`  | cube root of `x`                                                           |
| `hypot(x,y)`     | hypotenuse of right-angled triangle with other sides of length `x` and `y` |
| `exp(x)`         | natural exponential function at `x`                                        |
| `expm1(x)`       | accurate `exp(x)-1` for `x` near zero                                      |
| `ldexp(x,n)`     | `x*2^n` computed efficiently for integer values of `n`                     |
| `log(x)`         | natural logarithm of `x`                                                   |
| `log(b,x)`       | base `b` logarithm of `x`                                                  |
| `log2(x)`        | base 2 logarithm of `x`                                                    |
| `log10(x)`       | base 10 logarithm of `x`                                                   |
| `log1p(x)`       | accurate `log(1+x)` for `x` near zero                                      |
| `exponent(x)`    | binary exponent of `x`                                                     |
| `significand(x)` | binary significand (a.k.a. mantissa) of a floating-point number `x`        |

For an overview of why functions like `hypot`, `expm1`, and `log1p`
are necessary and useful, see John D. Cook's excellent pair of blog posts on the subject: [expm1, log1p, erfc](https://www.johndcook.com/blog/2010/06/07/math-library-functions-that-seem-unnecessary/),
and [hypot](https://www.johndcook.com/blog/2010/06/02/whats-so-hard-about-finding-a-hypotenuse/).

### Trigonometric and hyperbolic functions

All the standard trigonometric and hyperbolic functions are also defined:

```
sin    cos    tan    cot    sec    csc
sinh   cosh   tanh   coth   sech   csch
asin   acos   atan   acot   asec   acsc
asinh  acosh  atanh  acoth  asech  acsch
sinc   cosc
```

These are all single-argument functions, with `atan` also accepting two arguments
corresponding to a traditional [`atan2`](https://en.wikipedia.org/wiki/Atan2) function.

Additionally, `sinpi(x)` and `cospi(x)` are provided for more accurate computations
of `sin(pi*x)` and `cos(pi*x)` respectively.

In order to compute trigonometric functions with degrees instead of radians, suffix the function
with `d`. For example, `sind(x)` computes the sine of `x` where `x` is specified in degrees.
The complete list of trigonometric functions with degree variants is:

```
sind   cosd   tand   cotd   secd   cscd
asind  acosd  atand  acotd  asecd  acscd
```

### Special functions

Many other special mathematical functions are provided by the package
[SpecialFunctions.jl](https://github.com/JuliaMath/SpecialFunctions.jl).
