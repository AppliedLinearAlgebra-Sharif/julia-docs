# Integers and Floating-Point Numbers

Integers and floating-point values are the basic building blocks of arithmetic and computation.
Built-in representations of such values are called numeric primitives, while representations of
integers and floating-point numbers as immediate values in code are known as numeric literals.
For example, `1` is an integer literal, while `1.0` is a floating-point literal; their binary
in-memory representations as objects are numeric primitives.

Julia provides a broad range of primitive numeric types, and a full complement of arithmetic and
bitwise operators as well as standard mathematical functions are defined over them. These map
directly onto numeric types and operations that are natively supported on modern computers, thus
allowing Julia to take full advantage of computational resources. Additionally, Julia provides
software support for Arbitrary Precision Arithmetic, which can handle operations on numeric
values that cannot be represented effectively in native hardware representations, but at the cost
of relatively slower performance.

The following are Julia's primitive numeric types:

  * **Integer types:**

| Type              | Signed? | Number of bits | Smallest value | Largest value |
|:----------------- |:------- |:-------------- |:-------------- |:------------- |
| `Int8`    | ✓       | 8              | -2^7           | 2^7 - 1       |
| `UInt8`   |         | 8              | 0              | 2^8 - 1       |
| `Int16`   | ✓       | 16             | -2^15          | 2^15 - 1      |
| `UInt16`  |         | 16             | 0              | 2^16 - 1      |
| `Int32`   | ✓       | 32             | -2^31          | 2^31 - 1      |
| `UInt32`  |         | 32             | 0              | 2^32 - 1      |
| `Int64`   | ✓       | 64             | -2^63          | 2^63 - 1      |
| `UInt64`  |         | 64             | 0              | 2^64 - 1      |
| `Int128`  | ✓       | 128            | -2^127         | 2^127 - 1     |
| `UInt128` |         | 128            | 0              | 2^128 - 1     |
| `Bool`    | N/A     | 8              | `false` (0)    | `true` (1)    |

  * **Floating-point types:**

| Type              | Precision                                                                      | Number of bits |
|:----------------- |:------------------------------------------------------------------------------ |:-------------- |
| `Float16` | [half](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)     | 16             |
| `Float32` | [single](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |
| `Float64` | [double](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |

Additionally, full support for Complex and Rational Numbers is built on top of these primitive
numeric types. All numeric types interoperate naturally without explicit casting, thanks to a
flexible, user-extensible [type promotion system](@ref conversion-and-promotion).

## Integers

Literal integers are represented in the standard manner:

```julia
julia> 1
1

julia> 1234
1234
```

The default type for an integer literal depends on whether the target system has a 32-bit architecture
or a 64-bit architecture:

```julia
# 32-bit system:
julia> typeof(1)
Int32

# 64-bit system:
julia> typeof(1)
Int64
```

The Julia internal variable `Sys.WORD_SIZE` indicates whether the target system is 32-bit
or 64-bit:

```julia
# 32-bit system:
julia> Sys.WORD_SIZE
32

# 64-bit system:
julia> Sys.WORD_SIZE
64
```

Julia also defines the types `Int` and `UInt`, which are aliases for the system's signed and unsigned
native integer types respectively:

```julia
# 32-bit system:
julia> Int
Int32
julia> UInt
UInt32

# 64-bit system:
julia> Int
Int64
julia> UInt
UInt64
```

Larger integer literals that cannot be represented using only 32 bits but can be represented in
64 bits always create 64-bit integers, regardless of the system type:

```julia
# 32-bit or 64-bit system:
julia> typeof(3000000000)
Int64
```

Unsigned integers are input and output using the `0x` prefix and hexadecimal (base 16) digits
`0-9a-f` (the capitalized digits `A-F` also work for input). The size of the unsigned value is
determined by the number of hex digits used:

```julia
julia> x = 0x1
0x01

julia> typeof(x)
UInt8

julia> x = 0x123
0x0123

julia> typeof(x)
UInt16

julia> x = 0x1234567
0x01234567

julia> typeof(x)
UInt32

julia> x = 0x123456789abcdef
0x0123456789abcdef

julia> typeof(x)
UInt64

julia> x = 0x11112222333344445555666677778888
0x11112222333344445555666677778888

julia> typeof(x)
UInt128
```

This behavior is based on the observation that when one uses unsigned hex literals for integer
values, one typically is using them to represent a fixed numeric byte sequence, rather than just
an integer value.

Binary and octal literals are also supported:

```julia
julia> x = 0b10
0x02

julia> typeof(x)
UInt8

julia> x = 0o010
0x08

julia> typeof(x)
UInt8

julia> x = 0x00000000000000001111222233334444
0x00000000000000001111222233334444

julia> typeof(x)
UInt128
```

As for hexadecimal literals, binary and octal literals produce unsigned integer types. The size
of the binary data item is the minimal needed size, if the leading digit of the literal is not
`0`. In the case of leading zeros, the size is determined by the minimal needed size for a
literal, which has the same length but leading digit `1`. That allows the user to control
the size.
Values which cannot be stored in `UInt128` cannot be written as such literals.

Binary, octal, and hexadecimal literals may be signed by a `-` immediately preceding the
unsigned literal. They produce an unsigned integer of the same size as the unsigned literal
would do, with the two's complement of the value:

```julia
julia> -0x2
0xfe

julia> -0x0002
0xfffe
```

The minimum and maximum representable values of primitive numeric types such as integers are given
by the `typemin` and `typemax` functions:

```julia
julia> (typemin(Int32), typemax(Int32))
(-2147483648, 2147483647)

julia> for T in [Int8,Int16,Int32,Int64,Int128,UInt8,UInt16,UInt32,UInt64,UInt128]
           println("$(lpad(T,7)): [$(typemin(T)),$(typemax(T))]")
       end
   Int8: [-128,127]
  Int16: [-32768,32767]
  Int32: [-2147483648,2147483647]
  Int64: [-9223372036854775808,9223372036854775807]
 Int128: [-170141183460469231731687303715884105728,170141183460469231731687303715884105727]
  UInt8: [0,255]
 UInt16: [0,65535]
 UInt32: [0,4294967295]
 UInt64: [0,18446744073709551615]
UInt128: [0,340282366920938463463374607431768211455]
```

The values returned by `typemin` and `typemax` are always of the given argument
type. (The above expression uses several features that have yet to be introduced, including [for loops](@ref man-loops),
[Strings](@ref man-strings), and [Interpolation](@ref string-interpolation), but should be easy enough to understand for users
with some existing programming experience.)

### Overflow behavior

In Julia, exceeding the maximum representable value of a given type results in a wraparound behavior:

```julia
julia> x = typemax(Int64)
9223372036854775807

julia> x + 1
-9223372036854775808

julia> x + 1 == typemin(Int64)
true
```

Thus, arithmetic with Julia integers is actually a form of [modular arithmetic](https://en.wikipedia.org/wiki/Modular_arithmetic).
This reflects the characteristics of the underlying arithmetic of integers as implemented on modern
computers. In applications where overflow is possible, explicit checking for wraparound produced
by overflow is essential; otherwise, the `BigInt` type in Arbitrary Precision Arithmetic
is recommended instead.

An example of overflow behavior and how to potentially resolve it is as follows:

```julia
julia> 10^19
-8446744073709551616

julia> big(10)^19
10000000000000000000
```

### Division errors

Integer division (the `div` function) has two exceptional cases: dividing by zero, and dividing
the lowest negative number (`typemin`) by -1. Both of these cases throw a `DivideError`.
The remainder and modulus functions (`rem` and `mod`) throw a `DivideError` when their
second argument is zero.

## Floating-Point Numbers

Literal floating-point numbers are represented in the standard formats, using
[E-notation](https://en.wikipedia.org/wiki/Scientific_notation#E_notation) when necessary:

```julia
julia> 1.0
1.0

julia> 1.
1.0

julia> 0.5
0.5

julia> .5
0.5

julia> -1.23
-1.23

julia> 1e10
1.0e10

julia> 2.5e-4
0.00025
```

The above results are all `Float64` values. Literal `Float32` values can be
entered by writing an `f` in place of `e`:

```julia
julia> x = 0.5f0
0.5f0

julia> typeof(x)
Float32

julia> 2.5f-4
0.00025f0
```

Values can be converted to `Float32` easily:

```julia
julia> x = Float32(-1.5)
-1.5f0

julia> typeof(x)
Float32
```

Hexadecimal floating-point literals are also valid, but only as `Float64` values,
with `p` preceding the base-2 exponent:

```julia
julia> 0x1p0
1.0

julia> 0x1.8p3
12.0

julia> x = 0x.4p-1
0.125

julia> typeof(x)
Float64
```

Half-precision floating-point numbers are also supported (`Float16`), but they are
implemented in software and use `Float32` for calculations.

```julia
julia> sizeof(Float16(4.))
2

julia> 2*Float16(4.)
Float16(8.0)
```

The underscore `_` can be used as digit separator:

```julia
julia> 10_000, 0.000_000_005, 0xdead_beef, 0b1011_0010
(10000, 5.0e-9, 0xdeadbeef, 0xb2)
```

### Floating-point zero

Floating-point numbers have [two zeros](https://en.wikipedia.org/wiki/Signed_zero), positive zero
and negative zero. They are equal to each other but have different binary representations, as
can be seen using the `bitstring` function:

```julia
julia> 0.0 == -0.0
true

julia> bitstring(0.0)
"0000000000000000000000000000000000000000000000000000000000000000"

julia> bitstring(-0.0)
"1000000000000000000000000000000000000000000000000000000000000000"
```

### Special floating-point values

There are three specified standard floating-point values that do not correspond to any point on
the real number line:

| `Float16` | `Float32` | `Float64` | Name              | Description                                                     |
|:--------- |:--------- |:--------- |:----------------- |:--------------------------------------------------------------- |
| `Inf16`   | `Inf32`   | `Inf`     | positive infinity | a value greater than all finite floating-point values           |
| `-Inf16`  | `-Inf32`  | `-Inf`    | negative infinity | a value less than all finite floating-point values              |
| `NaN16`   | `NaN32`   | `NaN`     | not a number      | a value not `==` to any floating-point value (including itself) |

For further discussion of how these non-finite floating-point values are ordered with respect
to each other and other floats, see Numeric Comparisons. By the [IEEE 754 standard](https://en.wikipedia.org/wiki/IEEE_754-2008),
these floating-point values are the results of certain arithmetic operations:

```julia
julia> 1/Inf
0.0

julia> 1/0
Inf

julia> -5/0
-Inf

julia> 0.000001/0
Inf

julia> 0/0
NaN

julia> 500 + Inf
Inf

julia> 500 - Inf
-Inf

julia> Inf + Inf
Inf

julia> Inf - Inf
NaN

julia> Inf * Inf
Inf

julia> Inf / Inf
NaN

julia> 0 * Inf
NaN
```

The `typemin` and `typemax` functions also apply to floating-point types:

```julia
julia> (typemin(Float16),typemax(Float16))
(-Inf16, Inf16)

julia> (typemin(Float32),typemax(Float32))
(-Inf32, Inf32)

julia> (typemin(Float64),typemax(Float64))
(-Inf, Inf)
```

### Machine epsilon

Most real numbers cannot be represented exactly with floating-point numbers, and so for many purposes
it is important to know the distance between two adjacent representable floating-point numbers,
which is often known as [machine epsilon](https://en.wikipedia.org/wiki/Machine_epsilon).

Julia provides `eps`, which gives the distance between `1.0` and the next larger representable
floating-point value:

```julia
julia> eps(Float32)
1.1920929f-7

julia> eps(Float64)
2.220446049250313e-16

julia> eps() # same as eps(Float64)
2.220446049250313e-16
```

These values are `2.0^-23` and `2.0^-52` as `Float32` and `Float64` values,
respectively. The `eps` function can also take a floating-point value as an
argument, and gives the absolute difference between that value and the next representable
floating point value. That is, `eps(x)` yields a value of the same type as `x` such that
`x + eps(x)` is the next representable floating-point value larger than `x`:

```julia
julia> eps(1.0)
2.220446049250313e-16

julia> eps(1000.)
1.1368683772161603e-13

julia> eps(1e-27)
1.793662034335766e-43

julia> eps(0.0)
5.0e-324
```

The distance between two adjacent representable floating-point numbers is not constant, but is
smaller for smaller values and larger for larger values. In other words, the representable floating-point
numbers are densest in the real number line near zero, and grow sparser exponentially as one moves
farther away from zero. By definition, `eps(1.0)` is the same as `eps(Float64)` since `1.0` is
a 64-bit floating-point value.

Julia also provides the `nextfloat` and `prevfloat` functions which return
the next largest or smallest representable floating-point number to the argument respectively:

```julia
julia> x = 1.25f0
1.25f0

julia> nextfloat(x)
1.2500001f0

julia> prevfloat(x)
1.2499999f0

julia> bitstring(prevfloat(x))
"00111111100111111111111111111111"

julia> bitstring(x)
"00111111101000000000000000000000"

julia> bitstring(nextfloat(x))
"00111111101000000000000000000001"
```

This example highlights the general principle that the adjacent representable floating-point numbers
also have adjacent binary integer representations.

### Rounding modes

If a number doesn't have an exact floating-point representation, it must be rounded to an
appropriate representable value. However, the manner in which this rounding is done can be
changed if required according to the rounding modes presented in the [IEEE 754
standard](https://en.wikipedia.org/wiki/IEEE_754-2008).

The default mode used is always `RoundNearest`, which rounds to the nearest representable
value, with ties rounded towards the nearest value with an even least significant bit.

### Background and References

Floating-point arithmetic entails many subtleties which can be surprising to users who are unfamiliar
with the low-level implementation details. However, these subtleties are described in detail in
most books on scientific computation, and also in the following references:

  * The definitive guide to floating point arithmetic is the [IEEE 754-2008 Standard](https://standards.ieee.org/standard/754-2008.html);
    however, it is not available for free online.
  * For a brief but lucid presentation of how floating-point numbers are represented, see John D.
    Cook's [article](https://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/)
    on the subject as well as his [introduction](https://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/)
    to some of the issues arising from how this representation differs in behavior from the idealized
    abstraction of real numbers.
  * Also recommended is Bruce Dawson's [series of blog posts on floating-point numbers](https://randomascii.wordpress.com/2012/05/20/thats-not-normalthe-performance-of-odd-floats/).
  * For an excellent, in-depth discussion of floating-point numbers and issues of numerical accuracy
    encountered when computing with them, see David Goldberg's paper [What Every Computer Scientist Should Know About Floating-Point Arithmetic](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf).
  * For even more extensive documentation of the history of, rationale for, and issues with floating-point
    numbers, as well as discussion of many other topics in numerical computing, see the [collected writings](https://people.eecs.berkeley.edu/~wkahan/)
    of [William Kahan](https://en.wikipedia.org/wiki/William_Kahan), commonly known as the "Father
    of Floating-Point". Of particular interest may be [An Interview with the Old Man of Floating-Point](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html).

## دقت دلخواه محاسبات

برای انجام محاسبات با دقت دلخواه اعداد صحیح و ممیز شناور، جولیا به ترتیب از کتابخانه‌های [دقت محاسبات چندگانه GNU](https://gmplib.org) و [GNU MPFR](https://www.mpfr.org) استفاده می‌کند. در زبان برنامه نویسی جولیا تایپ‌های `BigInt` و `Big Float` به ترتیب برای دقت دلخواه اعداد صحیح و اعشاری در دسترس هستند.

در زبان برنامه نویسی جولیا، سازندگانی برای ساخت این تایپ‌ها از تایپ‌های اعداد اولیه و لیترال رشته وجود دارند [(@ref non-standard-string-literals) ] و شما می‌توانید از `@big_str` یا  `parse` برای ساخت این تایپ‌ها از `AbstractString`ها استفاده کنید. همچنین `BigInt`ها زمانی که برای سایر تایپ‌های اعداد صحیح موجود خیلی بزرگ باشند، ممکن است به عنوان لیترال اعداد صحیح ورودی داده شوند. توجه داشته باشید که با توجه به اینکه هیچ تایپ عدد صحیح بدون علامت با دقت دلخواه در  `Base`  وجود ندارد (`BigInt` در اکثر موارد کافی است)، هگزادسیمال ، هشت و دودویی می توانند مورد استفاده قرار گیرند (علاوه بر لیترال‌های اعشاری).

به لطف [مکانیسم ارتقاء و تبدیل زبان برنامه نویسی جولیا](@ref conversion-and-promotion) شما می‌توانید پس از ساخت این تایپ‌ها، آن‌ها را در محاسبات با دیگر تایپ‌های عددی جولیا به کار بگیرید:


```julia
julia> BigInt(typemax(Int64)) + 1
9223372036854775808

julia> big"123456789012345678901234567890" + 1
123456789012345678901234567891

julia> parse(BigInt, "123456789012345678901234567890") + 1
123456789012345678901234567891

julia> string(big"2"^200, base=16)
"100000000000000000000000000000000000000000000000000"

julia> 0x100000000000000000000000000000000-1 == typemax(UInt128)
true

julia> 0x000000000000000000000000000000000
0

julia> typeof(ans)
BigInt

julia> big"1.23456789012345678901"
1.234567890123456789010000000000000000000000000000000000000000000000000000000004

julia> parse(BigFloat, "1.23456789012345678901")
1.234567890123456789010000000000000000000000000000000000000000000000000000000004

julia> BigFloat(2.0^66) / 3
2.459565876494606882133333333333333333333333333333333333333333333333333333333344e+19

julia> factorial(BigInt(40))
815915283247897734345611269596115894272000000000
```


توجه داشته باشید که قابلیت ارتقاء تایپ‌ها بین تایپ‌های اولیه بالا و تایپ‌های `BigInt`/`BigFloat` به صورت خود به خود نیست و باید به طور صریح مشخص شوند: 


```julia
julia> x = typemin(Int64)
-9223372036854775808

julia> x = x - 1
9223372036854775807

julia> typeof(x)
Int64

julia> y = BigInt(typemin(Int64))
-9223372036854775808

julia> y = y - 1
-9223372036854775809

julia> typeof(y)
BigInt
```


شما می‌توانید دقت (در تعداد بیت‌های مانتیس) و حالت گرد کردن پیش‌فرض را برای عملیات‌های `BigFloat` به صورت کلی (در سر تا سر برنامه) به کمک صدا زدن `setprecision` و `setrounding` تغییر داده و محاسبات بعدی خود را به وسیله تغییرات ایجاد شده انجام دهید. از طرف دیگر، شما می‌توانید دقت یا گرد کردن را فقط برای یک بلاک خاص از کدتان، تغییر دهید و برای این کار کافی است تا همین توابع را به همراه بلاک `do` مورد استفاده قرار دهید:

```julia
julia> setrounding(BigFloat, RoundUp) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.100000000000000000000000000000000000000000000000000000000000000000000000000003

julia> setrounding(BigFloat, RoundDown) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.099999999999999999999999999999999999999999999999999999999999999999999999999986

julia> setprecision(40) do
           BigFloat(1) + parse(BigFloat, "0.1")
       end
1.1000000000004
```

## ضرایب لیترال عددی

برای همگامی با فرمول‌های مرسوم و کدهای تمیز و سرراست‌تر، در زبان برنامه نویسی جولیا شما می‌توانید ضرایب لیترال متغیرها را مستقیما پیش از متغیرها به منظور عمل ضرب میان آن‌ها به کار بگیرید. با این کار بیان عبارات چند جمله‌ای‌ بسیار مرتب‌تر می‌شود:

```julia
julia> x = 3
3

julia> 2x^2 - 3x + 1
10

julia> 1.5x^2 - .5x + 1
13.0
```


این کار همچنین باعث ظرافت بیشتری در بیان توابع نمایی می‌شود:

```julia
julia> 2^2x
64
```


در بحث تقدم اعمال عملکردهای ریاضی، ضرایب لیترال عددی تقدم نسبتاً کمتری از عملگرهای یگانی (عملگرهایی که فقط روی یک عملوند عمل می‌کنند) همانند منفی کردن دارند. پس `2x-` به صورت `(-2) * x` و `2x√` به صورت `(2√) * x` اعمال می‌شود. با این حال، ضرایب لیترال عددی هنگامی که با یک بیان چند جمله‌ای ترکیب می‌شوند، شبیه به عملگرهای یگانی عمل می‌کنند. مثلاً `2x^3` به صورت `2*(x^3)` عمل می‌کند.

لیترال‌های عددی همچنین به عنوان ضرایب عبارات داخل پرانتز نیز می‌توانند استفاده شوند:

```julia
julia> 2(x-1)^2 - 3(x-1) + 1
3
```


```eval_rst

.. note::
    The precedence of numeric literal coefficients used for implicit
    multiplication is higher than other binary operators such as multiplication
    (`*`), and division (`/`, `\\`, and `//`).  This means, for example, that
    `1 / 2im` equals `-0.5im` and `6 // 2(2 + 1)` equals `1 // 1`.
```


علاوه بر این‌ها، پرانتزها نیز می‌توانند به عنوان ضریب متغیرها به منظور اعمال عمل ضرب میان آن‌ها استفاده شوند:

```julia
julia> (x-1)x
6
```


شما نمی‌توانید دو پرانتز را به منظور اعمال عمل ضرب در کنار یکدیگر بگذارید و همچنین نمی‌توانید یک متغیر را به منظور اعمال عمل ضرب قبل از یک پرانتز بگذارید:

```julia
julia> (x-1)(x+1)
ERROR: MethodError: objects of type Int64 are not callable

julia> x(x+1)
ERROR: MethodError: objects of type Int64 are not callable
```


هر دو عبارت به عنوان کاربرد تابع تفسیر می‌شوند: هر عبارتی که یک لیترال عددی نباشد، زمانی که بلافاصله به دنبال آن یک عبارت پرانتزی بیاید، به عنوان یک تابع اعمال شده روی مقادیر درون پرانتز تفسیر می‌شود (برای اطلاع بیشتر در مورد توابع، بخش توابع را ببینید). بنابراین در هر دو مورد بالا، خطا رخ می‌دهد زیرا که مقدار سمت چپ یک تابع نیست.

عبارات نحوی(سینتکس‌ها) بالا باعث می‌شود تا نامنظمی‌های بصری در نوشتن فرمول‌های ریاضی به صورت رایج، بسیار کاهش پیدا کنند. توجه داشته باشید که برای اعمال عمل ضرب، نباید میان یک ضریب لیترال عددی و متغیر یا پرانتزش، فاصله وجود داشته باشد.

### تضادهای نحوی(سینتکس)

قرار دادن نحو(سینتکس) ضریب‌های لیترال در کنار هم ممکن است با برخی از نحوهای مختلف لیترال‌های عددی تداخل کند: لیترال‌های صحیح هگزادسیمال و نحوه نوشتن مهندسی(علمی) برای لیترال‌های ممیز شناور. برخی از شرایطی که موجب وقوع تداخل‌های نحوی می‌شوند، به این صورت هستند: 

+ عبارت لیترال صحیح هگزادسیمال `0xff` ممکن است به صورت ضرب  لیترال عددی `0` در متغیر `xff` تفسیر شود. تداخل‌های مشابهی از لیترال‌های  اوکتال یا دودویی مانند `0o777` یا `0b01001010` بوجود می‌آیند.
+ عبارت لیترال ممیز شناور `1e10` ممکن است به صورت ضرب لیترال عددی `1` در متغیر `e10` تفسیر شود و با فرم متناظر `E` نیز همچنین.
+ عبارت لیترال ممیز شناور 32بیتی `1.5f22` ممکن است به صورت ضرب لیترال عددی `1.5` در متغیر `f22` تفسیر شود.

در همه این گونه موارد، عبارات به صورت لیترال‌های عددی تفسیر می‌شوند:

+ عباراتی که با `0x`/`0o`/`0b` شروع می‌شوند، همیشه لیترال‌های هگزادسیمال، اوکتال و دودویی هستند.
+ عباراتی که با لیترال‌های عددی شروع شده و بعد از آن‌ها `e` یا `E` است، همیشه لیترال‌های ممیز شناور هستند.
+ عباراتی که با لیترال‌های عددی شروع شده و همراهشان `f` است، همیشه لیترال‌های ممیز شناور 32بیتی هستند.

برخلاف `E` که به خاطر دلایل تاریخی متناظر با `e`  در نظر گرفته می‌شود، `F` فقط یکی دیگر از حروف است و مانند `f` در لیترال‌های عددی عمل نمی‌کند. از این رو، عباراتی که با لیترال عددی شروع بشوند و به دنبالش `F` بیاید، به صورت ضرب یک لیترال عددی در یک متغیر تفسیر می‌شود. برای مثال، `1.5F22` که برابر است با `1.5 * F22`.

## لیترال صفر و یک

در زبان برنامه نویسی جولیا توابعی مهیا شده است که لیترال صفر و یک را با توجه به یک تایپ مشخص شده یا تایپ یک متغیر داده شده، باز می‌گردانند.

| تابع          | توضیحات                                      |
|:----------------- |:------------------------------------------------ |
| `zero(x)` | گرفتن لیترال صفر از تایپ `x` یا تایپ متغیر `x`|
| `one(x)` | گرفتن لیترال یک از تایپ `x` یا تایپ متغیر `x`|

این توابع در مقایسه‌های عددی برای جلوگیری از اورهد غیرضروری در [تبدیل تایپ](@ref conversion-and-promotion) به کار می‌آیند.

مثال‌ها:

```julia
julia> zero(Float32)
0.0f0

julia> zero(1.0)
0.0

julia> one(Int32)
1

julia> one(BigFloat)
1.0
```

