# Functions

In Julia, a function is an object that maps a tuple of argument values to a return value. Julia
functions are not pure mathematical functions, because they can alter and be affected
by the global state of the program. The basic syntax for defining functions in Julia is:

فصل 8  توابع
در جولیا ، یک تابع یک شی است که تعداد زیادی از مقادیر آرگومان را به یک مقدار بازگشتی ترسیم می کند. توابع جولیا توابع ریاضی خالص نیستند ، زیرا آنها می توانند وضعیت کلوبال برنامه را تغییر داده و تحت تأثیر قرار دهند. سینتکس اساسی برای تعریف توابع در جولیا عبارت است از:


```julia
julia> function f(x,y)
           x + y
       end
f (generic function with 1 method)
```

This function accepts two arguments `x` and `y` and returns the value
of the last expression evaluated, which is `x + y`.

There is a second, more terse syntax for defining a function in Julia. The traditional function
declaration syntax demonstrated above is equivalent to the following compact "assignment form":

این تابع دو آرگومان x و y را می پذیرد و مقدار آخرین عبارت ارزیابی شده را برمی گرداند ، یعنی
x + y .
یک نحو دوم و مختصر برای تعریف یک تابع در جولیا وجود دارد. نحو بیان تابع سنتی
نشان داده شده در بالا معادل "فرم واگذاری" فشرده زیر است:


```julia
julia> f(x,y) = x + y
f (generic function with 1 method)
```

In the assignment form, the body of the function must be a single expression, although it can
be a compound expression (see [Compound Expressions](@ref man-compound-expressions)). Short, simple function definitions
are common in Julia. The short function syntax is accordingly quite idiomatic, considerably reducing
both typing and visual noise.

A function is called using the traditional parenthesis syntax:

در فرم انتساب assigment، بدنه تابع باید یک عبارت واحد باشد ، اگرچه می تواند یک ترکیب باشد بیان (نگاه کنید به ترکیب 
اصطلاحات). تعریف کوتاه و ساده تابع در جولیا معمول است. تابع کوتاه   بر این اساس کاملا کاربردی است و به طور قابل توجهی هم تایپ و هم سر و صدای بصری را کاهش می دهد.
یک تابع با استفاده از نحو پرانتز سنتی فراخوانی می شود:


```julia
julia> f(2,3)
5
```

Without parentheses, the expression `f` refers to the function object, and can be passed around
like any other value:

بدون پرانتز ، عبارت f به شی تابع اشاره دارد و می تواند مانند سایر مقادیر به اطراف منتقل شود:

```julia
julia> g = f;

julia> g(2,3)
5
```

As with variables, Unicode can also be used for function names:

همانند متغیرها ، از Unicode می توان برای نام تابع نیز استفاده کرد:

```julia
julia> ∑(x,y) = x + y
∑ (generic function with 1 method)

julia> ∑(2, 3)
5
```

## Argument Passing Behavior

Julia function arguments follow a convention sometimes called "pass-by-sharing", which means that
values are not copied when they are passed to functions. Function arguments themselves act as
new variable *bindings* (new locations that can refer to values), but the values they refer to
are identical to the passed values. Modifications to mutable values (such as `Array`s) made within
a function will be visible to the caller. This is the same behavior found in Scheme, most Lisps,
Python, Ruby and Perl, among other dynamic languages.

8.1  چگونگی دادن آرگومان ها
آرگومان های توابع جولیا از قراردادی پیروی می کنند که گاهی اوقات "گذر از طریق به اشتراک گذاری" نامیده می شود ، به این معنی که مقادیروقتی به توابع منتقل می شوند ، کپی نمی شوند. آرگومان های تابعی خود به عنوان صحافی متغیر جدید عمل می کنند (مکانهای جدیدی که می توانند به مقادیر اشاره کنند) ، اما مقادیری که آنها به آنها اشاره می کنند با مقادیر داده شده یکسان هستند. اصلاحات به مقادیر قابل تغییر (مانند آرایه ها) ساخته شده در یک تابع برای صدا کننده قابل مشاهده است. این است همان رفتاری که در Scheme ، بیشتر Lisps ، Python ، Ruby و Perl و سایر زبانهای پویا مشاهده می شود.

## The `return` Keyword

The value returned by a function is the value of the last expression evaluated, which, by default,
is the last expression in the body of the function definition. In the example function, `f`, from
the previous section this is the value of the expression `x + y`.
As an alternative, as in many other languages,
the `return` keyword causes a function to return immediately, providing
an expression whose value is returned:

8.2   کلید واژه return
مقدار برگردانده شده توسط یک تابع مقدار آخرین عبارت ارزیابی شده است که به طور پیش فرض آخرین عبارت بیان شده در متن و بدنه تعریف تابع است. در تابع مثال ، f ، از بخش قبلی این  مقدار عبارت x + y است .به عنوان یک گزینه ، مانند بسیاری از زبانهای دیگر ، کلمه کلیدی return باعث بازگشت فوری تابع می شود با ارائه عبارتی که مقدار آن برگردانده می شود:


```julia
function g(x,y)
    return x * y
    x + y
end
```

Since function definitions can be entered into interactive sessions, it is easy to compare these
definitions:

از آنجا که تعاریف تابع را می توان در جلسات تعاملی وارد کرد ، مقایسه این تعاریف آسان است:

```julia
julia> f(x,y) = x + y
f (generic function with 1 method)

julia> function g(x,y)
           return x * y
           x + y
       end
g (generic function with 1 method)

julia> f(2,3)
5

julia> g(2,3)
6
```

Of course, in a purely linear function body like `g`, the usage of `return` is pointless since
the expression `x + y` is never evaluated and we could simply make `x * y` the last expression
in the function and omit the `return`. In conjunction with other control flow, however, `return`
is of real use. Here, for example, is a function that computes the hypotenuse length of a right
triangle with sides of length `x` and `y`, avoiding overflow:

البته ، در یک بدنه تابع کاملاً خطی مانند g ، ، استفاده از  returnبی معنی است از آنجا که عبارت x + y هرگز ارزیابی نمی شود و ما می توانیم به سادگی x * y را به آخرین عبارت در تابع تبدیل کنیم و بازگشت را حذف کنیم. همراه با جریان کنترل دیگر ،  return کاربرد واقعی دارد. به عنوان مثال ، در اینجا تابعی وجود دارد که طول هیپوتنوز مثلث قائم الزاویه را با اضلاع طول x و y محاسبه می کند ، و جلوگیری از سرریز:

```julia
julia> function hypot(x,y)
           x = abs(x)
           y = abs(y)
           if x > y
               r = y/x
               return x*sqrt(1+r*r)
           end
           if y == 0
               return zero(x)
           end
           r = x/y
           return y*sqrt(1+r*r)
       end
hypot (generic function with 1 method)

julia> hypot(3, 4)
5.0
```

There are three possible points of return from this function, returning the values of three different
expressions, depending on the values of `x` and `y`. The `return` on the last line could be omitted
since it is the last expression.

سه نقطه بازگشت ممکن از این تابع وجود دارد که مقادیر سه عبارت مختلف را برمی گرداند ،و
به مقادیر x و y بستگی دارد. بازگشت در آخرین خط ممکن است حذف شود زیرا آخرین عبارت است.


### Return type

A return type can be specified in the function declaration using the `::` operator. This converts
the return value to the specified type.

نوع بازگشت return 
با استفاده از عملگر :: می توان یک نوع  returnرا در تابع مشخص کرد. این مقدار برگشتی را به نوع مشخص شده تبدیل می کند.


```julia
julia> function g(x, y)::Int8
           return x * y
       end;

julia> typeof(g(1, 2))
Int8
```

This function will always return an `Int8` regardless of the types of `x` and `y`.
See Type Declarations for more on return types.

این تابع بدون توجه به انواع x و y همیشه یک Int8 را برمی گرداند. برای اطلاعات بیشتر به
   Type declarationمراجعه کنید


### Returning nothing

For functions that do not need to return a value (functions used only for some side effects),
the Julia convention is to return the value `nothing`:

برای توابعی که نیازی به بازگشت مقدار ندارند (توابع فقط برای برخی از عوارض جانبی استفاده می شوند) ، قرارداد جولیا این است که مقدارnothing را بازگرداند:

```julia
function printx(x)
    println("x = $x")
    return nothing
end
```

This is a *convention* in the sense that `nothing` is not a Julia keyword
but a only singleton object of type `Nothing`.
Also, you may notice that the `printx` function example above is contrived,
because `println` already returns `nothing`, so that the `return` line is redundant.

There are two possible shortened forms for the `return nothing` expression.
On the one hand, the `return` keyword implicitly returns `nothing`, so it can be used alone.
On the other hand, since functions implicitly return their last expression evaluated,
`nothing` can be used alone when it's the last expression.
The preference for the expression `return nothing` as opposed to `return` or `nothing`
alone is a matter of coding style.

برگرداندن nothing
برای توابعی که نیازی به بازگشت مقدار ندارند (توابع فقط برای برخی از عوارض جانبی استفاده می شوند) ، قرارداد جولیا این است که مقدارnothing را بازگرداند:
این یک قرارداد است به این معنا که nothing  یک کلمه کلیدی در جولیا نیست بلکه تنها یک شی از نوع Nothing است.همچنین ، ممکن است متوجه شوید که تابع printx در بالا ساخته شده است ، زیرا println از قبل nothing برمی گرداند ، به طوری که خط بازگشت اضافی است.
دو فرم کوتاه ممکن برای بیان عبارت nothing وجود دارد. از یک طرف ، کلمه کلیدی  returnبه طور ضمنی چیزی را بر نمی گرداند ، بنابراین می تواند به تنهایی استفاده شود. از طرف دیگر ، از آنجا که توابع به طور ضمنی آخرین عبارتی که ارزیابی می شودرا برمی گردانند ، nothing به تنهایی قابل استفاده است  ،وقتی آخرین عبارت است. ترجیح برای کدام روش به موضوع سبک کد گذاری بستگی دارد .


## عملگرها تابع هستند

در جولیا اکثر عملگرها صرفا توابعی هستند که با سینتکس خاصی حمایت میشوند.
(استثناها عملگرهایی به خصوصی که برای ارزیابی منطقی بکار میروند هستند مانند
`&&` و `||`.
این عملگرها نمیتوانند تابع باشند.
از آنجا که این عملگرها نیاز دارند که عملوندهایی آنها قبل از ارزیابی آنها، ارزیابی نشده باشند.)
بر این اساس میتوان از آنها به شکل آرگومان-پرانتز نیز استفاده کرد مانند خیلی دیگر از توابع :

8.3   عملگرها تابع هستند
در جولیا ، اکثر عملگرها فقط توابع با پشتیبانی از سینتکس ویژه هستند. (استثنائات عملگرهایی که دارای ارزیابی ویژه هستند مانند && و ||. این عملگرها به دلیل ارزیابی اتصال کوتاه نمی توانند توابع باشند مستلزم آن است که عملوندهای آنها قبل از ارزیابی اپراتور ارزیابی نشوند.) بر این اساس ، شما همچنین می توانید آنها را با استفاده از لیست های آرگومان پرانتز اعمال کنید ، دقیقاً مانند سایر توابع:


```julia
julia> 1 + 2 + 3
6

julia> +(1,2,3)
6
```

فرم infix 
دقیقا معادل است با فرم کاربرد توابع در حقیقت فرم دهنده تبدیل شده است به تولید کردن فراخوانی تابع
و این به این معنی است که شما میتوانید به هر کس بسپارید و بعد بگذرید.
عملگرهایی مانند `+` و `*` دقیقا همانند بقیه عملگرها به همین شکل هستند:

فرم infix دقیقاً معادل تابع فرم است - در واقع فرم اول برای تولید به صورت فراخوانی داخلی تابع تجزیه می شود. این همچنین به این معنی است که شما می توانید اپراتورهایی مانند + و * را تعیین و دقیقاً مانند سایر مقادیر توابع وارد کنید:

```julia
julia> f = +;

julia> f(1,2,3)
6
```

زیر اسم `f`, تابع نمادگذاری infix را پشتیبانی نمیکند .

با این حال ، با نام f ، این تابع از نماد infix  پشتیبانی نمی کند.

## عملگرهایی با اسامی خاص

چند اسم و عبارت خاص نیز متناظر با توابعی هستند که اسم مشخضی ندارند به مانند زیر :

| Expression        | Calls                   |
|:----------------- |:----------------------- |
| `A B C ...]`     | [`hcat`          |
| `A; B; C; ...]`  | [`vcat`          |
| `A B; C D; ...]` | [`hvcat`         |
| `A'`              | `adjoint`       |
| `Ai]`            | [`getindex`      |
| `Ai] = x`        | [`setindex!`     |
| `A.n`             | [`getproperty`](@ref Base.getproperty) |
| `A.n = x`         | [`setproperty!`](@ref Base.setproperty!) |

## توابع ناشناس

توابع در جولیا [اشیا کلاس اولیه هستند](https://en.wikipedia.org/wiki/First-class_citizen):

آنها میتوانند به متغیرها نسبت داده شونذ, و بوسیله توابع استاندارد مخصوص از متغیرها فراخوانی شوند.
آنها میتوانند به عنوان آرگومان استفاده شوند و به عنوان مقدار برگردانده شوند.
همچنین بوسیله سینتکس زیر میتوان توابعی بدون اسم را به صورت ناشناس ساخت.

```julia
julia> x -> x^2 + 2x - 1
#1 (generic function with 1 method)

julia> function (x)
           x^2 + 2x - 1
       end
#3 (generic function with 1 method)
```

به این شکل تابعی با ورودی  `x` میسازیم که مقدار چندجمله ای  `x^2 +
2x - 1`را خروجی میدهد. توجه کنید که نتیجه یک generic function است, ولی با compiler-generated
بر اساس اعداد متوالی.

استفاده اصلی توابع ناشناس، انتقال دادن آن به بقیه توابع به عنوان ورودی است.
یک مثال معروف
`map`,
است که به هر جزیی از آرایه یک تابع نسبت میدهد
و یک آرایه خروجی میدهد که متناظر با مقادیر خروجی همان آرایه هاست:

```julia
julia> map(round, [1.2, 3.5, 1.7])
3-element Vector{Float64}:
 1.0
 4.0
 2.0
```

این اشکالی ندارد اگر یک تابع نامدار روی انتقالی که همین الان در وجود دارد تاثیر بگذارد به عنوان اولین ورودی
`map`. 
به هر حال یک تابع آمده برای استفاده اسم دار وجود ندارد.
 در این موقعیت، ساختار توابع ناشناس به ما اجاه میدهد
که به راحتی یک تابع تک مصرف بدون نیاز به نام بسازیم :


```julia
julia> map(x -> x^2 + 2x - 1, [1, 3, -1])
3-element Vector{Int64}:
  2
 14
 -2
```
تابع ناشناسی که میتواند چند ورودی را بگیرید میتواند به وسیله سینتکس رو به رو نوشته شود
 `(x,y,z)->2x+y-z`.
 یک تابع بدون ورودی ناشناس به شکل رو به رو نوشته میشود :
 `()->3`. 
 ایده استفاده از تابع بدون ورودی شاید عجیب بنظر برسد اما برای به تاخیر انداختن یک محاصبه بسیار کارا است. در این مورد قسمتی از کد درون یک تابع بدون ورودی گیر می افتد.

برای مثال تابع `get`:

```julia
get(dict, key) do
    # default value calculated here
    time()
end
```
کد بالا معادل است با صدا زدن
 `get`
 با استفاده از تابع ناشناسی که کد
 مابین
 `do` و `end`
 را در بردارد مانند :

```julia
get(()->time(), dict, key)
```

صدا زدن
`time`
با استفاده از تابع بدون ورودی ناشناسی به تاخیر افتاده است و وقتی صدا زده میشود که کلید درخواست شده در
 `dict`نباشد.

## چندتایی ها
جولیا یک ساختار داخلی به اسم "چندتایی ها" دارد که بسیار به ورودی های توابع و خروجی های آن ها نیز مرتبط است.
یک "چندتایی" عبارت است از نگهدارنده ای با طول ثابت که میتواند هر مقداری را درخود نگه دارد
اما نمیتواند اصلاح شود یا به عبارتی اصلاح پذیر نیست.
چندتایی ها یوسیله کاما و پرانتزها ساخته میشوند و میتوانند با عدد دهی مدیریت شوند.


```julia
julia> (1, 1+1)
(1, 2)

julia> (1,)
(1,)

julia> x = (0.0, "hello", 6*7)
(0.0, "hello", 42)

julia> x[2]
"hello"
```

توجه کنید که یک چندتایی به طول یک باید بوسیله کاما و به شکل
 `(1,)`
 نوشته شود در حالی که
 `(1)` 
 صرفا یک مقدار پرانتز شده است.

`()` 
نشان دهنده یک چندتایی به طول صفر است.


## چندتایی های اسم دار


مولفه های یک چندتایی میتوانند نامگذاری شوند، به این ترتیب یک "چندتایی اسم دار" بدست می آید :


```julia
julia> x = (a=2, b=1+2)
(a = 2, b = 3)

julia> x[1]
2

julia> x.a
2
```

چندتایی های اسم دار بسیار شبیه به چندتایی ها هستند غیر از اینکه به قسمت های مختلف میتوان بوسیله اسم آنها دسترسی داشت بوسیله سینتکس
 (`x.a`) البته علاوه بر سیستم دسترسی عادی که بوسیله پلاک هر عضو انجام میشد.
(`x[1]`).

## مقادیر بازگشتی چندگانه

در جولیا میتوان یک چندتایی را برگرداند برای شبیه سازی برگرداندن چندین مقدار. همچنین چندتایی ها میتوانند ساخته یا محذوف شوند بدون نیاز به پرانتز به این ترتیب این تفکر بوجود میآید که چند مقدار مختلف به صورت یک چندتایی واحد برگردانده میشود. برای مثال تابع زیر یک حفت مقدار برمیگرداند : 

```julia
julia> function foo(a,b)
           a+b, a*b
       end
foo (generic function with 1 method)
```


If you call it in an interactive session without assigning the return value anywhere, you will
see the tuple returned:

```julia
julia> foo(2,3)
(5, 6)
```

یک استفاده معمول از این جفت مقدارها، زمانی است که بخواهید یک مقدار را یه یکسری مقدار دیگر تجزیه کنید.
جولیا امکان استفاده از چندتایی های مخرب را به شما میدهد که اتفاقاتی مانند زیر را تسهیل میکند :


```julia
julia> x, y = foo(2,3)
(5, 6)

julia> x
5

julia> y
6
```

همچنین شما میتوانید چند مقدار مختلف را به استفاده از کلیدوازه 
`return`
برگردانید:

```julia
function foo(a,b)
    return a+b, a*b
end
```

این دقیقا همان تاثیری را دارد که تعریف قبلی 
`foo`
داشت.

## تخریب متغیر

قابلیت تخریب میتواند در ساختار یک توابع استفاده شود.
اگر اسم یک ساختار تابع به عتوان چندتایی نوشته شود
 (e.g. `(x, y)`)
 بجای یک نماد، آنگاه نامگذاری
`(x, y) = argument`
میتواند یک جایگزین برای ما باشد :


```julia
julia> minmax(x, y) = (y < x) ? (y, x) : (x, y)

julia> gap((min, max)) = max - min

julia> gap(minmax(10, 2))
8
```

به پرانتزهای اضافی در تعریف
`gap`
توجه کنید.
بدون آن
`gap`
 یک تابع دو متغیره میشود
و این مثال کار نمیکند.


## Varargs توابع

معمولا این کار راحتی است که شما تابعی بنویسید که تعداد تصادفی ورودی دریافت کند.
این توابع را به صورت سنتی توابع
 "varargs"
 مینامند که کوتاه شده عبارت
 "variable number
of arguments"
است.
شما میتوانید به این شکل همچین توابعی را تعریف کنید :


```julia
julia> bar(a,b,x...) = (a,b,x)
bar (generic function with 1 method)
```

متغیرهای
 `a` و `b` 
کران های دو متغیر اول هستند و متغیر

`x` 
یک کران برای مجموعه قابل تکراری از صفر یا مقادیر بیشتری است که به
`bar`
داده شده انذ بعد از دو ورودی اول :

```julia
julia> bar(1,2)
(1, 2, ())

julia> bar(1,2,3)
(1, 2, (3,))

julia> bar(1, 2, 3, 4)
(1, 2, (3, 4))

julia> bar(1,2,3,4,5,6)
(1, 2, (3, 4, 5, 6))
```

در همه این موارد
`x` 
یک کران برای برای چندتایی ای است که به 
 `bar`
 داده شده است.
میتوان تعداد متغیرهایی که به عنوان ورودی داده میشوند را محدود کرد.
به این موضوع بعدا در قسمت
 Parametrically-constrained Varargs methods
 میپردازیم.
در روی دیگر سکه معمولا
 "splat" 
 مقادیر نگه داشته شده درون یک مجموعه تکرارشونده به یک تابع
 بازخوانی به عنوان یک متغیر خاص کار راحتی است.
برای انجام این میتوان از
 `...` 
 استفاده کرد در تابع فراخوانی :


```julia
julia> x = (3, 4)
(3, 4)

julia> bar(1,2,x...)
(1, 2, (3, 4))
```

در این مورد یک چندتایی از مفادیر به یک  
 varargs call
 متصل میشوند دقیقا جایی که تعداد متغیرهای ورودی میرود.
 هر چند که این مورد لازم نیست.

```julia
julia> x = (2, 3, 4)
(2, 3, 4)

julia> bar(1,x...)
(1, 2, (3, 4))

julia> x = (1, 2, 3, 4)
(1, 2, 3, 4)

julia> bar(x...)
(1, 2, (3, 4))
```

همچنین آبجکت تکرارشونده که به صورت یک تابع فراخوانی آمده است نباید لزوما چندتایی باشد :

```julia
julia> x = [3,4]
2-element Vector{Int64}:
 3
 4

julia> bar(1,2,x...)
(1, 2, (3, 4))

julia> x = [1,2,3,4]
4-element Vector{Int64}:
 1
 2
 3
 4

julia> bar(x...)
(1, 2, (3, 4))
```

همچنین تابعی که ورودی هایش به صورت پراکنده باشند نباید لزوما varagas باشد. 
البته معمولا هست! :


```julia
julia> baz(a,b) = a + b;

julia> args = [1,2]
2-element Vector{Int64}:
 1
 2

julia> baz(args...)
3

julia> args = [1,2,3]
3-element Vector{Int64}:
 1
 2
 3

julia> baz(args...)
ERROR: MethodError: no method matching baz(::Int64, ::Int64, ::Int64)
Closest candidates are:
  baz(::Any, ::Any) at none:1
```


همانطور که میتوانید ببینید اگر تعداد اشتباهی المنت در یک نگهدارنده پراکنده باشند آنگاه تابع فراخوانی از درست کار نمیکند دقیقا همانطور که اگر به تعداد بیش از حد ورودی بگیرد کار نمیکند.


## ورودی های اختیاری

معمولا میتوان متغیرهای پیشفرض مناسبی را برای ورودی های تابع تهیه کرد. این میتواند کاربر را از اینکه بخواهد در هر فراخوانی مقدارهای یکسانی را به صورت اضافه وارد کند آسوده کند.
برای مثال تابع
 `Date(y, [m, d])`
از `Dates` 
میسازد یک
 `Date` 
برای سال داده شده `y`, ماه داده شده `m` و روز داده شده `d`.
همچنین ورودی های
`m` و `d` 
اخیتاری هستند و مقدار پیشفرضشان `1`
میباشد.
این ایده میتواند به شکل بیان شود:

```julia
function Date(y::Int64, m::Int64=1, d::Int64=1)
    err = validargs(Date, y, m, d)
    err === nothing || throw(err)
    return Date(UTD(totaldays(y, m, d)))
end
```

نگاه کنید این تعریف متدهای دیگری از
 `Date` 
 را فراخوانی میکند.
 تابع هایی که یک متغیر به عنوان ورودی میگیرند از نوع `UTInstant{Day}`.

با این تعریف، تابع میتواند به همراه یک یا دو یا سه متغیر فراخوانی شود و  
`1` 
به صورت اتوماتیک وقتی یک متعیر یا دو متغیر آمده باشند جایگذاری میشود :


```julia
julia> using Dates

julia> Date(2000, 12, 12)
2000-12-12

julia> Date(2000, 12)
2000-12-01

julia> Date(2000)
2000-01-01
```

متغیرهای اختیاری در حقیقت صرفا یک سیتکس برای نوشتن چندین متد مختلف با تعداد مختلف ورودی میباشند.
(نت درون متغیرهای اختیاری و کلیدی را ببینید)
این میتواند برای تابع
 `Date` چک شود با فراخوانی تابع `methods`.

## ورودی های کلیدی

بعضی از تابع ها به تعداد زیادی ورودی نیاز دارند یا اینکه تعداد زیادی behavior دارند.
با یادآوری اینکه فراخوانی این توابع بسیار سخت بود.
ورودی های کلیدی میتوانند این اینترفیس های پیچیده را راحت تر کنند برای استفاده و گسترش پیدا کنند بوسیله شناساندن نام ها بجای جایگاه ها.

برای مثال تابع
`plot`
را در نظر بگیرید که یک خط را برای ما رسم میکند. 
این تابع شاید تعداد زیادی آپشن داشته باشد برای کنترل کرد استایل خط، ذخامت، رنگ و غیره. اگر این تابع ورودی های کلیدی را بپذیرد یک فراخوانی ممکن میتواند به این شکل باشد
 `plot(x, y, width=2)`,
 که یعنی تصمیم گرفتیم فقط ذخامت آن را تعیین کنیم. توجه کنید که این کار دو هدف را براورده میکند. این فراخوانی راحت تر است برای خوانده شدن  و همچنین میتوان هر تعداد ورودی را به هر ترتیب دلخواهی وارد کرد.

ورودی های این توابع بوسیله سمیکالم از هم جدا میشوند :


```julia
function plot(x, y; style="solid", width=1, color="black")
    ###
end
```

زمانی که تابع فراخوانی میشود سمیکالم اختیاری است : یک نفر میتواند به هر دو شکل رو به رو فراخوانی را انجام دهد.
 `plot(x, y, width=2)`
و`plot(x, y;dth=2)`,
ولی حالت اول معمول تر است.
سمیکالم ها به صورت قطعی وقتی نیازند که میخواهیم به تابع های varargs متغیر را پاس دهیم یا اینکه کلمه کیلیدی را به شکل زیر محاسبه کنیم.

مقادیر پیشفرض متغیرهای کلیدی فقط وقتی تهیه میشوند که ضروری باشد و با ترتیب چپ به راست. همچنین میتواند به ورودی های کلیدی قبلی ربط داشت باشند.

انواع ورودی های کلیدی میتوانند به شکل زیر ساخته شوند.

```julia
function f(;x::Int=1)
    ###
end
```

ورودی های کلیدی همچنین میتوانند در تابع های varargs نیز استفاده شوند.

```julia
function plot(x...; style="solid")
    ###
end
```

کلمات کلیدی اضافی نیز میتوانند با استفاده
 `...`, 
 در توابع varargs جمع آوری شوند :

```julia
function f(x; y=0, kwargs...)
    ###
end
```

درون `f`, `kwargs` 
یک متغیر کلیدی تکرار شونده تغییرناپذیر روی یک چندتایی اسم دار خواهد بود.
چندتایی های نامدار(به خوبی لغتنامه با کلید `Symbol`) میتوانند به شکل ورودی کلیذی به وسیله سمیکالم در یک فراخوانی بیایند مانند
`f(x, z=1; kwargs...)`.

اگر یک ورودی کلیدی در تعریف متد به صورت مقدار پیشفرض نباشد، آنگاه یک چیز خواسته شده است: یک
 `UndefKeywordError` 
 که یک خطا است پرتاب خواهد شد اگر مقدار آن را مشخص نکنید :


```julia
function f(x; y)
    ###
end
f(3, y=5) # ok, y is assigned
f(3)      # throws UndefKeywordError(:y)
```

میتوان `key => value` اکسپرشن بعد از یک سمیکالم. برای مثال, `plot(x, y; :width => 2)`
معادل است با `plot(x, y, width=2)`. 
در مواقعی که اسم کلیدی در هنگام ران تایم محاسبه شده این کار بسیار کاربردی است.

هنگامی که یک شناساننده یا یک اکسپرشن نقطه ای بعد سمیکالم داریم، اسم متغیر ورودی کلیدی توسط شناساننده تعیین میشود یا توسط اسم فیلد.
برای مثال
`plot(x, y; width)` معادل است با
`plot(x, y; width=width)` و `plot(x, y; options.width)` معادل است با `plot(x, y; width=options.width)`.

طبیعت کلمات کلیدی این امکان را به ما میدهد که بتوانیم چندین ورودی را همزمان داشته باشیم برای مثال در فراخوانی
 `plot(x, y; options..., width=2)`
 میشود که ساختار
 `options` 
 همچنین یک مقدار برای 
 `width`نگه دارد. 
 در این مواقع سما راستی ترین دستور تقدم را خواهد داشت.

در این مثال, `width` 
مقدار
`2`را میگیرد. 
اگر چه نمیتوان یک ورودی کلیدی را چندین بار مشخض کرد برای مثال
 `plot(x, y, width=2, width=3)`, 
مجاز نیست و خطای سینتکس دارد.

## ارزیابی دامنه مقادیر پیشفرض

هنگامی اکسپرشن های پیشفرض یک متغیر اختیاری و کلیدی تایین میشوند فقط ورودی پیشین در دامنه است.
برای مثال این تعریف داده شده اسن :

```julia
function f(x, a=b, b=1)
    ###
end
```

 `b` در `a=b` ارجاع دارد به یک `b` در دامنه بیرونی, نه ورودی درونی `b`.

## سینتکس Do-Block برای ورودی توابع

دادن توابع به عنوان ورودی به یک تابع دیگر یک تکنیک قوی است اما سینتکس آن همیشه ساده نیست. هنگامی که توابع موردنظر از چندین خط نوشته شده اند نوشتن چنین تکنیکی بسیار بدشکل است.
برای مثال صدا زدن 
 `map`
 را در یک تابع با چند case در نظر بگیرید :


```julia
map(x->begin
           if x < 0 && iseven(x)
               return 0
           elseif x == 0
               return 1
           else
               return x
           end
       end,
    [A, B, C])
```

جولیا یک کلمه رزرو شده
 `do` 
 را برای بازنویسی این کدها به صورت تمیز تر در نظر گرفته است :
 

```julia
map([A, B, C]) do x
    if x < 0 && iseven(x)
        return 0
    elseif x == 0
        return 1
    else
        return x
    end
end
```

 `do x` یک تابع ناشناس با ورودی `x` میسازد و آن را به عنوان ورودی اول به `map` میدهد.
 مشابها, `do a,b` یک تابع دو ورودی ناشناس میسازد, 
 و
 `do` 
 شکل تابع ناشناس را از شکل قبلی خود متمایز میسازد :
`() -> ...`.

اینکه این ورودی ها چگونه ساخته میشوند به توابع بیرونی مربوط است;در اینجا, `map`
مجموعه `x` به `A`, `B`, `C`, برای هر کدام یک تابع ناشناس میاورد, همانطور که در سینتکس  `map(func, [A, B, C])`رخ میداد.

این سینتکس استفاده از تابع را و گسترش زبان را ساده تر میکند تا وفتی که فراخوانی ها شبیه کدهای نرمال و عادی باشند.
استفاده های متنوعی برای
 `map`, 
 وجود دارد
 برای مثال مدریت وضعیت سیستم
برای مثاب در اینجا ورژنی از
`open` 
که با اجرا شدنش فایل های باز شده فورا بسته میشوند.

```julia
open("outfile", "w") do io
    write(io, data)
end
```

This is accomplished by the following definition:

```julia
function open(f::Function, args...)
    io = open(args...)
    try
        f(io)
    finally
        close(io)
    end
end
```

در اینجا, 
`open`
ابتدا فایل ها را برای نوشتن باز میکند بعد نتیجه را به یک تابع ناشناس ارجاع میدهد که شمت تعریف کردید در
 `do ... end`بلاک.
 بعد از وجود تابع, `open`
مطمین میشود که استریم بسته شده است بدون توجه به اینکه در حالت عادی تابع شما وجود دارد یا اینکه اروری پرت شده باشد ( ساختار`try/finally` در کنترل فلو توضیح داده خواهد شد)

بوسیله `do` بلاک سینتکس, کمک میکند به چک کردن اسناد

یا پیاده سازی برای فهمیدن چگونگی ورودی های  تابع کاربر اینتیالایز شده اند.

یک بلاک `do`,
مانند هر تابع درونی دیگری میتواند مقادیر را دامنه اش بگیرد برای مثال مقدار
 `data` در مثال بالا
`open...do` از دامنه بیرونی گرفته شده است.گرفتن متغیرها میتواند باعث بوجود آمدن کارایی شود. [performance tips](@ref man-performance-captured).

## ترکیب و اتصال توابع

توابع در جولیا میتوانند ترکیب یا متصل شوند.

ترکیب وفتی است که نتیحع یک تابع را به عنوان ورودی به تایع دیگیری میدهید.
برای اینکار از عملگر ترکیب توابع
 (`∘`)
 استفاده میکنید پس
 `(f ∘ g)(args...)`همان`f(g(args...))`است.

میتوانید عملگر ترکیب را در REPL یا در
suitably-configured editors بوسیله `\circ<tab>`تایپ کنید.

برای مثال `sqrt` و `+` میتوانند ترکیب شوند:

```julia
julia> (sqrt ∘ +)(3, 6)
3.0
```

این اول اعداد را جمع میکند و سپس از آنها جذر میگیرد..

مثال بعدی سه تابع را ترکیب و جواب را در یک مپ آرایه رشته خروجی میدهد.:

```julia
julia> map(first ∘ reverse ∘ uppercase, split("you can compose functions like this"))
6-element Vector{Char}:
 'U': ASCII/Unicode U+0055 (category Lu: Letter, uppercase)
 'N': ASCII/Unicode U+004E (category Lu: Letter, uppercase)
 'E': ASCII/Unicode U+0045 (category Lu: Letter, uppercase)
 'S': ASCII/Unicode U+0053 (category Lu: Letter, uppercase)
 'E': ASCII/Unicode U+0045 (category Lu: Letter, uppercase)
 'S': ASCII/Unicode U+0053 (category Lu: Letter, uppercase)
```

زنجیر کردن توابع )گاهی اوقات لوله کردن یا متصل کردن نیست گفته میشود) وقتی است که میخواهید یک تابع را روی خروجی تابع قبلی اعمال کنید


```julia
julia> 1:10 |> sum |> sqrt
7.416198487095663
```

در اینجا مجموع یا همان
 `sum` به تابع `sqrt` پاس داده میشود. ترکیب معادل آن به این شکل است:

```julia
julia> (sqrt ∘ sum)(1:10)
7.416198487095663
```

عملگر لوله میتواند برای برادکستتینگ(؟) نیز استفاده شود از طریق
 `.|>`, برای تهیه ترکیبی کار آمد از زنجیرها و سینتکس برداری دات

```julia
julia> ["a", "list", "of", "strings"] .|> [uppercase, reverse, titlecase, length]
4-element Vector{Any}:
  "A"
  "tsil"
  "Of"
 7
```

## سینتکس دات برای توابع برداری کننده

در زبان محاسبه تکنیکال بسیار معمول است که از یک تابع ورژن برداری شده ان را داشته باشیم که بتواند روی تابع داده شده
 `f(x)` روی هر عضو آرایه `A` برای ساخت آرایه جدید
`f(A)`. این سینتکس برای بررسی داده ها بسیار خوب است, اما در بقیه زبان ها برداری کردن برای نمایش نیر بکار میرود: 
اگر حلقه ها کند هستند, ورژن برداری یکی تابه میتواند یک کتابخانه سریع در زبان سطح پایین نامیده شود.
در جولیا توابع برداری برای نمایش نیاز نیستند و قطعا گاهی سودمند است که شما خودتان یک حلقه طراحی کنید
 (ببینید
[Performance Tips](@ref man-performance-tips)), 
اما آن هم همچنان میتواند خوبی باشد.
همچنین هر تابعی در جولیا مانند

`f` 
میتواند به صورت تکی روی هر المنت از یک آرایه اعمال شود بوسیله دستور
 `f.(A)`.
برای مثال, `sin` میتواند روی همه اعضای بردار `A` اعمال شود:

```julia
julia> A = [1.0, 2.0, 3.0]
3-element Vector{Float64}:
 1.0
 2.0
 3.0

julia> sin.(A)
3-element Vector{Float64}:
 0.8414709848078965
 0.9092974268256817
 0.1411200080598672
```

البته شما میتوانید دات را حذف کنید اگر ساختار خاص برداری تابع
 `f`را نوشته باشید, برای مثال `f(A::AbstractArray) = map(f, A)`, 
و اینکار دقیقا مانند`f.(A)`کاربرد دارد. 
خوبی استفاده از دستور
`f.(A)` 
این است که هر تابعی که برداری شونده باشد نیازی ندارد که تصمیم گیری شود بوسیله کتابخانه نویسنده.

در حالت کلی
, `f.(args...)`معادل است با `broadcast(f, args...)`, 
که مارا مجاز میسازد تا آرایه های مختلفی را حتی به اشکال مختلف یا حتی ترکیبی از آرایه ها و اسکالر ها را اپریت کنیم
.
برای مثال اگر شما `f(x,y) = 3x + 4y`را داشته باشید,
آنگاه `f.(pi,A)` آرایه ای شامل `f(pi,a)` به ازای هر `a` در `A`را برمیگرداند,
و `f.(vector1,vector2)` 
یک بردار شامل `f(vector1[i],vector2[i])` برای هر عدد `i`
را برمیگرداند(ارور پرتاب میشود اگر سایزشان یکی نبود).

```julia
julia> f(x,y) = 3x + 4y;

julia> A = [1.0, 2.0, 3.0];

julia> B = [4.0, 5.0, 6.0];

julia> f.(pi, A)
3-element Vector{Float64}:
 13.42477796076938
 17.42477796076938
 21.42477796076938

julia> f.(A, B)
3-element Vector{Float64}:
 19.0
 26.0
 33.0
```

همچنین, *nested* `f.(args...)`  *fused*نامیده میشوند  درون یک حلقه `broadcast`.برای مثال,
`sin.(cos.(X))`
معادل است با
`broadcast(x -> sin(cos(x)), X)`,
مشابه با `[sin(cos(x)) for x in X]`:
یک در اینجا فقط یک حلقه تکی `X`,
و یک آرایه تکی برای نتیجه است. 
[در ساختار,
`sin(cos(X))`
در یک زبان برداری معمول اول باید یک آرایه ضروری
`tmp=cos(X)`را بسازیم
, و بعد `sin(tmp)` را در آرایه دوم حساب کنیم] 
این حلقه یک اپتیمیزیشن برای کامپایلر نیست که شاید بتوان و شاید نتوان بدستش آورد, بلکه آن یک
*syntactic guarantee*
هروقت تابع تو در تو
 `f.(args...)` تماس برقرار می شود تکنیکالی ، همجوشی به محض متوقف می شود
با یک تماس عملکرد "غیر دات" مواجه می شود; 
برای مثال در تابع `sin.(sort(cos.(X)))`، `sin`و `cos`
نمیتوانند ادغام شوند بدلیل مداخله تابع  `sort` .

در نهایت بیشترین بازدهی وقتی است که آرایه خروجی یک تابع برداری شده *pre-allocated* باشد,
تا فراخوانی های جدید آرایه های جدیدی را نیاز نباشد که اختیار کنند برای نگه داری نتیجه ها و خروجی ها
(ببینید Pre-allocating outputs). 
یک دستور راحت برای اینکار `X .= ...`است, 
که معادل است با`broadcast!(identity, X, ...)` به جز این, مانند بالا, حلقه `broadcast!` هر فراخوانی تودرتو را به هم میجوشاند.

برای مثال, `X .= sin.(Y)` معادل است با `broadcast!(sin, X, Y)`,
بازنویسی `X`با  `sin.(Y)` . اگر سمت چپ یک اکسپرشن آرایه باشد
e.g. `X[begin+1:end] .= sin.(Y)`, آنگاه ترجمه میشود بع=ه `broadcast!` در `view`, e.g.
`broadcast!(sin, view(X, firstindex(X)+1:lastindex(X)), Y)`,
پس سمت چپ همانجا آپدیت میشود.

از آنجا که اضافه کردن دات به خیلی از عملگرها و توابع در اکسپرشن میتواند ما را به یک کد که خواندنش سخت است ببرد پس
[`@.`](@ref @__dot__) تهیه شده است برای تبدیل فراخوان هر تابعی و هر نسبت دهی در یک اکسپرشن به یک ورژن نقطه دار شده!,


```
julia> Y = [1.0, 2.0, 3.0, 4.0];

julia> X = similar(Y); # pre-allocate output array

julia> @. X = sin(cos(Y)) # equivalent to X .= sin.(cos.(Y))
4-element Vector{Float64}:
  0.5143952585235492
 -0.4042391538522658
 -0.8360218615377305
 -0.6080830096407656
```

عملگرهای دودویی مانند
 `.+` با همان مکانیسم قبلی مدیریت میشوند:
آنها معادلند با`broadcast`و با بقیه دات های تودرتوی دیگر همجوشی میکنند.
 `X .+= Y` معادل است با `X .= X .+ Y` و نتیجه همان جا نسبت دهی میشود
 همچنین اینجا را ببینید [dot operators](@ref man-dot-operators).

همچنین میتوان عملگراهای دات را با هم ترکیب کرد بوسیله `|>`, مانند مثال زیر:
```julia
julia> [1:5;] .|> [x->x^2, inv, x->2*x, -, isodd]
5-element Vector{Real}:
    1
    0.5
    6
   -4
 true
```

## Further Reading

در اینجا باید یادآور شویم که مطالبی که گفته شد بسیار از تصویر کامل تعریف توابع دور است.
جولیا سیستم پیچیده ای دارد و اجازه میدهد که انواع مختلفی از ورودی ها را ارسال کنید.
هیچکدام از مثالهایی که زده شد و حاشیه نویسی هایی که گفته شد درباره ورودی ها نشان دهنده این نیستند که آنکار را میتوان با همه ی انواع ورودی ها انجام داد.
نوع سیستم در
[Types](@ref man-types)
شرح داده شده
و تعریف توابع به منظور متد ها انتخاب میشوند بوسیله انواع ورودی ها که در ران تایم و متد ها ارسال میشوند

