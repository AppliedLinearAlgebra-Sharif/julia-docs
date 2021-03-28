# متغیرها

یک متغیر در جولیا(و کلاً در برنامه نویسی) نامی است که به یک مقدار اختصاص می‌دهیم. این کار برای این مفید است که بخواهید مقدار مشخصی(که می‌تواند نتیجه محاسباتی باشد) را در حافظه برای استفاده در آینده ذخیره کنید. به عنوان مثال:

```julia-repl
# Assign the value 10 to the variable x
julia> x = 10
10

# Doing math with x's value
julia> x + 1
11

# Reassign x's value
julia> x = 1 + 1
2

# You can assign values of other types, like strings of text
julia> x = "Hello World!"
"Hello World!"
```

جولیا یک سیستم بسیار انعطاف پذیر برای نامگذاری متغیرها فراهم کرده است. توجه داشته باشید که متغیرها در جولیا `case-sensitive` هستند به این معنی که جولیا بین حروف بزرگ و کوچک تفاوت قائل می‌شود و مثلا متغیر `a` با `A` یکی نیست. همچنین جولیا بر اساس نام متغیرها برای آن‌ها تمایزی قائل نمی‌شود. برای مثال:

```jldoctest
julia> x = 1.0
1.0

julia> y = -3
-3

julia> Z = "My string"
"My string"

julia> customary_phrase = "Hello world!"
"Hello world!"

julia> UniversalDeclarationOfHumanRightsStart = "人人生而自由，在尊严和权利上一律平等。"
"人人生而自由，在尊严和权利上一律平等。"
```

برای نام گذاری متغیرها می‌توانید از یونیکد استفاده کنید( یونیکدهای مجاز در UTF-8):

```jldoctest
julia> δ = 0.00001
1.0e-5

julia> 안녕하세요 = "Hello"
"Hello"
```

در محیط REPL جولیا و بسیاری از محیط‌های ویرایش، شما بسیاری از نمادهای ریاضی یونیکد را می‌توانید با نوشتن بک اسلش شده نام نمادهای LaTeX و کلید tab استفاده کنید. برای مثال، متغیر با نام `δ` می‌تواند به وسیله نوشتن `delta\`–_فشردن کلید tab_ به نمایش درآید. همینطور برای نوشتن `α̂₂` بنویسید `alpha\`–_فشردن کلید تب_–`hat\`–_فشردن کلید تب_–`2_\`–_فشردن کلید تب_. اگر شما نمادی را پیدا کردید(مثلاً در کدهای دیگر) و نمی‌دانستید که چگونه آن را به نمایش در آورید، کافیست به جولیا REPL مراجعه کرده و بنویسید `?` و سپس نماد را paste کنید و enter را بزنید. 

جولیا حتی به شما اجازه می‌دهد تا ثابت‌ها و توابع درون-ساخت خود جولیا را نیز در صورت لزوم تغییر دهید. هرچند که این کار برای جلوگیری از سردرگمی اصلاً توصیه نمی‌شود:

```jldoctest
julia> pi = 3
3

julia> pi
3

julia> sqrt = 4
4
```

مطلب بالا فقط زمانی قابل انجام است که آن ثابتی که قرار است تغییرش دهید، در حال استفاده نباشد. اگر در حال استفاده باشد، جولیا به شما خطا می‌دهد:

```jldoctest
julia> pi
π = 3.1415926535897...

julia> pi = 3
ERROR: cannot assign a value to variable MathConstants.pi from module Main

julia> sqrt(100)
10.0

julia> sqrt = 4
ERROR: cannot assign a value to variable Base.sqrt from module Main
```

## نام‌های مجاز برای متغیرها در جولیا

نام متغیر باید با یک حرف (A-Z یا a-z)، زیرخط( _ ) یا یک زیرمجموعه یونیکد بزرگتر از 00A0، شروع شود؛ به طور خاص، [دسته‌های کاراکتر یونیکد](http://www.fileformat.info/info/unicode/category/index.htm) (حروف) Lu/Ll/Lt/Lm/Lo/Nl یا (ارز و سایر نمادها) Sc / So و چند کاراکتر letter-like (مانند یک زیر مجموعه از نمادهای ریاضی Sm) مجاز هستند. کاراکترهای بعدی نیز ممکن است مجاز باشند! ارقام (0-9 و سایر کاراکترها در دسته Nd / No)، همچنین سایر نقاط کد یونیکد: تعاریف و سایر علامت‌های اصلاح (دسته‌های Mn / Mc / Me / Sk)، برخی از نقطه گذاری‌های اتصال دهنده (دسته Pc)، پرایمزها و چند کاراکتر دیگر هم مجوز دارند که در نام گذاری متغیر شرکت کنند.

عملگرهایی مثل `+` هم شناسه‌های معتبری هستند اما از آنها باید به صورت خاصی استفاده کرد. در برخی موارد می توان از عملگرها مثل متغیرها استفاده کرد. برای مثال `(+)` به تابع جمع اشاره دارد و `(+) = f` آن را دوباره مقداردهی می‌کند. بسیاری از عملگرهای infix یونیکد(در دسته Sm) مانند `⊕`، به عنوان عملگر infix(یعنی عملگری که میان دو عملوند قرار می گیرد) تعریف شده و کاربر آنها را می تواند مشخص کند. برای مثال `const ⊗ = kron` عملگر `⊗` را به عنوان عملگر infix ضرب کرونکر معرفی می کند(یعنی اگر `⊗` بین دو عملوند قرار گیرد، عمل ضرب کرونکر را بر روی آن دو عملوند اعمال می کند). عملگرها همچنین می توانند به وسیله اصلاح علامت، اولویت‌ها و زیرنویس‌ها/بالا نویس‌ها جایگزین شوند. برای مثال `+̂ₐ″` به عنوان یک عملگر infix با همان اولویت `+` عمل می کند.

تنها نام‌های نامجاز برای متغیرها در جولیا نام‌هایی هستند که برای اسامی درون-ساخت به کار رفته اند:

```julia-repl
julia> else = false
ERROR: syntax: unexpected "else"

julia> try = "No"
ERROR: syntax: unexpected "="
```

Some Unicode characters are considered to be equivalent in identifiers.
Different ways of entering Unicode combining characters (e.g., accents)
are treated as equivalent (specifically, Julia identifiers are [NFC](http://www.macchiato.com/unicode/nfc-faq)-normalized).
The Unicode characters `ɛ` (U+025B: Latin small letter open e)
and `µ` (U+00B5: micro sign) are treated as equivalent to the corresponding
Greek letters, because the former are easily accessible via some input methods.

## Stylistic Conventions

While Julia imposes few restrictions on valid names, it has become useful to adopt the following
conventions:

  * Names of variables are in lower case.
  * Word separation can be indicated by underscores (`'_'`), but use of underscores is discouraged
    unless the name would be hard to read otherwise.
  * Names of `Type`s and `Module`s begin with a capital letter and word separation is shown with upper
    camel case instead of underscores.
  * Names of `function`s and `macro`s are in lower case, without underscores.
  * Functions that write to their arguments have names that end in `!`. These are sometimes called
    "mutating" or "in-place" functions because they are intended to produce changes in their arguments
    after the function is called, not just return a value.

For more information about stylistic conventions, see the [Style Guide](style-guide.html).
