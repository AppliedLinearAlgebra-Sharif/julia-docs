# اعداد صحیح و ممیز شناور

اعداد صحیح و مقادیر ممیز شناور عناصر اصلی حساب و محاسبه هستند. نحوه نمایش داخلی این مقادیر،  مقدماتی عددی نامیده می‌شود و نمایش اعداد صحیح و اعداد شناور به عنوان مقادیر درجا در کد به عنوان عدد واقعی شناخته می‌شوند. به عنوان مثال، `1` یک عدد صحیح است، در حالی که `1.0` یک مقدار ممیز شناور است. نمایش‌های دودویی آنها در حافظه نیز به صورت اشیا مقدماتی عددی است.

جولیا طیف گسترده ای از انواع عددی اولیه را فراهم می‌کند که بر روی آن‌ها یک مجموعه کامل از عملگرهای حسابی و بیتی و همچنین توابع استاندارد ریاضی نیز تعریف شده‌است. این انواع مستقیماً بر روی انواع عددی و عملیاتی که بطور مستقیم در رایانه‌های مدرن پشتیبانی می‌شوند نگاشته می‌شود و بنابراین به جولیا امکان استفاده کامل از منابع محاسباتی را می‌دهند. علاوه بر این، جولیا به صورت نرم‌افزاری از دقت اعشاری دلخواه پشتیبانی می‌کند. در نتیجه با هزینه نسبتاً کندتر شدن عملکرد، می‌تواند عملیات‌هایی را بر روی مقادیر عددی که نمی‌توانند به طور مناسب در نمایش‌های سخت‌افزاری قرار داده شوند، انجام دهد.

موارد زیر انواع عددی مقدامی جولیا هستند:

  * **انواع اعداد صحیح:**

| نوع              | علامت‌دار? | تعداد بیت | کمترین مقدار | بیشترین مقدار |
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

  * **انواع ممیز شناور:**

| نوع              | دقت                                                                      | تعداد بیت‌ها |
|:----------------- |:------------------------------------------------------------------------------ |:-------------- |
| `Float16` | [half](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)     | 16             |
| `Float32` | [single](https://en.wikipedia.org/wiki/Single_precision_floating-point_format) | 32             |
| `Float64` | [double](https://en.wikipedia.org/wiki/Double_precision_floating-point_format) | 64             |

علاوه بر این، پشتیبانی کامل از اعداد مختلط و کسری نیز با استفاده از این انواع عددی اولیه فراهم شده‌است. به لطف وجود یک سیستم ارتقا نوع انعطاف‌پذیر، همه اعداد بدون تغییر نوع صریح، می‌توانند در کنار یک‌دیگر استفاده شوند.

## اعداد صحیح

اعداد صحیح به صورت استاندارد نشان داده می‌شوند:

```julia
julia> 1
1

julia> 1234
1234
```

نوع پیش فرض برای یک عدد صحیح به این بستگی دارد که سیستم هدف معماری 32 بیتی یا معماری 64 بیتی دارد:

```julia
# 32-bit system:
julia> typeof(1)
Int32

# 64-bit system:
julia> typeof(1)
Int64
```

متغیر داخلی `Sys.WORD_SIZE` نشان می دهد که آیا سیستم هدف 32 بیتی است یا 64 بیتی:

```julia
# 32-bit system:
julia> Sys.WORD_SIZE
32

# 64-bit system:
julia> Sys.WORD_SIZE
64
```

جولیا همچنین انواع `Int` و `UInt` را تعریف می‌کند که به ترتیب مستعاری برای انواع صحیح علامت‌دار و بدون علامت سیستم هستند:

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

عددهای صحیح بزرگت که با استفاده از 32 بیت قابل نمایش نیستند اما می‌توانند در 64 بیت نشان داده شوند، بدون در نظر گرفتن نوع سیستم، همیشه عدد صحیح 64 بیتی ایجاد می‌کنند:
```julia
# 32-bit or 64-bit system:
julia> typeof(3000000000)
Int64
```

اعداد صحیح بدون علامت با استفاده از پیشوند `0x` و ارقام مبنا 16 `0-9a-f` ورودی و خروجی می‌شوند (رقم‌های بزرگ ‍‍`A-F` نیز برای ورودی کار می‌کنند). اندازه مقدار متغیر بدون علامت، با تعداد ارقام استفاده شده مشخص می‌شود:

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

این رفتار بر این اساس استوار است که وقتی کسی از اعداد بدون علامت برای مقادیر صحیح استفاده می‌کند، معمولاً از آنها برای نشان دادن یک دنباله بایت عددی ثابت استفاده می‌کند، نه فقط یک مقدار صحیح.

از اعداد دودویی و مبنا ۸ نیز پشتیبانی می‌شود:

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

مقادیر دودویی، مبنا ۸ و مبنا ۱۶ انواع صحیح بدون علامت را تولید می‌کنند. اگر رقم اول مقدار `0` نباشد، اندازه داده دودویی حداقل اندازه مورد نیاز است. در مورد صفرهای اول عدد، اندازه با حداقل اندازه مورد نیاز برای یک عدد که به جای آن `0`ها، `1` دارد تعیین می‌شود. به این شکل به کاربر اجازه داده می‌شود تا اندازه را کنترل کند. مقادیری را که نمی‌توان در `UInt128` ذخیره کرد، نمی‌توان به صورت چنین مقادیری نوشت.

مقادیر دودویی، مبنا ۸ و مبنا ۱۶ می‌توانند با "-" بلافاصله قبل از مقدار عددی، به صورت علامت‌دار در نظر گرفته شوند. به این صورت یک عدد صحیح بدون علامت و هم‌اندازه با عدد اصلی ایجاد می‌شود که مقدار آن متمم دودویی مقدار اصلی است:

```julia
julia> -0x2
0xfe

julia> -0x0002
0xfffe
```

حداقل و حداکثر مقادیر قابل نمایش از انواع عددی اولیه مانند اعداد صحیح توسط توابع `typemin` و `typemax` به دست می‌آیند:

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

مقادیر برگشتی توسط `typemin` و `typemax` همیشه از نوع آرگومان داده شده هستند. (عبارت فوق از چندین ویژگی استفاده می‌کند که هنوز معرفی نشده‌اند، از جمله حلقه‌ها و رشته‌ها، اما درک آن برای کاربران با کمی تجربه برنامه‌نویسی باید به آسان باشد.)

### رفتار سرریز

در جولیا، عبور از حداکثر مقدار قابل نمایش برای یک نوع خاص منجر به یک رفتار پیچیده می‌شود:

```julia
julia> x = typemax(Int64)
9223372036854775807

julia> x + 1
-9223372036854775808

julia> x + 1 == typemin(Int64)
true
```

بنابراین حساب با اعداد صحیح در جولیا در واقع نوعی [حساب پیمانه‌ای](https://en.wikipedia.org/wiki/Modular_arithmetic)  است. این نشان دهنده خصوصیات محاسبات اعداد صحیحی است که در رایانه‌های مدرن اجرا می‌شود. در برنامه‌هایی که امکان سرریز وجود دارد، بررسی برای وقوع سرریز ضروری است. در غیر این صورت، استفاده از نوع `BigInt` در محاسبات پیشنهاد می‌شود.

نمونه ای از رفتار سرریز و نحوه حل شدن آن به شرح زیر است:

```julia
julia> 10^19
-8446744073709551616

julia> big(10)^19
10000000000000000000
```

### خطاهای تقسیم

تقسیم عدد صحیح (تابع `div`) دارای دو حالت استثنایی است: تقسیم بر صفر و تقسیم کمترین عدد منفی (`typemin`) بر 1. هر دوی این موارد یک `DivideError` ایجاد می‌کنند. توابع باقیمانده و پیمانه (`rem` و `mod`) نیز هنگامی که آرگومان دوم آنها صفر باشد، یک `DivideError` ایجاد می‌کنند.

## اعداد ممیز شناور

در صورت لزوم با استفاده از [نماد E](https://en.wikipedia.org/wiki/Sniversity_notation#E_notation) اعداد متغیر شناور در قالب‌های استاندارد نشان داده می‌شوند:

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

نتایج فوق همه مقادیر `Float64` هستند. مقادیر `Float32` را می‌توان با نوشتن`f` به جای `e` وارد کرد:

```julia
julia> x = 0.5f0
0.5f0

julia> typeof(x)
Float32

julia> 2.5f-4
0.00025f0
```

مقادیر را می‌توان به راحتی به `Float32` تبدیل کرد:

```julia
julia> x = Float32(-1.5)
-1.5f0

julia> typeof(x)
Float32
```

اعداد ممیز شناور مبنا ۱۶ نیز (فقط به عنوان مقادیر `Float64` با `p` قبل از نما در مبنا ۲) معتبر هستند:

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

اعداد ممیز شناور `Half-precision` نیز پشتیبانی می‌شوند (`Float16`)، اما آنها در نرم افزار پیاده سازی می‌شوند و برای محاسبات از`Float32` استفاده می‌کنند.

```julia
julia> sizeof(Float16(4.))
2

julia> 2*Float16(4.)
Float16(8.0)
```

زیر خط `_` می‌تواند به عنوان جدا کننده رقم نیز استفاده شود:

```julia
julia> 10_000, 0.000_000_005, 0xdead_beef, 0b1011_0010
(10000, 5.0e-9, 0xdeadbeef, 0xb2)
```

### ممیز شناور صفر

اعداد ممیز شناور [دو صفر (صفر مثبت و صفر منفی)](https://en.wikipedia.org/wiki/Signed_zero) دارند. آنها با یکدیگر برابر هستند اما نمایش دودویی متفاوتی دارند، همانطور که با استفاده از تابع `bitstring` مشاهده می‌شود:

```julia
julia> 0.0 == -0.0
true

julia> bitstring(0.0)
"0000000000000000000000000000000000000000000000000000000000000000"

julia> bitstring(-0.0)
"1000000000000000000000000000000000000000000000000000000000000000"
```

### مقادیر ممیز شناور خاص

سه مقدار استاندارد ممیز شناور مشخص وجود دارند که با هیچ نقطه‌ای از محور اعداد حقیقی مطابقت ندارند:

| `Float16` | `Float32` | `Float64` | Name              | Description                                                     |
|:--------- |:--------- |:--------- |:----------------- |:--------------------------------------------------------------- |
| `Inf16`   | `Inf32`   | `Inf`     | مثبت بی‌نهایت | مقداری بزرگتر از تمام مقادیر متناهی ممیز شناور       |
| `-Inf16`  | `-Inf32`  | `-Inf`    | منفی بی‌نهایت | مقداری کوچکتر از تمام مقادیر متناهی ممیز شناور|
| `NaN16`   | `NaN32`   | `NaN`     | عدد نیست      | مقداری نابرابر با تمام اعداد ممیز شناور(از جمله خودش) |

برای مطالعه بیشتر در مورد چگونگی ترتیب این مقادیر نقطه شناور نامحدود نسبت به یکدیگر و سایر مقادیر، به بخش مقایسه عددی مراجعه کنید. با توجه به [استاندارد IEEE 754](https://en.wikipedia.org/wiki/IEEE_754-2008)، این مقادیر شناور نتایج برخی از محاسبات هستند:

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

توابع `typemin` و `typemax` نیز بر روی اعداد ممیز شناور قابل استفاده هستند:

```julia
julia> (typemin(Float16),typemax(Float16))
(-Inf16, Inf16)

julia> (typemin(Float32),typemax(Float32))
(-Inf32, Inf32)

julia> (typemin(Float64),typemax(Float64))
(-Inf, Inf)
```

### اپسیلون ماشین

اکثر اعداد واقعی را نمی‌توان دقیقاً با اعداد ممیز شناور نشان داد و بنابراین برای بسیاری از اهداف، دانستن فاصله بین دو عدد نقطه شناور قابل نمایش مجاور، که اغلب به عنوان [اپسیلون ماشین](https://en.wikipedia.org/wiki/Mapine_epsilon) شناخته می‌شود، مهم است.

جولیا تابع `eps` را ارائه می‌دهد که فاصله بین ‍`1.0` و مقدار بعدی بزرگتر ممیز شناور آن را نشان می‌دهد:

```julia
julia> eps(Float32)
1.1920929f-7

julia> eps(Float64)
2.220446049250313e-16

julia> eps() # same as eps(Float64)
2.220446049250313e-16
```

تابع `eps` همچنین می‌تواند یک مقدار ممیز شناور را به عنوان آرگومان بگیرد و تفاوت مطلق بین آن و مقدار بعدی شناور را نشان می‌دهد. یعنی `eps(x)` مقداری از همان نوع `x` خروجی می‌دهد به طوری که`x+eps(x)`مقدار بعدی شناور قابل نمایش بزرگتر از`x` است:

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

فاصله بین دو عدد ممیز شناور قابل نمایش مجاور ثابت نیست اما برای مقادیر کوچکتر، کوچکتر و برای مقادیر بزرگتر، بزرگتر است. به عبارت دیگر، اعداد قابل نمایش به صورت ممیز شناور در نزدیکی صفر بیشترین تراکم را دارند و با فاصله گرفتن از صفر، به صورت نمایی پراکنده می‌شوند. طبق تعریف، `eps(1.0)` همان `eps(Float64)` است زیرا `1.0` یک مقدار شناور 64 بیتی است.

جولیا همچنین توابع `nextfloat` و `prevfloat` را ارائه می‌کند که به ترتیب کوچکترین عدد قابل نمایش ممیز شناور بزرگتر از ورودی و  بزرگترین عدد قابل نمایش ممیز شناور کوچکتر از ورودی را برمی گردانند:

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

این مثال این اصل کلی را برجسته می‌کند که اعداد ممیز شناور قابل نمایش مجاور نیز دارای نمایش‌های دودویی مجاور هستند.

### حالتهای گردکردن

اگر عددی نمایش دقیق ممیز شناور نداشته باشد، باید آنرا گرد کرده و به یک مقدار نمایشی مناسب رساند. با این وجود، در صورت نیاز می‌توان نحوه انجام این گردکردن را با توجه به حالت‌های گردکردن ارائه شده در [استاندارد IEEE 754](https://en.wikipedia.org/wiki/IEEE_754-2008) تغییر داد.

حالت پیش فرض مورد استفاده همیشه گرد کردن به نزدیکترین مقدار قابل نمایش است و مقادیری که فاصله مساوی از دو طرف دارند، به سمت عددی که کوچکترین بیتش `0` است گرد می‌شوند.

### پیش‌زمینه و منابع

محاسبات ممیز شناور ظرافت‌های بسیاری را به همراه دارد که می‌تواند برای کاربرانی که با جزئیات اجرای سطح پایین آشنا نیستند تعجب آور باشد. با این حال، این ظرافت ها در اکثر کتابهای مربوط به محاسبات علمی و همچنین در منابع زیر به تفصیل شرح داده شده‌است:

  * راهنمای صریح محاسبات ممیز شناور [استاندارد IEEE 754-2008](https://standards.ieee.org/standard/754-2008.html) است. که البته به صورت آنلاین رایگان در دسترس نیست.
  * برای ارائه مختصر اما شفاف نحوه نمایش اعداد ممیز شناور، به [مقاله John D. Cook](https://www.johndcook.com/blog/2009/04/06/anatomy-of-a-floating-point-number/) یا [مقدمه](https://www.johndcook.com/blog/2009/04/06/numbers-are-a-leaky-abstraction/) نوشته شده توسط او که به بعضی از مسائل نمایش این اعداد و تفاوتشان با ایده انتزاعی که از اعداد حقیقی وجود دارد می‌پردازد، مراجعه کنید.  
  * همچنین [مجموعه پستهای وبلاگ بروس داوسون در مورد اعداد شناور](https://randomascii.wordpress.com/2012/05/20/thats-normalthe-performance-of-dd-floats/) نیز توصیه می شود.
  * برای یک بحث عالی و عمیق در مورد اعداد ممیز شناور و مسائل مربوط به صحت عددی هنگام محاسبه با آنها، به مقاله دیوید گلدبرگ [آنچه دانشمندان کامپیوتر باید درباره حساب‌های ممیز شناور بدانند](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.22.6768&rep=rep1&type=pdf) مراجعه کنید.
  * برای به دست آوردن مستندات گسترده تر تاریخچه، منطق و مسائل مربوط به اعداد با ممیز شناور و همچنین بحث درباره بسیاری از موضوعات دیگر در محاسبات عددی، به [نوشته‌های جمع آوری شده](https://people.eecs.berkeley.edu/~wkahan/) [William Kahan](https://en.wikipedia.org/wiki/William_Kahan)  که معمولاً به عنوان "پدر ممیز شناور" شناخته می‌شود مراجعه کنید.  [مصاحبه ای با پیرمرد نقطه شناور](https://people.eecs.berkeley.edu/~wkahan/ieee754status/754story.html) به صورت خاص ممکن است مورد توجه باشد.
  
## Arbitrary Precision Arithmetic

To allow computations with arbitrary-precision integers and floating point numbers, Julia wraps
the [GNU Multiple Precision Arithmetic Library (GMP)](https://gmplib.org) and the [GNU MPFR Library](https://www.mpfr.org),
respectively. The `BigInt` and `BigFloat` types are available in Julia for arbitrary
precision integer and floating point numbers respectively.

Constructors exist to create these types from primitive numerical types, and the
string literal](@ref non-standard-string-literals) [`@big_str` or `parse`
can be used to construct them from `AbstractString`s.
`BigInt`s can also be input as integer literals when
they are too big for other built-in integer types. Note that as there
is no unsigned arbitrary-precision integer type in `Base` (`BigInt` is
sufficient in most cases), hexadecimal, octal and binary literals can
be used (in addition to decimal literals).

Once created, they participate in arithmetic
with all other numeric types thanks to Julia's
[type promotion and conversion mechanism](@ref conversion-and-promotion):

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

However, type promotion between the primitive types above and `BigInt`/`BigFloat`
is not automatic and must be explicitly stated.

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

The default precision (in number of bits of the significand) and rounding mode of `BigFloat`
operations can be changed globally by calling `setprecision` and `setrounding`,
and all further calculations will take these changes in account.  Alternatively, the precision
or the rounding can be changed only within the execution of a particular block of code by using
the same functions with a `do` block:

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

## Numeric Literal Coefficients

To make common numeric formulae and expressions clearer, Julia allows variables to be immediately
preceded by a numeric literal, implying multiplication. This makes writing polynomial expressions
much cleaner:

```julia
julia> x = 3
3

julia> 2x^2 - 3x + 1
10

julia> 1.5x^2 - .5x + 1
13.0
```

It also makes writing exponential functions more elegant:

```julia
julia> 2^2x
64
```

The precedence of numeric literal coefficients is slightly lower than that of
unary operators such as negation.
So `-2x` is parsed as `(-2) * x` and `√2x` is parsed as `(√2) * x`.
However, numeric literal coefficients parse similarly to unary operators when
combined with exponentiation.
For example `2^3x` is parsed as `2^(3x)`, and `2x^3` is parsed as `2*(x^3)`.

Numeric literals also work as coefficients to parenthesized expressions:

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

Additionally, parenthesized expressions can be used as coefficients to variables, implying multiplication
of the expression by the variable:

```julia
julia> (x-1)x
6
```

Neither juxtaposition of two parenthesized expressions, nor placing a variable before a parenthesized
expression, however, can be used to imply multiplication:

```julia
julia> (x-1)(x+1)
ERROR: MethodError: objects of type Int64 are not callable

julia> x(x+1)
ERROR: MethodError: objects of type Int64 are not callable
```

Both expressions are interpreted as function application: any expression that is not a numeric
literal, when immediately followed by a parenthetical, is interpreted as a function applied to
the values in parentheses (see Functions for more about functions). Thus, in both of these
cases, an error occurs since the left-hand value is not a function.

The above syntactic enhancements significantly reduce the visual noise incurred when writing common
mathematical formulae. Note that no whitespace may come between a numeric literal coefficient
and the identifier or parenthesized expression which it multiplies.

### Syntax Conflicts

Juxtaposed literal coefficient syntax may conflict with some numeric literal syntaxes: hexadecimal,
octal and binary integer literals and engineering notation for floating-point literals. Here are some situations
where syntactic conflicts arise:

  * The hexadecimal integer literal expression `0xff` could be interpreted as the numeric literal
    `0` multiplied by the variable `xff`. Similar ambiguities arise with octal and binary literals like
    `0o777` or `0b01001010`.
  * The floating-point literal expression `1e10` could be interpreted as the numeric literal `1` multiplied
    by the variable `e10`, and similarly with the equivalent `E` form.
  * The 32-bit floating-point literal expression `1.5f22` could be interpreted as the numeric literal
    `1.5` multiplied by the variable `f22`.

In all cases the ambiguity is resolved in favor of interpretation as numeric literals:

  * Expressions starting with `0x`/`0o`/`0b` are always hexadecimal/octal/binary literals.
  * Expressions starting with a numeric literal followed by `e` or `E` are always floating-point literals.
  * Expressions starting with a numeric literal followed by `f` are always 32-bit floating-point literals.

Unlike `E`, which is equivalent to `e` in numeric literals for historical reasons, `F` is just another
letter and does not behave like `f` in numeric literals. Hence, expressions starting with a numeric literal
followed by `F` are interpreted as the numerical literal multiplied by a variable, which means that, for
example, `1.5F22` is equal to `1.5 * F22`.

## Literal zero and one

Julia provides functions which return literal 0 and 1 corresponding to a specified type or the
type of a given variable.

| Function          | Description                                      |
|:----------------- |:------------------------------------------------ |
| `zero(x)` | Literal zero of type `x` or type of variable `x` |
| `one(x)`  | Literal one of type `x` or type of variable `x`  |

These functions are useful in Numeric Comparisons to avoid overhead from unnecessary
[type conversion](@ref conversion-and-promotion).

Examples:

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
