# Style Guide(راهنمای سبک نوشتن)
<div dir="auto">
 در این بخش ها ما به بررسی بخش از عبارات اصطلاحی در نوشتن برنامه به زبان جولیا می پردازیم
    .
    هیچ کدام از این قوانین مطلق نیستند
    .
    انها تنها برای کمک  به اشنا شدن شما با زبان و کمک برای انتخاب بین راه حل های طراحی موجود است
    .
 </div>

## Indentation
<div dir="auto">
 برای سطح های مختلف 
 indentation
 از 4 تا اسپیس می توانید استفاده کنید
 .
 </div>

## Write functions, not just scripts(نوشتن توابع)
<div dir="auto">
 یکی از راه های سریع حل یک مسئله، نوشتن کد به صورت یک سری گام در بالاترین سطح است ولی بهتر است هر چه سریعتر برنامه خود را به یکسری تابع تقسیم کنید
 .
 توابع بهتر از حالت قبل قابل استفاده مجدد و تست شدن هستند
 ,
 واینکه چه گام هایی  تمام شده اند و چه ورودی ها و خروجی هایی داشتند  را مشخص میکنند 
 .
 در ادامه در زبان جولیا کد های داخل توابع 
 ،
 سریعتر از کد های سطح اول اجرا می شوند
 .
 که این به عملکرد کامپایلر جولیا بازمی گردد
 .
 </div>
 <div dir="auto">
 همین طور تاکیید این نکته مهم است که توابع بهتر است 
 ورودی بگیرند به جای اینکه  به صورت 
 global 
 از متغیر ها استفاده کنند
 .
 )
 جدای از ثابت ها مانند
 
 </div>
 
  [`pi`](@ref))

## Avoid writing overly-specific types(جلوگیری از نوشتن تایپ های بیش از حد مشخص)

<div dir="auto">
کد باید تا جایی که میتوانیم
 generic
 عمومی باشد
 .
 به جای رویه زیر
 </div>

```julia
Complex{Float64}(x)
```
<div dir="auto">
 بهتر است که از توابع در دسترس عمومی
 )
 اصطلاحا
 genenric
 استفاده شود
 .
 </div>

```julia
complex(float(x))
```
<div dir="auto">
 رویه دوم 
 `x`
 را به تایپ مورد نظر تبدیل میکند 
 .
 به جای اینکه همیشه به یک نوع تبدیل کند
 .
  </div>
  <div dir="auto">
 این طرز نوشتن مخصوصا مربوط به ورودی های تابع است
 .
 برای مثال ما نوع ارگومان را
 `Int` 
 یا
 <span><a href="https://docs.julialang.org/en/v1/)/">Int32</a></span>
 
 در واقع  ،در بیشتر اوقات تایپ یا نوع ورودی را میتوانید اعلام نکنید مگر اینکه برای ابهام زدایی از سایر توابع لازم باشداگر نوعی که به ارگومان داده میشودتوسط هیچ یک از  
اسم گذاری های تابع معتبر نباشد،یک ارور
 <span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">MethodError</a></span>
 به ما داده میشود
 .
 به این مسئله
 <span><a href="https://en.wikipedia.org/wiki/Duck_typing">duck typing</a></span>
 می گویند
 .
 </div>
<div dir="auto">
 برای مثال تعریف زیر را برای تابع 
 addone
 در نظر بگیرید که عدد یک را با ارگومان ورودیش جمع می کند
 :
</div>


```julia
addone(x::Int) = x + 1                 # works only for Int
addone(x::Integer) = x + oneunit(x)    # any integer type
addone(x::Number) = x + oneunit(x)     # any numeric type
addone(x) = x + oneunit(x)             # any type supporting + and oneunit
```
<div dir="auto">
 اخرین تعریف 
 addone
 برای تمام ارگومان ها از نوع
 <span><a href="https://docs.julialang.org/en/v1/)/">oneunit</a></span>
 )
 که یکی را برمی گرداند که هم نوع 
 'x'
 باشد که از تایپی که مناسب نباشد و در نتیجه به ارور برخورد کردن خودداری میکند
 وتابع
 <span><a href="https://docs.julialang.org/en/v1/)/">'+'</a></span>
 با ان ارگومان اجرا میشود
 .
 نکته مهم این است که ما هیچ مشکلی در اجرا و عملکرد
 `addone(x) = x + oneunit(x)`
 نسبت به حالتی که تایپ را بنویسیم نداریم 
 )
 به طور اصطلاح 
we dont have  performance penalty
 (
 چون جولیا به طور اتوماتیک تابع 
 `addone`
 را به ازای ارگومان 
 `x::Int`
 صدا میزند
 و
 `oneunit`
 با 
 1
 جایگزین می شود
 .
 بنابراین هر سه تعریف اول از تابع 
 `addone`
 به طور کامل به تعریف چهارم کاهش می یابند
 .
</div>

## Handle excess argument diversity in the caller(تعدد ارگومان در تابع)

<div dir="auto">
 به جای 
 :
 </div>


```julia
function foo(x, y)
    x = Int(x); y = Int(y)
    ...
end
foo(x, y)
```
<div dir="auto">
 از این کد استفاده کنید
 :
 </div>

```julia
function foo(x::Int, y::Int)
    ...
end
foo(Int(x), Int(y))
```
<div dir="auto">
 این سبک نوشتن بهتر است زیرا دیگر ارگومان هایی از نوع دیگر را قبول نمیکند و باعث اشتباه کمتر برنامه نویس می شود
 .
 وفقط نوع 
 `Int`
 را قبول میکند
 .
 </div>
<div dir="auto">
 یک مسئله اینجا این است که این تابع به صورت ذاتی فقط 
 integer
 قبول میکند 
 .
 شاید بهتر بود تابع تضمیم بگیرد که با
 non-integers
 چگونه تصمیم بگیرد
 (مانند کف یا سقف گرفتن عمل کند)
 مسئله دیگر این است که این دقیق مشخص کردن نوع ورودی ارگومان توابع ابتکار عمل را برای تعاریف بدی تابع می گیرد
 .
 </div>

## [Append `!` to names of functions that modify their arguments]
<div dir="auto">
 به جای 
 :
 </div>

```julia
function double(a::AbstractArray{<:Number})
    for i = firstindex(a):lastindex(a)
        a[i] *= 2
    end
    return a
end
```
<div dir="auto">
 از این کد استفاده کنید
 :
 </div>

```julia
function double!(a::AbstractArray{<:Number})
    for i = firstindex(a):lastindex(a)
        a[i] *= 2
    end
    return a
end
```
<div dir="auto">
همچنین
 Julia Base
 از این روش مرسوم استفاده میکند و توابعی با هر دو صورت کپی شده و اصلاح شده استفاده میکند 
 .
 </div>
 
 (e.g., [`sort`](@ref) and [`sort!`](@ref))
 <div dir="auto">
و بقیه فقط فزم اصلاح شده اند
 .
 و این برای چنین توابعی عادی است که ارایه اصلاح شده را برگردانند
 .
 </div>
 
 (e.g., [`push!`](@ref), [`pop!`](@ref), [`splice!`](@ref))
 

## Avoid strange type `Union`s(از نوع های عجیب "اجتماع " دوری کنید)
 <div dir="auto">
 تایپ ها یا نوع هایی مثل
 `Union{Function,AbstractString}`
 نشانه هایی هستند که طراحی شما میتوانست بهتر و تمیز تر باشد
 .
 </div>

## Avoid elaborate container types(از نوع های در برگیرنده پیچیده بپرهیزید)
 <div dir="auto">
 معمولا ساخت ارایه به صورت زیر کمک شایانی نمی کند
 :
  </div>

```julia
a = Vector{Union{Int,AbstractString,Tuple,Array}}(undef, n)
```
<div dir="auto">
 در این حالت
 `Vector{Any}(undef, n)`
 بهتر است .
 و همچنین برای کامپایلر کمک کننده تراست که یک حاشیه نویسی هایی به صورت زیر انجام دهد که
 بسیاری از نوع های جایگزین را به یک نوع تبدیل کند
 .
  </div>
  
  (e.g. `a[i]::Int`)
  
## Use naming conventions consistent with Julia `base/`
<div dir="auto">
 *
 ماژول ها و نوع متغیر ها از حروف بزرگ تشکیل شده اند و در اصطلاح 
 camel case
 هستند برای مثال 
 `module SparseArrays`
 و
 `struct UnitRange`
 <br>
 *
 توابع با حروف کوچک یا به اصطلاح 
 lowercase
 هستند
 .
 مانند
 <span><a href="https://docs.julialang.org/en/v1/)/">`maximum`</a></span>
 و
 <span><a href="https://docs.julialang.org/en/v1/)/">`convert`</a></span>
 و وقتی از چند کلمه تشکیل شده باشد
 مانند
 <span><a href="https://docs.julialang.org/en/v1/)/">`isequal`</a></span>
 و
 <span><a href="https://docs.julialang.org/en/v1/)/">`haskey`</a></span>
 .
 اگر لازم بود 
 از 
 underscore
 به عنوان کلمه جدا کن استفاده منید
 .
  underscores
 معمولا برای نشان دادن ترکیب چند موضوع استفاده می شوند
 مثلا
 <span><a href="https://docs.julialang.org/en/v1/)/">`remotecall_fetch`</a></span>
 ورژن با عملکرد بهتر برای تابع
  <span><a href="https://docs.julialang.org/en/v1/)/">`fetch(remotecall(...))`</a></span>
 است
 .
 <br>
 *
 مختصر نوشتن خیلی خوب است ولی از خلاصه نویسی بپرهیزید برای مثال 
 <span><a href="https://docs.julialang.org/en/v1/)/">`indexin`</a></span>
 بهتر است از
 <span><a href="https://docs.julialang.org/en/v1/)/">`indxin`</a></span>
 چون باعث میشود سخت تر به یاد بیاوریم که هر لغت مختصر شده کدام کلمه است 
 .
 
</div>

<div dir="auto">
 اگربرای اسم یک تابع به چند کلمه نیاز داشتید ،این موضوع را که به چند تا موضوع مربوط است و بهتر است انها را به چند تا تابع تقسیم کنید
 .
  </div>

## Write functions with argument ordering similar to Julia Base
<div dir="auto">
 به عنوان قانونی کلی کتابخانه 
 BASE
 از ترتیب زیر برای ارگومان های تابع استفاده میکند.
 :
  </div>
1. **ارگومان تابع**.
<div dir="auto">
اضافه کردن ارگومان تابع به ما اجازه میدهد که از بلاک 
 do 
 استفاده کنیم .
 برای اجرا کردن توابع چندخطی 
 anonymous
  .
  </div>

2. **I/O stream**.
 <div dir="auto">
مشخص کردن اینکه اشیا
 `IO`
 اجازه میدهند تابع به توابع دیگر مثل 
 `sprint(show, x)`
 پاس داده شود
 .
  </div>
  
  [`sprint`](@ref)
  
3. **Input being mutated**.
 <div dir="auto">
برای مثال در تابع
 <span><a href="https://docs.julialang.org/en/v1/)/"></a>`fill!(x, v)`</span>
 متغیر
 `x`
 شئ است که جهش یافته 
 (mutated)
 و قبل از مقدار قرار داده شده در 
 `x`
 ظاهر میشود
 .
  </div>

4. **Type**.
 <div dir="auto">
پاس دادن یک تایپ (یا نوع)به طور عادی به این معنی است که خروجی ما به طور عادی به صورت همان نوع داده شده است .
 در تابع 
 <span><a href="https://docs.julialang.org/en/v1/)/"></a>`parse(Int, "1")`</span>
 تایپ مورد نظر قبل از رشته ای که باید 
 parse
 شود می اید
 .
 در بسیاری از مثال ها که نوع خروجی اول می اید ولی مفید است که ذکر کنیم که
 ارگومان 
 `IO`
 در تابع
 <span><a href="https://docs.julialang.org/en/v1/)/">`read(io, String)`</a></span>
 قبل از تایپ (نوع) ظاهر می شود که مطابق ترتیب ذکر شده در اینجاست
 .
  </div>

5. **Input not being mutated**.
 <div dir="auto">
در تابع 
 <span><a href="https://docs.julialang.org/en/v1/)/">`fill!(x, v)`</a></span>
 متغیر 
 `v`
 تغییر نکرده و بعد از 
 `x`
 می اید
 .
  </div>

6. **Key**.
 <div dir="auto">
برای مجموعه های انجمنی به اصطلاح
 (associative)
 این کلید زوج های کلید-مقدار 
 s
 است و برای سایر مجموعه ها بیان کننده شماره 
 (index)
 است.
  </div>

7. **Value**.
<div dir="auto">
برای مجموعه های انجمنی به اصطلاح
 (associative)
 این مقدار زوج های کلید-مقدار 
 s
 است و
 در حالاتی مثل 
 <span><a href="https://docs.julialang.org/en/v1/)/">`fill!(x, v)`</a></span>
 همان 
 'v'
 است
 .
  </div>
  
8. **Everything else**.
<div dir="auto">
هر ارگومان دیگری 
  </div>

9. **Varargs**.

<div dir="auto">
این اصطلاح به ارگومان هایی اشاره می کند که در انتهای فرایند صدا شدن 
 (call)
  تابع
  قابل لیست شدن باشند
 . 
 به عنوان مثال در 
 `Matrix{T}(undef, dims)`
 ابعاد ماتریس میتوانند به عنوان یک 
 <span><a href="https://docs.julialang.org/en/v1/)/">`Tuple`</a></span>
 یعنی 
 `Matrix{T}(undef, (1,2))
 یا به عنوان 
 `Vararg`](@ref)s,
   e.g. `Matrix{T}(undef, 1, 2)`.
</div>

10. **Keyword arguments**.
<div dir="auto">
 در جولیا ارگومان های کیبورد باید در هر صورت در اخر تعریف توابع بیایند
 .
 انها برای کامل شدن مطلب ابنجا ذکر شده اند
 .
 <br>
 اکثر قریب به اتفاق توابع از هر نوع ارگومان ذکر شده در بالا استفاده نمی کنند
 .
 اعداد صرفاً تقدم را نشان می دهند که باید برای هر ارگومان  قابل استفاده یک تابع استفاده شود
 <br>
 بدیهی است مقدار کمی استثنا وجود دارد.
  <br>
 برای مثال در تابع 
 <span><a href="https://docs.julialang.org/en/v1/)/">`convert`</a></span>
 تایپ (یا نوع )خروجی همیشه بایداول بیاید
 .
 در تابع 
 <span><a href="https://docs.julialang.org/en/v1/)/">`setindex!`</a></span>
 مقدار قبل از اندیس ها می اید پس می توانند به عنوان 
 varargs
 در نظر گرفته شوند
 .
 <br>
 درهنگام طراحی 
 API
 ها
 ،
 پایبندی به این دستور کلی تا آنجا که ممکن است احتمالاً به کاربران شما تجربه سازگارتری میدهد
 .
 </div>

## Don't overuse try-catch(بیش از اندازه استفاده نکنید)
<div dir="auto">
 بهتر است از گرفتن خطا اجتناب کنید تا اینکه انهارا 
 catch 
 کنید
 .
</div>

## Don't parenthesize conditions(شرط ها را با پرانتز ننویسید)
<div dir="auto">
 جولیا نیازی به پرانتز برای نوشتن شرط ها در 
 if
 و 
 while
 نیازی ندارد
 .
 بنویسید
 :
</div>

```julia
if a == b
```
<div dir="auto">
 به جای 
 :
</div>


```julia
if (a == b)
```

## Don't overuse `...`(دوباره استفاده نکنید)
<div dir="auto">
 اتصال
 ارگومان توابع یا به اصطلاح 
 (splicing)
 میتواند کاری خطرناک باشد
 .
 به جای
 [a..., b...]
 به سادگی از 
 [a; b]
 استفاده کنید که خودش عمل 
 concatation
 را برای ارایه انجام میدهد.
 collect(a)
 بهتر از 
 [a...]
 است ولی زمانی که 
 a
  خودش قابل تکرار است ،معمولا بهتر است که انها را خودش انجام دهیم و به ارایه تبدیل نکنیم 
 .
 
 
 
</div>

## Don't use unnecessary static parameters(از پارامترهای غیر ضروری استاتیک استفاده نکنید)
<div dir="auto">
 امضای تابع
 :
</div>

```julia
foo(x::T) where {T<:Real} = ...
```
<div dir="auto">
 بهتر است بدین صورت نوشته شود
 :
</div>

```julia
foo(x::Real) = ...
```
<div dir="auto">
 در مقابل ،مخصوصا اگر 
 T
 در بدنه تابع استفاده نشده باشد، حتی اگر استفاده شدهباشد در صورت راحتی میتواند با 
  <span><a href="https://docs.julialang.org/en/v1/)/">typeof(x)</a></span>
 جایگزین شود 
و از لحاظ عملکردی فرقی ندارند.
 توجه داشته باشید که این یک احتیاط کلی و عمومی در برابر پارامترهای استاتیک نیست بلکه فقط درمقابل مواقعی است که استفاده از انها لازم نباشد.
 <br>
توجه داشته باشید که تایپ های دربرگیرنده 
 container types
 به طور ویژهاحتیاج به تایپ پارامتر در صدا زدن تابع دارند .
 برای اطلاعات بیشتر به 
 <span><a href="https://docs.julialang.org/en/v1/)/">Avoid fields with abstract containers</a></span>
 مراجعه کنید.

</div>

## Avoid confusion about whether something is an instance or a type(سردرگمی  تایپ و نمونه )
<div dir="auto">
 مجموعه ای از تعاریف مانند تعاریف زیر گیج کننده هستند 
 :
</div>

```julia
foo(::Type{MyType}) = ...
foo(::MyType) = foo(MyType)
```
<div dir="auto">
 تصمیم بگیرید که ایا مفهوم و موضوع مورد سوال به صورت 
 MyType
 یا
 MyType()
 قابل نوشت است و از ان پیروی کنید.
 <br>
 استایل و طراحی موردنظر ما استفاده پیش فرض از نمونه ها واگر واقعا اضافه کردن انها برای حل مسئله لازم بود ،بعدا فقط افزودن متد هایی که شامل   
 Type{MyType}
 می شوند
 .
  <br>
 اگر تایپ به صورت موثر یک شکارشگر 
 enum
 باشد بهتر است به صورت تایپ تکی 
 (single type )
 تعریف شود.
 البته در حالت ایده ال) 
 بهتر است 
 immutable 
 (باشد
با مقدار شمارش شده 
 (enumration value)
 یک نمونه یا شی از ان باشد کانستراکتور ها و کانورژن ها می توانند چک کنند که لیل مقادیر قابل اطمینان هستندد یا خیر .
 این طراحی نسبت به حالتی که 
 enum
 هارا یک تایپ ابسترکت تعریف کرده و "مقادیر" را به صورت زیر نوع تعریف کنیم ، بهتر است .
 


</div>

## Don't overuse macros(زیاد از ماکرو استفاده نکنید)
<div dir="auto">
 باید بدانیم که چه موقع یک ماکرو میتواند یک تابع به حساب اید .
 <br>
 صدا کردن 
 <span><a href="https://docs.julialang.org/en/v1/)/">eval</a></span>
 داخل یک ماکرو به طور خاص یک علامت خطرنام است .
 به این معناست که ماکرو فقط در حالتی کار میکند که در بالاترین سطخ 
 (top level)
 صدا شده باشد.
 اگر این ماکرو به صورت تابع نوشته شده باشد،به طور طبیعی به مقادیر
 run-time
 ای که نیاز دارد دسترسی دارد
 .
</div>


## Don't expose unsafe operations at the interface level(در سطح رابط از عمل خطرناک استفاده نکنیذ)
<div dir="auto">
 اگر شما یک تایپ دارید که از یک اشاره گر بومی استفاده می کند.
 (native pointer):
</div>

```julia
mutable struct NativeType
    p::Ptr{UInt8}
    ...
end
```
<div dir="auto">
 تعاریف را مانند مثال زیر ننویسید
 :
</div>

```julia
getindex(x::NativeType, i) = unsafe_load(x.p, i)
```
<div dir="auto">
 مشکل اینجاست که کاربر این تایپ می تواند بنویسد 
 x[i]
 بدون اینکه بفهمد این عملیات امن نیست.
و ممکن است دچار باگ های حافظه شود. 
 <br>
 چنین تابعی باید عملیات هایش چک شود تا تضمین شود که امن بوده است و یا یکجایی در اسم خود کلمه 
 unsafe
 داشته باشد که صدازننده این تابع ،این موضوع را بداند.
 
</div>

## Don't overload methods of base container types
<div dir="auto">
 ممکن است تعاریفی مانند زیر بنویسیم 
 :
</div>

```julia
show(io::IO, v::Vector{MyType}) = ...
```
<div dir="auto">
 این ممکن است یک نمایشی از بردار ها بدهد که نوع اعضای ان یک نوع جدید باشد.
 در حالیکه این کار وسوسه کننده است باید از ان خودداری شود.
 مشکل این اسن که کاربر انتظار نوع مشهوری مثل 
 Vector()
 را دارد  پس به روش بالا عمل کردن و زیاد شخصی سازی توابع  کار با ان ها را سخت تر می کند.
</div>


## Avoid type piracy(از سطح امنیت دادن به تایپ ها بپرهیزید)
<div dir="auto">
 کلمه 
 "Type piracy"
 یا سطح امنیت تایپ 
 به عمل گسترش یا تعریف مجدد روش ها در 
 Base
 اشاره دارد.
 در بعضی از حالات ،با کمی تاثیر بد  میتوانید با ان کنار بیایید ولی در حالات خیلی اکستریم، حتی میتواند منجر به  خراب شدن جولیا شود 
اگر تعریف مجدد یا اکستنشن تابع شما منجر به تولید ورودی غیر قابل قبولی که به تابع مثلا) 
 <span><a href="https://docs.julialang.org/en/v1/)/">ccall</a></span>
 شود.
 (
 Type piracy
 میتواند منجر به پیچیده شدن کد و ایجاد مشکلاتی که به سختی قابل پیش بینی وتشخیص باشند ، شود.
 <br>
 به عنوان مثال ،فرض کنید میخواهی د عمل ضرب را بر روی سمبل ها در یک ماژول تعریف کنید 
 :
 
</div>

```julia
module A
import Base.*
*(x::Symbol, y::Symbol) = Symbol(x,y)
end
```
<div dir="auto">
 مشکل این است که الان هر ماژول دیگری که از 
 Base
 استفاده میکند هم این تعریف یا تابع را میبیند و تا زمانی که 
 Symbol
 در 
 Base
 تعریف شده باشد،توسط دیگر ماژول ها استفاده میشود.
 و این میتواند رفتار و عملکرد کد های در ضاهر بی ربط به ان را تغییر دهد و یا مختل کند .
 یکسری راه های دیگر نیز برای این حالت وجود دارد، مانند در نظر گرفتن یک اسم دیگر برای تابع
 و یا دسته بندی 
 (wrapping)
  نماد 
 Symbol
 در نوع دیگری که تعریف کردید.
 <br>
 بعضی اوقات  پکیج های 
 coupled
 ممکن است به نوعی از 
 type piracy
 برای جداکردن ویژگی هایی از تعاریف استفاده کرده باشند مخصوصا وقتی که ان بکیج ها توسط تعدادی برنامه نویس طراحی شده باشند.
 که با هم همکاری داشتند وقتی که تعاریف و یا تابع ها قابل استفاده مجدد هستند .
 برای مثال فرض کنید یک پکیج یکسری تایپ  برای کار با رنگ ها فراهم میکند و پکیج دیگر میتواند یکسری تابع برای ان تایپ های که اجازه تبدیل بین فضا های رنگ را دارد ، تعریف کند 
 مثال دیگر ممکن است این باشد که یک پکیجیک 
 wrapper
 برای کد ها به زبان 
 c 
 باشد درحالیکه دیگری باید یک 
 API
 سطح بالا به زبان جولیا پیاده سازی مند.

</div>

## Be careful with type equality(در برابر بودن تایپ دقت کنید)
<div dir="auto">
 ما به صورت کلی دوست داریم که از 
 <span><a href="https://docs.julialang.org/en/v1/)/">isa</a></span>
 و
 <span><a href="https://docs.julialang.org/en/v1/)/"><:</a></span>
 برای چک کردن نوع ارگومان استفاده کنیم .
 و نه 
  <span><a href="https://docs.julialang.org/en/v1/)/">==</a></span>
  چک کردن نوع به صورت  تساوی دقیق با نماد قبل فقط موقعی خوب و منطقی به نظر میرسد که که با یک 
  concrete
  تایپ داده شده مقایسه شود.
  مثل
  `T == Float64`
  ویا حتما حتما حتما بدانید که چگونه از ان استفاده می کنید.
  
  
</div>


## Do not write `x->f(x)`(این را ننویسید)
 
<div dir="auto">
 تا جایی که توابع با ترتیب بالاتر در اجرا معمولا به وسیله توابع ناشناس 
 anonymous
 ساخته شده اند ،راحت است دریابیم که این خوب یا حتی لازم است .
 ولی هر تابعی میتواند به صورت مستقیم پاس داده شود ،بدون اینکه در توابع ناشناس
 (anonymous)
  به اصطلاح 
 (wrapped)
 شوند .
 <br>
 به جای نوشتن 
 map(x->f(x), a)
 بنویسید 
 <span><a href="https://docs.julialang.org/en/v1/)/">map(f, a)</a></span>
 .
 
</div>

## Avoid using floats for numeric literals in generic code when possible
<div dir="auto">
 اگر شما یک کد عمومی یا 
 generic
 بنویسید که روی اعداد کار میکند و انتظار دارید روی تمام ارگومان های تایپ عددی کار کند،سعی کنید از حرف های تایپ های عددی استفاده کنید که باعث میشود دامنه نوع  ارگومان های قابل قبول مینیمال شود. 
 
 
</div>

<div dir="auto">
 به عنوان مثال 
 
</div>

```jldoctest
julia> f(x) = 2.0 * x
f (generic function with 1 method)

julia> f(1//2)
1.0

julia> f(1/2)
1.0

julia> f(1)
2.0
```
<div dir="auto">
 هنگامی که 
</div>

```jldoctest
julia> g(x) = 2 * x
g (generic function with 1 method)

julia> g(1//2)
1//1

julia> g(1/2)
1.0

julia> g(1)
2
```
<div dir="auto">
 همان طور که می بینید ،ورژن دوم  موقعی که ما از 
 Int
 استفاده کردیم به عنوان نوع ارگومان ورودی در نظر گرفته شد ولی دفعه اول چنین اتفاقی نیافتاد .
 زیرا 
promote_type(Int, Float64) == Float64
 و ترفیع با ضرب اتفاق می افتد.
 به طور مشابه حرف های 
 Rational
 کمتر
 مخل نوع هستند تا 
  حرف های 
 Float64
 ولی بیشتر از 
 Int
 خراب کننده هستند 
 :


</div>

```jldoctest
julia> h(x) = 2//1 * x
h (generic function with 1 method)

julia> h(1//2)
1//1

julia> h(1/2)
1.0

julia> h(1)
2//1
```
<div dir="auto">
 پس به منظور سادگی استفاده از کد شما 
 در صورت امکان از کلمات
  'Int'
 و برای اعداد غیر صحیح از 
 `Rational{Int}`
 استفاده کنید
 .
</div>
