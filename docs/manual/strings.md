# رشته‌ها
رشته‌ها دنباله‌های محدودی از کاراکترها هستند، اما مشکل اصلی هنگامی است که بخواهیم یک کاراکتر را تعریف کنیم. کاراکترهایی که انگلیسی‌زبان‌ها با آن آشنا هستند حروفی مثل `A` و `B` و حروف عددی و علائم نگارشی هستند. این کاراکترها توسط [ASCII](https://en.wikipedia.org/wiki/ASCII) به صورت یک نگاشت از مقادیر صحیح بین ۰ تا ۱۲۷ هستند و البته تعداد زیادی کاراکتری که در زبان‌های غیر انگلیسی استفاده می‌شود (مانند حروف یونانی، عربی، چینی و غیره)  نیز در ASCII پشتیبانی می‌شود.
استاندارد Unicode پیچیدگی‌های تعریف یک کاراکتر را برطرف می‌کند و به طور کلی به عنوان یک استاندارد برای حل این مشکل پذیرفته شده است. بسته به نوع نیاز شما، یا این پیچیدگی‌ها را نادیده می‌گیرید یا فرض می‌کنید که فقط کاراکترهای ASCII وجود دارد یا اینکه می‌توانید کدی طراحی کنید که هر نوع کاراکتری را پشتیبانی کند یا کدگذاری‌هایی که ممکن است هنگام است هنگام متن‌های غیر ASCII با آن‌ها برخورد کنید.
جولیا کار با متن ASCII را ساده و کارآمد می‌کند و مدیریت Unicode تا حد امکان ساده شده است. به طور خاص می‌تواند برای پردازش رشته‌های ASCII کد رشته به صورت زبان C بنویسید یا آن‌ها دقیقا به همان صورت که انتظار دارید عمل خواهند کرد.
درمورد رشته‌ها در جولیا چند ویژگی سطح بالای قابل توجه وجود دارد:

+ تایپ درون-ساختی که برای استفاده از رشته‌ها در جولیا وجود دارد `String` است که دامنه‌ی کامل کاراکترهای  [Unicode](https://en.wikipedia.org/wiki/Unicode) از طریق کدگذاری UTF-8 پشتیبانی می‌کند.
+ همه‌ی انواع تایپ‌های رشته‌ای، درواقع زیرگروهی از نوع انتزاعی (abstract) `AbstractString` هستند و کتابخانه‌‌های خارجی زیرگروه‌های اضافی `AbstractString` را برای تعریف کدگذاری خود، تعریف می‌کنند.
+ مانند C و Java، اما برخلاف زبان‌های برنامه‌‌نویسی پویا، جولیا نوع first-class برای نمایش صرفا یک کاراکتر دارد که به آن `AbstractChar` گفته می‌شود. درواقع تایپ درون‌ساخت `Char` زیرگروهی از `AbstractString` است که ۳۲ بیتی است و می‌تواند هر کاراکتر Unicodeای را نمایش دهد.
+ مانند Java، رشته‌ها در جولیا نیز immutable هستند. مقدار `AbstractString` نمی‌تواند تغییر کند. 
+ به صورت مفهومی، یک رشته یک *partial function* از اندیس‌ها به کاراکترها است، به ازای بعضی اندیس‌ها هیچ کاراکتری برگردانه نمی‌شود و exception برگردانده می‌شود.

## کاراکتر ها

یک `Char`	نشان‌دهنده یک کاراکتر است،یک تایپ اصلی ۳۲ بیتی که توسط حرف به‌خصوصی نمایش داده می‌شود و می‌تواند به مقدار عددی تبدیل شود تا مقدار Unicode خود را نشان دهد.
اینجا نشان داده می‌شود که این مقادیر چطور نشان داده می‌شوند:

```ulia
julia> c = 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> typeof(c)
Char
```
می‌توانید به آسانی آن‌ها را به مقادیر صحیح تبدیل کنید:

```julia
julia> c = Int('x')
120

julia> typeof(c)
Int64
```
یا به صورت عکس، می‌تواند عدد را به کاراکتر متناظر آن تبدیل کنید:

```julia
julia> Char(120)
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)
```
قاعدتا تمام مقادیر صحیح قابل قبول نیستند، اما به جهت عملکرد `Char` معتبر بودن یا نبود هر مقدار را چک نمی‌کند. اگر می‌خواهید معتبر بودن یا نبودن را چک کنید می‌توانید از تابع `isvalid` استفاده کنید:

```julia
julia> Char(0x110000)
'\U110000': Unicode U+110000 (category In: Invalid, too high)

julia> isvalid(Char, 0x110000)
false
```

شما می‌تواند هرکاراکتر Unicodeای را با استفاده از ' توسط `\u` تا ۴ رقم hex یا با `\U` تا ۸ رقم hex ورودی بگیرید:

```julia
julia> '\u0'
'\0': ASCII/Unicode U+0000 (category Cc: Other, control)

julia> '\u78'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> '\u2200'
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> '\U10ffff'
'\U10ffff': Unicode U+10FFFF (category Cn: Other, not assigned)
```

جولیا از تنظیمات محلی و زبان سیستم شما استفاده می‌کند تا مشخص کند کدام کاراکترها می‌توانند به صورت موجود چاپ شوند و کدام یک باید از نسخه عمومی منتشر شوند.

```julia
julia> Int('\0')
0

julia> Int('\t')
9

julia> Int('\n')
10

julia> Int('\e')
27

julia> Int('\x7f')
127

julia> Int('\177')
127
```
شما می‌تواند تمام عملیات‌های حسابی و مقایسه‌ ای را روی تایپ `Char` نیز انجام دهید:

```julia
julia> 'A' < 'a'
true

julia> 'A' <= 'a' <= 'Z'
false

julia> 'A' <= 'X' <= 'Z'
true

julia> 'x' - 'a'
23

julia> 'A' + 1
'B': ASCII/Unicode U+0042 (category Lu: Letter, uppercase)
```

## مبانی رشته‌

حروف رشته با استفاده از double quotes یا triple double quotes مشخص می‌شوند:

```julia
julia> str = "Hello, world.\n"
"Hello, world.\n"

julia> """Contains "quote" characters"""
"Contains \"quote\" characters"
```

اگر بخواهید کاراکتر خاصی از رشته‌ را جدا کنید، می‌توانید با اندیس آن این کار را انجام دهید:

```julia
julia> str[begin]
'H': ASCII/Unicode U+0048 (category Lu: Letter, uppercase)

julia> str[1]
'H': ASCII/Unicode U+0048 (category Lu: Letter, uppercase)

julia> str[6]
',': ASCII/Unicode U+002C (category Po: Punctuation, other)

julia> str[end]
'\n': ASCII/Unicode U+000A (category Cc: Other, control)
```

بسیاری از شیهای جولیا، از جمله رشته‌‌ها می‌توانند توسط اعداد صحیح اندیس گذاری شوند.
کلیدواژه‌های `begin` و `end` می‌توانند به عنوان اولین اندیس و آخرین اندیس استفاده شوند.

```julia
julia> str[end-1]
'.': ASCII/Unicode U+002E (category Po: Punctuation, other)

julia> str[end÷2]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```

استفاده از اندیسی بزرگتر از `end` یا کوچکتر از `begin` یا همان `1` منجر به خطا می‌شود:

```julia
julia> str[begin-1]
ERROR: BoundsError: attempt to access 14-codeunit String at index [0]
[...]

julia> str[end+1]
ERROR: BoundsError: attempt to access 14-codeunit String at index [15]
[...]
```

همچنین می‌توانید با استفاده از اندیس گذاری بازه‌ای، یک زیررشته استخراج کنید:

```julia
julia> str[4:9]
"lo, wo"
```

دقت کنید که عبارات `str[k]` و `str[k:k]` مقدار یکسانی را برنمی‌گردانند:

```julia
julia> str[6]
',': ASCII/Unicode U+002C (category Po: Punctuation, other)

julia> str[6:6]
","
```
اولی یک کاراکتر از نوع `Char` است و دومی یک رشته است که مقدار آن یک تک کاراکتر است. در جولیا این دو چیز متفاوت هستند.

اندیس گذاری بازه‌ای یک کپی از آن بازه برمی‌گرداند. برای جداکردن زیررشته از `SubString` هم می‌توان استفاده کرد:

```julia
julia> str = "long string"
"long string"

julia> substr = SubString(str, 1, 4)
"long"

julia> typeof(substr)
SubString{String}
```


## استفاده از  Unicode and UTF-8 در جولیا


همانطور که قبلتر نیز بحث کردیم، حروف کاراکترها می‌توانند توسط Unicode یعنی `\u` و `\U`  نمایش داده شوند:

```julia
julia> s = "\u2200 x \u2203 y"
"∀ x ∃ y"
```

برای اندیس‌گذاری رشته‌ها در جولیا لازم است که از اندیس‌ها معتبر استفاده کنیم و درغیر این صورت با خطا مواجه می‌شویم:

```julia
julia> s[1]
'∀': Unicode U+2200 (category Sm: Symbol, math)

julia> s[2]
ERROR: StringIndexError: invalid index [2], valid nearby indices [1]=>'∀', [4]=>' '
Stacktrace:
[...]

julia> s[3]
ERROR: StringIndexError: invalid index [3], valid nearby indices [1]=>'∀', [4]=>' '
Stacktrace:
[...]

julia> s[4]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)
```
به عنوان مثال، کاراکتر `∀` یک کاراکتر ۳ بایتی است و پس اندیس‌های ۲ و ۳ نامعتبر هستند و اندیس کاراکتر بعدی از ۴ است. اندیس معتبر بعدی با استفاده از `nextind(s,1)` قابل دریافت است.
از آنجا که `end` همواره به آخرین اندیس یک مجموعه اشاره می‌کند، قاعدتا  `end-1`  یک اندیس‌گذاری نامعتبر است.


```julia
julia> s[end-1]
' ': ASCII/Unicode U+0020 (category Zs: Separator, space)

julia> s[end-2]
ERROR: StringIndexError: invalid index [9], valid nearby indices [7]=>'∃', [10]=>' '
Stacktrace:
[...]

julia> s[prevind(s, end, 2)]
'∃': Unicode U+2203 (category Sm: Symbol, math)
```

اولین مورد کار می‌کند زیرا کاراکتر `y` و فاصله کاراکترهای یک بایتی هستند.

خروجی گرفتن از یک زیررشته توسط اندیس گذاری بازه‌ای انظار دارد که ورودی معتبر بگیرد، درغیر این صورت خطا برمی‌گرداند:

```julia
julia> s[1:1]
"∀"

julia> s[1:2]
ERROR: StringIndexError: invalid index [2], valid nearby indices [1]=>'∀', [4]=>' '
Stacktrace:
[...]

julia> s[1:4]
"∀ "
```

پس بخاطر نوع کدگذاری گفته شده، تعداد کاراکترهای یک رشته (که توسط `length(s)` محاسبه می‌شود) لزوما با اخرین اندیس آن برابر نیست. اگر حول اندیس ۱ تا  `lastindex(s)` پیمایش انجام دهید، رشته‌ی کاراکترهایی که از `s` (در صورت عدم رخدادن خط) برگردانده می‌شوند، رشته‌ی کاراکترهای `s` هستند. پس `length(s) <= lastindex(s)` است.
در زیر یک روش ناکارآمد برای پیمایش حول `s` آمده است:

```julia
julia> for i = firstindex(s):lastindex(s)
           try
               println(s[i])
           catch
               # ignore the index error
           end
       end
∀

x

∃

y
```

خط های خالی درواقع شامل حرف فاصله هستند

یک راه دیگر برای استفاده از اندیس‌گذاری معتبر در رشته‌ها استفاده از `nextind` و `prevind` است تا اندیس معتبر بعدی یا قبلی را بدهد و با استفاده از آن بتوان پیمایش را انجام داد.
همچین می‌توان از تابع `eachindex(s)` برای پیمایش حول رشته نیز استفاده کرد:

```julia
julia> collect(eachindex(s))
7-element Vector{Int64}:
  1
  4
  5
  6
  7
 10
 11
```

رشته‌ ها در جولیا می‌توانند شامل رشته کدهای نامعتبر UTF-8 شوند. این تبدیل این امکان را به وجود می‌آورد که هر با هر دنباله‌ی بایتی به صورت `String` برخورد شود. در این موقعیت‌ها قانون این است که هنگام پردازش کردن دنباله‌ای از کدهای واحد از چپ به راست به صورت دنباله‌ی کد واحد ۸ بیتی که با شروع یکی از الگوهای زیر مطابق دارند تشکیل شوند ( هر `x` می‌تواند 0 یا 1 باشد):


* `0xxxxxxx`;
* `110xxxxx` `10xxxxxx`;
* `1110xxxx` `10xxxxxx` `10xxxxxx`;
* `11110xxx` `10xxxxxx` `10xxxxxx` `10xxxxxx`;
* `10xxxxxx`;
* `11111xxx`.

این قانون در زیر توسط یک مثال توضیح داده شده است:

```
julia> s = "\xc0\xa0\xe2\x88\xe2|"
"\xc0\xa0\xe2\x88\xe2|"

julia> foreach(display, s)
'\xc0\xa0': [overlong] ASCII/Unicode U+0020 (category Zs: Separator, space)
'\xe2\x88': Malformed UTF-8 (category Ma: Malformed, bad data)
'\xe2': Malformed UTF-8 (category Ma: Malformed, bad data)
'|': ASCII/Unicode U+007C (category Sm: Symbol, math)

julia> isvalid.(collect(s))
4-element BitArray{1}:
 0
 0
 0
 1

julia> s2 = "\xf7\xbf\xbf\xbf"
"\U1fffff"

julia> foreach(display, s2)
'\U1fffff': Unicode U+1FFFFF (category In: Invalid, too high)
```


## الحاق

یکی از رایج‌ترین و مفیدترین عملیات‌هایی که روش رشته‌ها تعریف می‌شود، الحاق کردن است::

```julia
julia> greet = "Hello"
"Hello"

julia> whom = "world"
"world"

julia> string(greet, ", ", whom, ".\n")
"Hello, world.\n"
```

لازم است که از خطرات الحاق کردن رشته‌های نامعتبر UTF-8 آگاه باشیم. رشته‌ی حالص ممکن است شامل کاراکترهایی متفاوت با کاراکترهای ورودی باشد و تعداد این کاراکترها ممکن است کمتر از تعداد کاراکترهای ورودی شود:

```
julia> a, b = "\xe2\x88", "\x80"
("\xe2\x88", "\x80")

julia> c = a*b
"∀"

julia> collect.([a, b, c])
3-element Array{Array{Char,1},1}:
 ['\xe2\x88']
 ['\x80']
 ['∀']

julia> length.([a, b, c])
3-element Array{Int64,1}:
 1
 1
 1
```

این شرایط هنگامی رخ می‌دهد که این عملیات را روی رشته‌های UTF-8 نامعتبر اجرا کنیم و برای رشته‌های UTF-8 معتبر این خطا رخ نمی‌دهد.
جولیا همچنین `*` را برای عملیات الحاق پشتیبانی میکند:

```julia
julia> greet * ", " * whom * ".\n"
"Hello, world.\n"
```

درصورتی که `*` ممکن است برای این کار غافلگیرانه به نظر بیاید، استفاده از آن در ریاضیات به خصوص در جبر سابقه دارد.
در ریاضیات،  `+` معمولا به عنوان عملگر *جابه‌جایی* استفاده می‌شود و معمولا ترتیب عملوندها اهمیتی ندارد. به عنوان مثال در جمع ماتریس‌ها `A + B == B + A` برای هر نوع ماتریس `A` و `B` که ابعاد یکسان دارند برقرار است.
اما `*` معمولا برای عملیات‌های *جابه‌‌جایی ناپذیر* به کار می‌رود و ترتیب عملوندها اهمیت پیدا می‌کند. به طور مثال در مثال ضرب ماتریس‌ها به طول کلی `A * B != B * A` برقرار نیست. همین مثال برای الحاق رشته‌ها نیز برقرار است `greet * whom != whom * greet` . پس استفاده از `*` به جای `+` از منظر دلایل گفته‌شده منطقی تر است.


## درون‌یابی

ساختن رشته‌های جدید توسط الحاق آن‌ها ممکن است به یک عملیت دست و پاگیر تبدیل شود، بنابراین برای راحتی در این موارد جولیا امکان درون‌یابی حروف رشته‌ها را توسط `$` (مانند Perl) مهیا کرده است:

```julia
julia> "$greet, $whom.\n"
"Hello, world.\n"
```

این عملیات خواناتر و ساده‌تر است. به طور مثال روش سنتی مثال بالا برابر است با  `string(greet, ", ", whom, ".\n")`.

عبارت کاملی که بعد از `$`	می‌آید تحت عنوان عبارتی برداشت می‌شود که مقدار آن قرار است درون رشته قرار بگیرد. برای سادگی و خوانا تر شدن می‌توانید از پرانتز استفاده کنید:

```julia
julia> "1 + 2 = $(1 + 2)"
"1 + 2 = 3"
```
هر  دو عملیات الحاق و درون‌یابی، `string` را برای تبدیل اشیابه رشته صدا می‌زنند. اما `string` صرفا خروجی تابع `print` را برمی‌گرداند. پس تایپ‌های جدید باید متد‌های `print` یا `show` را به جای `string` اضافه کنند.

اکثر اشیا non-`AbstractString` به رشته‌هایی تبدیل می‌ شوند که نزدیک و مشابه حروف آن‌ها است:

```julia
julia> v = [1,2,3]
3-element Vector{Int64}:
 1
 2
 3

julia> "v: $v"
"v: [1, 2, 3]"
```

همچنین قاعدتا اگر از تایپ `string`  استفاده کنیم، همان مقدار آن‌ها وارد درون‌یابی می‌شود:

```jlia
julia> c = 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> "hi, $c"
"hi, x"
```

اگر بخواهیم از خود کاراکتر `%` استفاده کنیم باید از backslash استفاده کنیم:

```julia
julia> print("I have \$100 in my account.\n")
I have $100 in my account.
```

## رشته‌ها با استفاده Triple-Quoted

وقتی رشته‌ها توسط triple-quotes (`"""..."""`) ساخته می‌شوند، برخی عملیات‌های خاص بر رویشان تعریف می‌شود که سبب می‌شود برای متن‌ها و بلاک‌‌های متنی طولانی مورد استفاده قرار گیرند.
این قابلیت برای رشته‌هایی که تو رفتگی و یا به صورت بلاکی هستند، مفید است:

```julia
julia> str = """
           Hello,
           world.
         """
"  Hello,\nو  world.\n"
```
سطح indentation را آخرین خط قبل از بسته‌شدن `"""` تعیین می‌کند.
برای تعیین سطح dedentation، از معیار طولانی‌ترین دنباله‌ی شروع فاصله‌ها یا تب‌ها در همه‌ی خط‌ها (به جز خط‌هایی که تنها شامل فاصله هستند) استفاده می‌شود.
بنابراین برای همه‌ی خط‌ها (‌به جز خط ابتدایی آغازگر `"""` زیررشته‌ی توصیف شده در بالا حذف می‌شود:
```julia
julia> """    This
         is
           a test"""
"    This\nis\n  a test"
```

حال  اگر بلافاصله بعد از آغاز `"""` به خط جدید برویم، معادل است با اینکه به خط جدید نرویم:


```julia
"""hello"""
```
دو رشته‌ی بالا با هم معادل هستند.

```julia
"""
hello"""
```

اما

```jula
"""

hello"""
```
شامل یک حرف خط جدید در آغاز خواهد بود.

برداشتن خط جدید بعد از dedentation انجام می‌شود، به عنوان مثال:

```julia
julia> """
         Hello,
         world."""
"Hello,\nworld."
```

فضای خالی دنباله ای بدون تغییر باقی مانده است.
