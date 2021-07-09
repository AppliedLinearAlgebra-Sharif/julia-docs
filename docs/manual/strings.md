# رشته‌ها
رشته‌ها دنباله‌های محدودی از کاراکترها هستند، اما مشکل اصلی هنگامی است که بخواهیم یک کاراکتر را تعریف کنیم. کاراکترهایی که انگلیسی‌زبان‌ها با آن آشنا هستند حروفی مثل `A` و `B` و حروف عددی و علائم نگارشی هستند. این کاراکترها توسط [ASCII](https://en.wikipedia.org/wiki/ASCII) به صورت یک نگاشت از مقادیر صحیح بین ۰ تا ۱۲۷ هستند و البته تعداد زیادی کاراکتر که در زبان‌های غیر انگلیسی استفاده می‌شود (مانند حروف یونانی، عربی، چینی و غیره)  نیز در ASCII پشتیبانی می‌شود.
استاندارد [Unicode](https://en.wikipedia.org/wiki/Unicode) پیچیدگی‌های تعریف یک کاراکتر را برطرف می‌کند و به طور کلی به عنوان یک استاندارد برای حل این مشکل پذیرفته شده است. بسته به نوع نیاز شما، یا می‌ توانید به طور کامل این پیچیدگی‌ها را نادیده می‌گیرید و فرض می‌کنید که فقط کاراکترهای ASCII وجود دارد یا اینکه می‌توانید کدی طراحی کنید که هر نوع کاراکتر (بسته به نیاز شما) را و یا کدگذاری را که ممکن است در هنگام مواجه با متن‌های غیر ACSII برخورد کنید را پشتیبانی کند.
جولیا کار با متن ASCII را ساده و کارآمد می‌کند و مدیریت Unicode تا حد امکان ساده شده است. به طور خاص می‌تواند برای پردازش رشته‌های ASCII کد رشته به صورت فرمت زبان C بنویسید و آن‌ها دقیقا به همان صورت که انتظار دارید عمل خواهند کرد. حال اگر با متنی با فرمت غیر ASCII برخورد داشته باشید، خطایی با پیام واضح برگردانده خواهد شد تا ایراد کار را متوجه شوید.

درمورد رشته‌ها در جولیا چند ویژگی سطح بالای قابل توجه وجود دارد:
+ تایپ درون-ساختی که برای استفاده از رشته‌ها در جولیا وجود دارد `String` است که دامنه‌ی کامل کاراکترهای  [Unicode](https://en.wikipedia.org/wiki/Unicode) را از طریق کدگذاری UTF-8 پشتیبانی می‌کند (تابع `transcode`  نیز وجود دارد که که امکان تبدیل به کدگذاری‌های دیگر را امکان‌پذیر کند).
+ همه‌ی انواع تایپ‌های رشته‌ (String). در جولیا، درواقع زیرگروهی از نوع گروه انتزاعی (abstract type) `AbstractString` هستند و کتابخانه‌‌های خارجی زیرگروه‌های اضافی `AbstractString` را برای تعریف کدگذاری خود، تعریف می‌کنند. اگر تابعی پیاده سازی کرده‌اید که در ورودی بخواهد رشته بگیرد، باید نوع آن را `AbstractString` بگذارید تا همه‌ی نوع رشته‌های موجود پذیرفته شوند.
+ مانند C و Java، اما برخلاف زبان‌های برنامه‌‌نویسی پویا، جولیا کلاسی مجزا برای نمایش صرفا یک کاراکتر دارد که به آن `AbstractChar` گفته می‌شود. درواقع تایپ درون‌ساخت `Char` زیرگروهی از `AbstractString` است که ۳۲ بیتی است و می‌تواند هر کاراکتر Unicodeای را نمایش دهد.
+ مانند Java، رشته‌ها در جولیا نیز immutable هستند. مقدار `AbstractString` نمی‌تواند تغییر کند. 
+ به صورت مفهومی، یک رشته یک *partial function* از اندیس‌ها به کاراکترها است، به ازای بعضی اندیس‌ها ممکن است هیچ کاراکتری برگردانه نمی‌شود و در این صورت exception برگردانده می‌شود.

## کاراکتر ها

یک `Char`	نشان‌دهنده یک کاراکتر است،یک تایپ اصلی ۳۲ بیتی که توسط یک حرف به‌ خصوص نمایش داده می‌شود و می‌تواند به مقدار عددی تبدیل شود تا مقدار Unicode خود را نشان دهد. (برخی کتابخانه‌های جولیا ممکن است زیرگروه دیگری از `AbstractChar` برای نمایش کاراکترها تعریف کنند که این بسته به نوع نیاز آن‌ها است.)
در مثال زیر می‌بینیم که این متغیرها به چه صورت نمایش داده می‌شوند:

```ulia
julia> c = 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)

julia> typeof(c)
Char
```
در معماری ۳۲بیتی، تابع بالا `Int32` برمی‌گرداند.
همانطور که گفته شد، می‌توانید آن‌ها را به آسانی به مقادیر صحیح نیز تبدیل کنید:

```julia
julia> c = Int('x')
120

julia> typeof(c)
Int64
```
یا به صورت عکس، می‌تواند عدد صحیح را به کاراکتر متناظر آن تبدیل کنید:

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
می‌دانیم که کدهای معتبر برای Unicode، از `U+0000` تا `U+D7FF` و از `U+E000` تا `U+10FFFF` هستند. البته هنوز برای تمام مقادیر این بازه، مقداردهی‌های معنادار داده نشده‌است و لزوما توسط همه‌ی برنامه‌ها قابل تفسیر نیستند اما همه‌ی مقادیر این بازه از نظر استاندارد Unicode معتبر هستند.

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

جولیا از تنظیمات محلی و زبان سیستم شما استفاده می‌کند تا مشخص کند کدام کاراکترها می‌توانند به صورت موجود چاپ شوند و کدام یک باید به صورت نسخه عمومی چاپ شوند. .
 همچنین از فرمت دهی مشابه زبان C نیز می‌توانید استفاده کنید:

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

حروف یک رشته با استفاده از double quotes یا triple double quotes مشخص می‌شوند:

```julia
julia> str = "Hello, world.\n"
"Hello, world.\n"

julia> """Contains "quote" characters"""
"Contains \"quote\" characters"
```

اگر بخواهید کاراکتر خاصی از رشته‌ را جدا کنید، می‌توانید با اندیس آن کاراکتر، این کار را انجام دهید:

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

بسیاری از اشیا جولیا، از جمله رشته‌‌ها می‌توانند توسط اعداد صحیح اندیس گذاری شوند. اندیس اولین المان‌ (در اینجا اولین کاراکتر)‌ با استفاده از دستور `firstindex(str)` و اندیس آخرین المان با استفاده از دستور  `lastindex(str)` برگردانده می‌شوند.
کلیدواژه‌های `begin` و `end` نیز می‌توانند به عنوان اولین اندیس و آخرین اندیس استفاده شوند.
اندیس‌گذاری رشته‌ها مانند بیشتر اشیا در جولیا، از 1 شروع می‌شود، به عبارت دیگر `firstindex(str)`  برای تمام زیرگروه‌های `AbstractString` همواره مقدار 1 برمی‌گرداند. حال نکته‌ی مهم در رشته‌ها این است که به صورت کلی `lastindex(str)` با `length(str)` برابر نیست. دلیل نیز این است که بعضی کاراکترها ممکن است بیشتر از یک واحد از کد مورد استفاده شده اشغال کرده باشند.

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

همچنین می‌توانید با استفاده از اندیس گذاری بازه‌ای، یک زیررشته از رشته‌ی موردنظر را استخراج کنید:

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
اولی یک کاراکتر از نوع `Char` است و دومی یک رشته است که مقدار آن یک تک کاراکتر است. در جولیا این دو چیز متفاوت هستند و به صورت متفاوت رفتار خواهند کرد.

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


همانطور که قبلتر نیز بحث کردیم، حروف کاراکترها می‌توانند توسط Unicode یعنی `\u` و `\U`  و یا روش‌های سنتی‌تر مبتنی بر C نمایش داده شوند:

```julia
julia> s = "\u2200 x \u2203 y"
"∀ x ∃ y"
```
حال بسته به تنظیمات شخصیتان، این کاراکترها می‌توانند به صورتی که انتظار دارید نمایش داده شوند و یا آن به صورت حالت خام آن‌ها. حروف رشته‌ها توسط UTF-8 کدگذاری شده‌اند. درواقع UTF-8 یک کدگذاری با طول متغیر است، به این معنا که طول کد همه‌ی کاراکترها با هم برابر نیستند. در کدگذاری UTF-8 کاراکترهای ASCII (یا به بیان دیگر آن‌هایی که کد آن‌ها کمتر از ۱۲۸ یعنی `0x80` است) توسط یک بایت کدگذاری می‌شوند، اما کدهای بیشتر از `0x80` بیشتر از یک بایت اشغال می‌کنند و حتی ممکن است تا ۴ بایت نیز اشغال کنند.
حال نکته‌ی بالا سبب می‌شود که اندیس‌گذاری در جولیا نیاز به دقت بیشتری داشته باشد. اندیس رشته‌ها در جولیا مطابق با واحد کدهای استفاده شده است که در UTF-8 این واحد، بایت است. پس این نکته سبب می‌شود که هر اندیسی یک اندیس معتبر برای رشته‌ها در جولیا تلقی نشود. اگر از اندیس نامعتبر استفاده شود، خطا برگردانده می‌شود:

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
به عنوان مثال، کاراکتر `∀` یک کاراکتر ۳ بایتی است و پس استفاده از اندیس‌های ۲ و ۳ نامعتبر هستند و اندیس کاراکتر بعدی از ۴ است. اندیس معتبر بعدی با استفاده از `nextind(s,1)` قابل دریافت است. می‌دانیم در مثال بالا این مقدار ۴ است. پس اندیس معتبر بعدی با دستور `nextind(s,4)` گرفته می‌شود الی آخر.
از آنجا که `end` همواره به آخرین اندیس یک مجموعه اشاره می‌کند، اگر کاراکتر یکی مانده با آخر کاراکتری باشد که چندین بایت را اشغال کرده باشد، قاعدتا  `end-1`  یک اندیس‌گذاری نامعتبر است.

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
اولین مورد بالا کار می‌کند زیرا کاراکتر `y` و فاصله کاراکترهای یک بایتی هستند. اما از آنجا که کاراکتر `∃` یک کاراکتر چندین بایتی است، استفاده از دستور `end-2` منجر به خطا شده است. روش درست برای اینکار استفاده از `prevind(s, lastindex(s), 2)` است که به جای `lastindex(s)` می‌توان از `end` نیز استفاده کرد.

قاعدتا برای استخراج یک زیررشته با استفاده از اندیس‌گذاری بازه‌ای نیز باید از اندیس‌های معتبر استفاده کنیم، در غیر این صورت خطا برگردانده می‌شود:
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

پس بخاطر نوع کدگذاری گفته شده، تعداد کاراکترهای یک رشته (که توسط `length(s)` محاسبه می‌شود) لزوما با عدد اخرین اندیس آن برابر نیست. اگر حول اندیس ۱ تا  `lastindex(s)` پیمایش انجام دهید، دنباله‌‌ی کاراکترهایی که از `s` (در صورت درنظر نگرفتن و چشم پوشی از خطاها) برگردانده می‌شوند، همان کاراکترهای `s` هستند. پس مشخص است که `length(s) <= lastindex(s)` است.
درواقع روش گفته‌ شده یک روش ناکارآمد برای پیمایش حول `s` آمده است:
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
خط های خالی درواقع شامل حرف فاصله هستند. خوشبختانه روش‌های بسیار کارآمدتری نسبت به روش عجیب بالا برای پیشمایش حول رشته وجود دارد. در یکی از ساده‌ترین آن‌ها با رشته می‌توانیم به صورت یک شی برخورد کنیم که در زیر مثالی از آن آمده است. یک مزیت مهم روش زیر نسبت به بالا این است که دیگر نیازی به مدیریت خطای برگردانده شده نیست.
```julia
julia> for c in s
           println(c)
       end
∀

x

∃

y
```

یک روش دیگر برای پیدا کردن اندیس‌های معتبر در رشته‌ها استفاده از `nextind` و `prevind` است تا اندیس معتبر بعدی یا قبلی را با استفاده از اندیس معتبر فعلی بدهد و با استفاده از آن بتوان پیمایش را انجام داد.
همچین می‌توان از تابع `eachindex(s)` برای پیمایش حول اندیس‌های معبتر یک رشته استفاده کرد:

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
اگر بخواهید به کدهای خام دسترسی داشته باشید، می‌توانید از تابع `codeunit(s,i)` استفاده کنید که `i`  از ۱ تا  `ncodeunits(s)` به صورت پشت سرهم اجرا می‌شود. تابع `codeunits(s)` نیز wrapper از نوع `AbstractVector{UInt8}` خروجی می‌دهد تا به این کدهای خام به صورت یک آرایه دسترسی داشته باشید.

رشته‌ ها در جولیا می‌توانند شامل رشته کدهای نامعتبر UTF-8 نیز باشند. این تبدیل این امکان را به وجود می‌آورد که هر با هر دنباله‌ی بایتی به صورت یک رشته برخورد شود. در این موقعیت‌ها قانون این است که هنگام پردازش کردن دنباله‌ای از کدهای واحد از چپ به راست به صورت دنباله‌ی کد واحد ۸ بیتی که با شروع یکی از الگوهای زیر مطابق دارند تشکیل شوند ( هر `x` می‌تواند 0 یا 1 باشد):
* `0xxxxxxx`;
* `110xxxxx` `10xxxxxx`;
* `1110xxxx` `10xxxxxx` `10xxxxxx`;
* `11110xxx` `10xxxxxx` `10xxxxxx` `10xxxxxx`;
* `10xxxxxx`;
* `11111xxx`.
به طور کلی این قانون باعث می‌شود که رشته کدهای بسیار طولانی و پیشوندهای آن، به صورت یک کاراکتر نامعتبر تلقی شوند تا چندین کاراکتر نامعتبر.
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
در مثال بالا مشاهده می‌کنید که دو کد اول رشته‌ی `s`  یک کد بسیار طولانی برای کاراکتر فاصله هستند و نامعتبر است، اما در رشته به صورت صرفا یک کاراکتر پذیرفته شده است. دو کد بعدی،  یک آغاز کد معتبر برای  یک رشته‌ی ۳ بایتی معتبر (از نوع UTF-8)‌ هستند، هرچند کد پنجم یعنی `\xe2` یک ادامه‌ی معتبر برای این کد نمی‌باشد. پس کدهای سوم و چهارم به عنوان ناقص و ناهنجار در این رشته درنظر گرفته می‌شوند. به طریق مشابه کد پنجم نیز به همین صورت است زیر `|` یک ادامه‌ی معتبر برای آن نمی‌باشد. درنهایت رشته‌ی ۲ شامل یک کد بسیار طولانی است.

جولیا به صورت پیش‌فرض از کدگذاری UTF-8 استفاده می‌کند و از کدگذاری‌هایی دیگر نیز به صورت اضافه کردن کتابخانه‌ی موردنظر پشتیبانی می‌کند. به طور مثال کتابخانه‌ی [LegacyStrings.jl](https://github.com/JuliaStrings/LegacyStrings.jl) `UTF16String` و `UTF32String` را پیاده‌سازی کرده است. توضیحات اضافه‌تر درمورد چگونگی پیاده‌سازی این کدگذاری‌ها از حوصله‌ی این بخش خارج است. تابع `transcode`  برای تبدیل داده به کدگذاری‌های مختلف ` UTF-xx` مورد استفاده قرار می‌گیرد که از آن در کتابخانه‌ها استفاده می‌شود.

## الحاق

یکی از رایج‌ترین و مفیدترین عملیات‌هایی که روش رشته‌ها تعریف می‌شود، الحاق کردن (به هم چسباندن ) آن‌ها است:

```julia
julia> greet = "Hello"
"Hello"

julia> whom = "world"
"world"

julia> string(greet, ", ", whom, ".\n")
"Hello, world.\n"
```

لازم است که از خطرات الحاق کردن رشته‌های نامعتبر UTF-8 آگاه باشیم. رشته‌ی حالص ممکن است شامل کاراکترهایی متفاوت با کاراکترهای ورودی باشد و تعداد این کاراکترها ممکن است کمتر از جمع تعداد کاراکترهای ورودی شود:

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

این شرایط هنگامی رخ می‌دهد که این عملیات را روی رشته‌های UTF-8 نامعتبر اجرا کنیم و برای رشته‌های UTF-8 معتبر این خطا رخ نمی‌دهد و خروجی دقیقا مورد انتظار ما خواهد بود.
جولیا همچنین `*` را برای عملیات الحاق پشتیبانی میکند:

```julia
julia> greet * ", " * whom * ".\n"
"Hello, world.\n"
```

درصورتی که `*` ممکن است برای این کار غافلگیرانه به نظر بیاید، زیرا در بسیاری از زبان‌ها از نماد `+` برای این منظور استفاده می‌شود اما  استفاده از آن در ریاضیات به خصوص در جبر سابقه دارد.
در ریاضیات،  `+` معمولا به عنوان عملگر *جابه‌جایی* استفاده می‌شود و معمولا ترتیب عملوندها اهمیتی ندارد. به عنوان مثال در جمع ماتریس‌ها `A + B == B + A` برای هر نوع ماتریس `A` و `B` که ابعاد یکسان دارند برقرار است.
اما `*` معمولا برای عملیات‌های *جابه‌‌جایی ناپذیر* به کار می‌رود و ترتیب عملوندها اهمیت پیدا می‌کند. به طور مثال در مثال ضرب ماتریس‌ها به طول کلی `A * B != B * A` برقرار نیست. همین مثال برای الحاق رشته‌ها نیز برقرار است `greet * whom != whom * greet` . پس استفاده از `*` به جای `+` از منظر دلایل گفته‌شده منطقی تر است.
به طور دقیقتر و با ادبیات جبر، مجموعه‌ی همه‌ی رشته‌های با طول محدود با عملیات الحاق کردن (`*`)،  تشکیل یک [free monoid](https://en.wikipedia.org/wiki/Free_monoid) (*S*, `*`) می‌دهند که المان identity این مجموعه، قاعدتا رشته‌ی `""` است. 


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
هر  دو عملیات الحاق و درون‌یابی، درواقع `string` را برای تبدیل این اشیا به فرم رشته صدا می‌زنند. اما `string` صرفا خروجی تابع `print` را برمی‌گرداند. پس تایپ‌های جدید باید متد‌های `print` یا `show` را به جای `string` اضافه کنند.

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

وقتی رشته‌ها توسط triple-quotes (`"""..."""`) ساخته می‌شوند، برخی عملیات‌های خاص بر رویشان تعریف می‌شود که سبب می‌شود برای متن‌ها و بلاک‌‌های متنی طولانی مورد استفاده قرار گیرند و بسیار مفید واقع شوند.
ابتدا رشته‌های triple-quoted خط با کمترین تورفتگی اختصاص یافته اند. این قابلیت برای رشته‌هایی که تو رفتگی و یا به صورت بلاکی هستند، مفید است:

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




## Common Operations

You can lexicographically compare strings using the standard comparison operators:

```julia
julia> "abracadabra" < "xylophone"
true

julia> "abracadabra" == "xylophone"
false

julia> "Hello, world." != "Goodbye, world."
true

julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
true
```

You can search for the index of a particular character using the
`findfirst` and `findlast` functions:

```julia
julia> findfirst(isequal('o'), "xylophone")
4

julia> findlast(isequal('o'), "xylophone")
7

julia> findfirst(isequal('z'), "xylophone")
```

You can start the search for a character at a given offset by using
the functions `findnext` and `findprev`:

```julia
julia> findnext(isequal('o'), "xylophone", 1)
4

julia> findnext(isequal('o'), "xylophone", 5)
7

julia> findprev(isequal('o'), "xylophone", 5)
4

julia> findnext(isequal('o'), "xylophone", 8)
```

You can use the `occursin` function to check if a substring is found within a string:

```julia
julia> occursin("world", "Hello, world.")
true

julia> occursin("o", "Xylophon")
true

julia> occursin("a", "Xylophon")
false

julia> occursin('o', "Xylophon")
true
```

The last example shows that `occursin` can also look for a character literal.

Two other handy string functions are `repeat` and `join`:

```julia
julia> repeat(".:Z:.", 10)
".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

julia> join(["apples", "bananas", "pineapples"], ", ", " and ")
"apples, bananas and pineapples"
```

Some other useful functions include:

  * `firstindex(str)` gives the minimal (byte) index that can be used to index into `str` (always 1 for strings, not necessarily true for other containers).
  * `lastindex(str)` gives the maximal (byte) index that can be used to index into `str`.
  * `length(str)` the number of characters in `str`.
  * `length(str, i, j)` the number of valid character indices in `str` from `i` to `j`.
  * `ncodeunits(str)` number of [code units](https://en.wikipedia.org/wiki/Character_encoding#Terminology) in a string.
  * `codeunit(str, i)` gives the code unit value in the string `str` at index `i`.
  * `thisind(str, i)` given an arbitrary index into a string find the first index of the character into which the index points.
  * `nextind(str, i, n=1)` find the start of the `n`th character starting after index `i`.
  * `prevind(str, i, n=1)` find the start of the `n`th character starting before index `i`.

## Non-Standard String Literals

There are situations when you want to construct a string or use string semantics, but the behavior
of the standard string construct is not quite what is needed. For these kinds of situations, Julia
provides non-standard string literals. A non-standard string literal looks like a regular
double-quoted string literal, but is immediately prefixed by an identifier, and doesn't behave
quite like a normal string literal.  Regular expressions, byte array literals and version number
literals, as described below, are some examples of non-standard string literals. Other examples
are given in the Metaprogramming section.

## Regular Expressions

Julia has Perl-compatible regular expressions (regexes), as provided by the [PCRE](http://www.pcre.org/)
library (a description of the syntax can be found [here](http://www.pcre.org/current/doc/html/pcre2syntax.html)). Regular expressions are related to strings in two ways: the obvious connection is that
regular expressions are used to find regular patterns in strings; the other connection is that
regular expressions are themselves input as strings, which are parsed into a state machine that
can be used to efficiently search for patterns in strings. In Julia, regular expressions are input
using non-standard string literals prefixed with various identifiers beginning with `r`. The most
basic regular expression literal without any options turned on just uses `r"..."`:

```julia
julia> re = r"^\s*(?:#|$)"
r"^\s*(?:#|$)"

julia> typeof(re)
Regex
```

To check if a regex matches a string, use `occursin`:

```julia
julia> occursin(r"^\s*(?:#|$)", "not a comment")
false

julia> occursin(r"^\s*(?:#|$)", "# a comment")
true
```

As one can see here, `occursin` simply returns true or false, indicating whether a
match for the given regex occurs in the string. Commonly, however, one wants to know not
just whether a string matched, but also *how* it matched. To capture this information about
a match, use the `match` function instead:

```julia
julia> match(r"^\s*(?:#|$)", "not a comment")

julia> match(r"^\s*(?:#|$)", "# a comment")
RegexMatch("#")
```

If the regular expression does not match the given string, `match` returns `nothing`
-- a special value that does not print anything at the interactive prompt. Other than not printing,
it is a completely normal value and you can test for it programmatically:

```julia
m = match(r"^\s*(?:#|$)", line)
if m === nothing
    println("not a comment")
else
    println("blank or comment")
end
```

If a regular expression does match, the value returned by `match` is a `RegexMatch`
object. These objects record how the expression matches, including the substring that the pattern
matches and any captured substrings, if there are any. This example only captures the portion
of the substring that matches, but perhaps we want to capture any non-blank text after the comment
character. We could do the following:

```julia
julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
RegexMatch("# a comment ", 1="a comment")
```

When calling `match`, you have the option to specify an index at which to start the
search. For example:

```julia
julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
RegexMatch("1")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
RegexMatch("2")

julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",11)
RegexMatch("3")
```

You can extract the following info from a `RegexMatch` object:

  * the entire substring matched: `m.match`
  * the captured substrings as an array of strings: `m.captures`
  * the offset at which the whole match begins: `m.offset`
  * the offsets of the captured substrings as a vector: `m.offsets`

For when a capture doesn't match, instead of a substring, `m.captures` contains `nothing` in that
position, and `m.offsets` has a zero offset (recall that indices in Julia are 1-based, so a zero
offset into a string is invalid). Here is a pair of somewhat contrived examples:

```julia
julia> m = match(r"(a|b)(c)?(d)", "acd")
RegexMatch("acd", 1="a", 2="c", 3="d")

julia> m.match
"acd"

julia> m.captures
3-element Vector{Union{Nothing, SubString{String}}}:
 "a"
 "c"
 "d"

julia> m.offset
1

julia> m.offsets
3-element Vector{Int64}:
 1
 2
 3

julia> m = match(r"(a|b)(c)?(d)", "ad")
RegexMatch("ad", 1="a", 2=nothing, 3="d")

julia> m.match
"ad"

julia> m.captures
3-element Vector{Union{Nothing, SubString{String}}}:
 "a"
 nothing
 "d"

julia> m.offset
1

julia> m.offsets
3-element Vector{Int64}:
 1
 0
 2
```

It is convenient to have captures returned as an array so that one can use destructuring syntax
to bind them to local variables:

```julia
julia> first, second, third = m.captures; first
"a"
```

Captures can also be accessed by indexing the `RegexMatch` object with the number or name of the
capture group:

```julia
julia> m=match(r"(?<hour>\d+):(?<minute>\d+)","12:45")
RegexMatch("12:45", hour="12", minute="45")

julia> m[:minute]
"45"

julia> m[2]
"45"
```

Captures can be referenced in a substitution string when using `replace` by using `\n`
to refer to the nth capture group and prefixing the substitution string with `s`. Capture group
0 refers to the entire match object. Named capture groups can be referenced in the substitution
with `\g<groupname>`. For example:

```julia
julia> replace("first second", r"(\w+) (?<agroup>\w+)" => s"\g<agroup> \1")
"second first"
```

Numbered capture groups can also be referenced as `\g<n>` for disambiguation, as in:

```julia
julia> replace("a", r"." => s"\g<0>1")
"a1"
```

You can modify the behavior of regular expressions by some combination of the flags `i`, `m`,
`s`, and `x` after the closing double quote mark. These flags have the same meaning as they do
in Perl, as explained in this excerpt from the [perlre manpage](http://perldoc.perl.org/perlre.html#Modifiers):

```
i   Do case-insensitive pattern matching.

    If locale matching rules are in effect, the case map is taken
    from the current locale for code points less than 255, and
    from Unicode rules for larger code points. However, matches
    that would cross the Unicode rules/non-Unicode rules boundary
    (ords 255/256) will not succeed.

m   Treat string as multiple lines.  That is, change "^" and "$"
    from matching the start or end of the string to matching the
    start or end of any line anywhere within the string.

s   Treat string as single line.  That is, change "." to match any
    character whatsoever, even a newline, which normally it would
    not match.

    Used together, as r""ms, they let the "." match any character
    whatsoever, while still allowing "^" and "$" to match,
    respectively, just after and just before newlines within the
    string.

x   Tells the regular expression parser to ignore most whitespace
    that is neither backslashed nor within a character class. You
    can use this to break up your regular expression into
    (slightly) more readable parts. The '#' character is also
    treated as a metacharacter introducing a comment, just as in
    ordinary code.
```

For example, the following regex has all three flags turned on:

```julia
julia> r"a+.*b+.*?d$"ism
r"a+.*b+.*?d$"ims

julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
RegexMatch("angry,\nBad world")
```

The `r"..."` literal is constructed without interpolation and unescaping (except for
quotation mark `"` which still has to be escaped). Here is an example
showing the difference from standard string literals:

```julia
julia> x = 10
10

julia> r"$x"
r"$x"

julia> "$x"
"10"

julia> r"\x"
r"\x"

julia> "\x"
ERROR: syntax: invalid escape sequence
```

Triple-quoted regex strings, of the form `r"""..."""`, are also supported (and may be convenient
for regular expressions containing quotation marks or newlines).

The `Regex()` constructor may be used to create a valid regex string programmatically.  This permits using the contents of string variables and other string operations when constructing the regex string. Any of the regex codes above can be used within the single string argument to `Regex()`. Here are some examples:

```julia
julia> using Dates

julia> d = Date(1962,7,10)
1962-07-10

julia> regex_d = Regex("Day " * string(day(d)))
r"Day 10"

julia> match(regex_d, "It happened on Day 10")
RegexMatch("Day 10")

julia> name = "Jon"
"Jon"

julia> regex_name = Regex("[\"( ]$name[\") ]")  # interpolate value of name
r"[\"( ]Jon[\") ]"

julia> match(regex_name," Jon ")
RegexMatch(" Jon ")

julia> match(regex_name,"[Jon]") === nothing
true
```

## Byte Array Literals

Another useful non-standard string literal is the byte-array string literal: `b"..."`. This
form lets you use string notation to express read only literal byte arrays -- i.e. arrays of
`UInt8` values. The type of those objects is `CodeUnits{UInt8, String}`.
The rules for byte array literals are the following:

  * ASCII characters and ASCII escapes produce a single byte.
  * `\x` and octal escape sequences produce the *byte* corresponding to the escape value.
  * Unicode escape sequences produce a sequence of bytes encoding that code point in UTF-8.

There is some overlap between these rules since the behavior of `\x` and octal escapes less than
0x80 (128) are covered by both of the first two rules, but here these rules agree. Together, these
rules allow one to easily use ASCII characters, arbitrary byte values, and UTF-8 sequences to
produce arrays of bytes. Here is an example using all three:

```julia
julia> b"DATA\xff\u2200"
8-element Base.CodeUnits{UInt8, String}:
 0x44
 0x41
 0x54
 0x41
 0xff
 0xe2
 0x88
 0x80
```

The ASCII string "DATA" corresponds to the bytes 68, 65, 84, 65. `\xff` produces the single byte 255.
The Unicode escape `\u2200` is encoded in UTF-8 as the three bytes 226, 136, 128. Note that the
resulting byte array does not correspond to a valid UTF-8 string:

```julia
julia> isvalid("DATA\xff\u2200")
false
```

As it was mentioned `CodeUnits{UInt8, String}` type behaves like read only array of `UInt8` and
if you need a standard vector you can convert it using `Vector{UInt8}`:

```julia
julia> x = b"123"
3-element Base.CodeUnits{UInt8, String}:
 0x31
 0x32
 0x33

julia> x[1]
0x31

julia> x[1] = 0x32
ERROR: setindex! not defined for Base.CodeUnits{UInt8, String}
[...]

julia> Vector{UInt8}(x)
3-element Vector{UInt8}:
 0x31
 0x32
 0x33
```

Also observe the significant distinction between `\xff` and `\uff`: the former escape sequence
encodes the *byte 255*, whereas the latter escape sequence represents the *code point 255*, which
is encoded as two bytes in UTF-8:

```julia
julia> b"\xff"
1-element Base.CodeUnits{UInt8, String}:
 0xff

julia> b"\uff"
2-element Base.CodeUnits{UInt8, String}:
 0xc3
 0xbf
```

Character literals use the same behavior.

For code points less than `\u80`, it happens that the
UTF-8 encoding of each code point is just the single byte produced by the corresponding `\x` escape,
so the distinction can safely be ignored. For the escapes `\x80` through `\xff` as compared to
`\u80` through `\uff`, however, there is a major difference: the former escapes all encode single
bytes, which -- unless followed by very specific continuation bytes -- do not form valid UTF-8
data, whereas the latter escapes all represent Unicode code points with two-byte encodings.

If this is all extremely confusing, try reading ["The Absolute Minimum Every
Software Developer Absolutely, Positively Must Know About Unicode and Character
Sets"](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/).
It's an excellent introduction to Unicode and UTF-8, and may help alleviate
some confusion regarding the matter.

## Version Number Literals

Version numbers can easily be expressed with non-standard string literals of the form [`v"..."`](@ref @v_str).
Version number literals create `VersionNumber` objects which follow the
specifications of [semantic versioning](https://semver.org/),
and therefore are composed of major, minor and patch numeric values, followed by pre-release and
build alpha-numeric annotations. For example, `v"0.2.1-rc1+win64"` is broken into major version
`0`, minor version `2`, patch version `1`, pre-release `rc1` and build `win64`. When entering
a version literal, everything except the major version number is optional, therefore e.g.  `v"0.2"`
is equivalent to `v"0.2.0"` (with empty pre-release/build annotations), `v"2"` is equivalent to
`v"2.0.0"`, and so on.

`VersionNumber` objects are mostly useful to easily and correctly compare two (or more) versions.
For example, the constant `VERSION` holds Julia version number as a `VersionNumber` object, and
therefore one can define some version-specific behavior using simple statements as:

```julia
if v"0.2" <= VERSION < v"0.3-"
    # do something specific to 0.2 release series
end
```

Note that in the above example the non-standard version number `v"0.3-"` is used, with a trailing
`-`: this notation is a Julia extension of the standard, and it's used to indicate a version which
is lower than any `0.3` release, including all of its pre-releases. So in the above example the
code would only run with stable `0.2` versions, and exclude such versions as `v"0.3.0-rc1"`. In
order to also allow for unstable (i.e. pre-release) `0.2` versions, the lower bound check should
be modified like this: `v"0.2-" <= VERSION`.

Another non-standard version specification extension allows one to use a trailing `+` to express
an upper limit on build versions, e.g.  `VERSION > v"0.2-rc1+"` can be used to mean any version
above `0.2-rc1` and any of its builds: it will return `false` for version `v"0.2-rc1+win64"` and
`true` for `v"0.2-rc2"`.

It is good practice to use such special versions in comparisons (particularly, the trailing `-`
should always be used on upper bounds unless there's a good reason not to), but they must not
be used as the actual version number of anything, as they are invalid in the semantic versioning
scheme.

Besides being used for the `VERSION` constant, `VersionNumber` objects are widely used
in the `Pkg` module, to specify packages versions and their dependencies.

## Raw String Literals

Raw strings without interpolation or unescaping can be expressed with
non-standard string literals of the form `raw"..."`. Raw string literals create
ordinary `String` objects which contain the enclosed contents exactly as
entered with no interpolation or unescaping. This is useful for strings which
contain code or markup in other languages which use `$` or `\` as special
characters.

The exception is that quotation marks still must be escaped, e.g. `raw"\""` is equivalent
to `"\""`.
To make it possible to express all strings, backslashes then also must be escaped, but
only when appearing right before a quote character:

```julia
julia> println(raw"\\ \\\"")
\\ \"
```

Notice that the first two backslashes appear verbatim in the output, since they do not
precede a quote character.
However, the next backslash character escapes the backslash that follows it, and the
last backslash escapes a quote, since these backslashes appear before a quote.