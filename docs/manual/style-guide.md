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
Instead of:

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
use:

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
Instead of:

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
use:

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
 در بسیاری از مثال ها که نوع خروجب اول می اید ولی مفید است که ذکر کنیم که
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
   Any other arguments.

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
   This refers to arguments that can be listed indefinitely at the end of a function call.
   For example, in `Matrix{T}(undef, dims)`, the dimensions can be given as a
   [`Tuple`](@ref), e.g. `Matrix{T}(undef, (1,2))`, or as [`Vararg`](@ref)s,
   e.g. `Matrix{T}(undef, 1, 2)`.

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
It is better to avoid errors than to rely on catching them.

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
Julia doesn't require parens around conditions in `if` and `while`. Write:

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

## Don't overuse `...`
<div dir="auto">
</div>
Splicing function arguments can be addictive. Instead of `[a..., b...]`, use simply `[a; b]`,
which already concatenates arrays. [`collect(a)`](@ref) is better than `[a...]`, but since `a`
is already iterable it is often even better to leave it alone, and not convert it to an array.

## Don't use unnecessary static parameters
<div dir="auto">
</div>
A function signature:

```julia
foo(x::T) where {T<:Real} = ...
```
<div dir="auto">
</div>
should be written as:

```julia
foo(x::Real) = ...
```
<div dir="auto">
</div>
instead, especially if `T` is not used in the function body. Even if `T` is used, it can be replaced
with [`typeof(x)`](@ref) if convenient. There is no performance difference. Note that this is
not a general caution against static parameters, just against uses where they are not needed.

Note also that container types, specifically may need type parameters in function calls. See the
FAQ [Avoid fields with abstract containers](@ref) for more information.

## Avoid confusion about whether something is an instance or a type
<div dir="auto">
</div>
Sets of definitions like the following are confusing:

```julia
foo(::Type{MyType}) = ...
foo(::MyType) = foo(MyType)
```
<div dir="auto">
</div>
Decide whether the concept in question will be written as `MyType` or `MyType()`, and stick to
it.

The preferred style is to use instances by default, and only add methods involving `Type{MyType}`
later if they become necessary to solve some problems.

If a type is effectively an enumeration, it should be defined as a single (ideally immutable struct or primitive)
type, with the enumeration values being instances of it. Constructors and conversions can check
whether values are valid. This design is preferred over making the enumeration an abstract type,
with the "values" as subtypes.

## Don't overuse macros
<div dir="auto">
</div>
Be aware of when a macro could really be a function instead.

Calling [`eval`](@ref) inside a macro is a particularly dangerous warning sign; it means the
macro will only work when called at the top level. If such a macro is written as a function instead,
it will naturally have access to the run-time values it needs.

## Don't expose unsafe operations at the interface level
<div dir="auto">
</div>
If you have a type that uses a native pointer:

```julia
mutable struct NativeType
    p::Ptr{UInt8}
    ...
end
```
<div dir="auto">
</div>
don't write definitions like the following:

```julia
getindex(x::NativeType, i) = unsafe_load(x.p, i)
```
<div dir="auto">
</div>
The problem is that users of this type can write `x[i]` without realizing that the operation is
unsafe, and then be susceptible to memory bugs.

Such a function should either check the operation to ensure it is safe, or have `unsafe` somewhere
in its name to alert callers.

## Don't overload methods of base container types
<div dir="auto">
</div>
It is possible to write definitions like the following:

```julia
show(io::IO, v::Vector{MyType}) = ...
```

This would provide custom showing of vectors with a specific new element type. While tempting,
this should be avoided. The trouble is that users will expect a well-known type like `Vector()`
to behave in a certain way, and overly customizing its behavior can make it harder to work with.

## Avoid type piracy
<div dir="auto">
</div>
"Type piracy" refers to the practice of extending or redefining methods in Base
or other packages on types that you have not defined. In some cases, you can get away with
type piracy with little ill effect. In extreme cases, however, you can even crash Julia
(e.g. if your method extension or redefinition causes invalid input to be passed to a
`ccall`). Type piracy can complicate reasoning about code, and may introduce
incompatibilities that are hard to predict and diagnose.

As an example, suppose you wanted to define multiplication on symbols in a module:

```julia
module A
import Base.*
*(x::Symbol, y::Symbol) = Symbol(x,y)
end
```
<div dir="auto">
</div>
The problem is that now any other module that uses `Base.*` will also see this definition.
Since `Symbol` is defined in Base and is used by other modules, this can change the
behavior of unrelated code unexpectedly. There are several alternatives here, including
using a different function name, or wrapping the `Symbol`s in another type that you define.

Sometimes, coupled packages may engage in type piracy to separate features from definitions,
especially when the packages were designed by collaborating authors, and when the
definitions are reusable. For example, one package might provide some types useful for
working with colors; another package could define methods for those types that enable
conversions between color spaces. Another example might be a package that acts as a thin
wrapper for some C code, which another package might then pirate to implement a
higher-level, Julia-friendly API.

## Be careful with type equality
<div dir="auto">
</div>
You generally want to use [`isa`](@ref) and [`<:`](@ref) for testing types,
not `==`. Checking types for exact equality typically only makes sense when comparing to a known
concrete type (e.g. `T == Float64`), or if you *really, really* know what you're doing.

## Do not write `x->f(x)`
<div dir="auto">
</div>
Since higher-order functions are often called with anonymous functions, it is easy to conclude
that this is desirable or even necessary. But any function can be passed directly, without being
"wrapped" in an anonymous function. Instead of writing `map(x->f(x), a)`, write [`map(f, a)`](@ref).

## Avoid using floats for numeric literals in generic code when possible
<div dir="auto">
</div>
If you write generic code which handles numbers, and which can be expected to run with many different
numeric type arguments, try using literals of a numeric type that will affect the arguments as
little as possible through promotion.

For example,

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
</div>
As you can see, the second version, where we used an `Int` literal, preserved the type of the
input argument, while the first didn't. This is because e.g. `promote_type(Int, Float64) == Float64`,
and promotion happens with the multiplication. Similarly, [`Rational`](@ref) literals are less type disruptive
than [`Float64`](@ref) literals, but more disruptive than `Int`s:

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
Thus, use `Int` literals when possible, with `Rational{Int}` for literal non-integer numbers,
in order to make it easier to use your code.
