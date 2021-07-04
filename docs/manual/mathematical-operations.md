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
| `x && y`   | [اند مدار کوتاه](@ref man-conditional-evaluation) |
| `x \|\| y` | [اور مدار کوتاه](@ref man-conditional-evaluation)  |

عمل منفی کردن `true` را به `false` تبدیل می‌کند و برعکس. عملیات‌های مدار کوتاه در صفحه‌ی لینک‌شده توضیح داده شده‌اند.

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

## Vectorized "dot" operators

For *every* binary operation like `^`, there is a corresponding
"dot" operation `.^` that is *automatically* defined
to perform `^` element-by-element on arrays. For example,
`[1,2,3] ^ 3` is not defined, since there is no standard
mathematical meaning to "cubing" a (non-square) array, but
`[1,2,3] .^ 3` is defined as computing the elementwise
(or "vectorized") result `[1^3, 2^3, 3^3]`.  Similarly for unary
operators like `!` or `√`, there is a corresponding `.√` that
applies the operator elementwise.

```julia
julia> [1,2,3] .^ 3
3-element Vector{Int64}:
  1
  8
 27
```

More specifically, `a .^ b` is parsed as the ["dot" call](@ref man-vectorized)
`(^).(a,b)`, which performs a [broadcast](@ref Broadcasting) operation:
it can combine arrays and scalars, arrays of the same size (performing
the operation elementwise), and even arrays of different shapes (e.g.
combining row and column vectors to produce a matrix). Moreover, like
all vectorized "dot calls," these "dot operators" are
*fusing*. For example, if you compute `2 .* A.^2 .+ sin.(A)` (or
equivalently `@. 2A^2 + sin(A)`, using the [`@.`](@ref @__dot__) macro) for
an array `A`, it performs a *single* loop over `A`, computing `2a^2 + sin(a)`
for each element of `A`. In particular, nested dot calls like `f.(g.(x))`
are fused, and "adjacent" binary operators like `x .+ 3 .* x.^2` are
equivalent to nested dot calls `(+).(x, (*).(3, (^).(x, 2)))`.

Furthermore, "dotted" updating operators like `a .+= b` (or `@. a += b`) are parsed
as `a .= a .+ b`, where `.=` is a fused *in-place* assignment operation
(see the [dot syntax documentation](@ref man-vectorized)).

Note the dot syntax is also applicable to user-defined operators.
For example, if you define `⊗(A,B) = kron(A,B)` to give a convenient
infix syntax `A ⊗ B` for Kronecker products (`kron`), then
`[A,B] .⊗ [C,D]` will compute `[A⊗C, B⊗D]` with no additional coding.

Combining dot operators with numeric literals can be ambiguous.
For example, it is not clear whether `1.+x` means `1. + x` or `1 .+ x`.
Therefore this syntax is disallowed, and spaces must be used around
the operator in such cases.

## Numeric Comparisons

Standard comparison operations are defined for all the primitive numeric types:

| Operator                     | Name                     |
|:---------------------------- |:------------------------ |
| `==`                 | equality                 |
| `!=`, [`≠`](@ref !=) | inequality               |
| `<`                  | less than                |
| `<=`, [`≤`](@ref <=) | less than or equal to    |
| `>`                  | greater than             |
| `>=`, [`≥`](@ref >=) | greater than or equal to |

Here are some simple examples:

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

Integers are compared in the standard manner -- by comparison of bits. Floating-point numbers
are compared according to the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008):

  * Finite numbers are ordered in the usual manner.
  * Positive zero is equal but not greater than negative zero.
  * `Inf` is equal to itself and greater than everything else except `NaN`.
  * `-Inf` is equal to itself and less than everything else except `NaN`.
  * `NaN` is not equal to, not less than, and not greater than anything, including itself.

The last point is potentially surprising and thus worth noting:

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

and can cause headaches when working with [arrays](@ref man-multi-dim-arrays):

```julia
julia> [1 NaN] == [1 NaN]
false
```

Julia provides additional functions to test numbers for special values, which can be useful in
situations like hash key comparisons:

| Function                | Tests if                  |
|:----------------------- |:------------------------- |
| `isequal(x, y)` | `x` and `y` are identical |
| `isfinite(x)`   | `x` is a finite number    |
| `isinf(x)`      | `x` is infinite           |
| `isnan(x)`      | `x` is not a number       |

`isequal` considers `NaN`s equal to each other:

```julia
julia> isequal(NaN, NaN)
true

julia> isequal([1 NaN], [1 NaN])
true

julia> isequal(NaN, NaN32)
true
```

`isequal` can also be used to distinguish signed zeros:

```julia
julia> -0.0 == 0.0
true

julia> isequal(-0.0, 0.0)
false
```

Mixed-type comparisons between signed integers, unsigned integers, and floats can be tricky. A
great deal of care has been taken to ensure that Julia does them correctly.

For other types, `isequal` defaults to calling `==`, so if you want to define
equality for your own types then you only need to add a `==` method.  If you define
your own equality function, you should probably define a corresponding `hash` method
to ensure that `isequal(x,y)` implies `hash(x) == hash(y)`.

### Chaining comparisons

Unlike most languages, with the [notable exception of Python](https://en.wikipedia.org/wiki/Python_syntax_and_semantics#Comparison_operators),
comparisons can be arbitrarily chained:

```julia
julia> 1 < 2 <= 2 < 3 == 3 > 2 >= 1 == 1 < 3 != 5
true
```

Chaining comparisons is often quite convenient in numerical code. Chained comparisons use the
`&&` operator for scalar comparisons, and the `&` operator for elementwise comparisons,
which allows them to work on arrays. For example, `0 .< A .< 1` gives a boolean array whose entries
are true where the corresponding elements of `A` are between 0 and 1.

Note the evaluation behavior of chained comparisons:

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

The middle expression is only evaluated once, rather than twice as it would be if the expression
were written as `v(1) < v(2) && v(2) <= v(3)`. However, the order of evaluations in a chained
comparison is undefined. It is strongly recommended not to use expressions with side effects (such
as printing) in chained comparisons. If side effects are required, the short-circuit `&&` operator
should be used explicitly (see Short-Circuit Evaluation).

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
