# Performance Tips

In the following sections, we briefly go through a few techniques that can help make your Julia
code run as fast as possible.

## Avoid global variables

A global variable might have its value, and therefore its type, change at any point. This makes
it difficult for the compiler to optimize code using global variables. Variables should be local,
or passed as arguments to functions, whenever possible.

Any code that is performance critical or being benchmarked should be inside a function.

We find that global names are frequently constants, and declaring them as such greatly improves
performance:

```julia
const DEFAULT_VAL = 0
```

Uses of non-constant globals can be optimized by annotating their types at the point of use:

```julia
global x = rand(1000)

function loop_over_global()
    s = 0.0
    for i in x::Vector{Float64}
        s += i
    end
    return s
end
```

Passing arguments to functions is better style. It leads to more reusable code and clarifies what the inputs and outputs are.

```eval_rst

.. note::
    All code in the REPL is evaluated in global scope, so a variable defined and assigned
    at top level will be a **global** variable. Variables defined at top level scope inside
    modules are also global.
```

In the following REPL session:

```julia
julia> x = 1.0
```

is equivalent to:

```julia
julia> global x = 1.0
```

so all the performance issues discussed previously apply.

## Measure performance with `@time` and pay attention to memory allocation

A useful tool for measuring performance is the `@time` macro. We here repeat the example
with the global variable above, but this time with the type annotation removed:

```julia
julia> x = rand(1000);

julia> function sum_global()
           s = 0.0
           for i in x
               s += i
           end
           return s
       end;

julia> @time sum_global()
  0.009639 seconds (7.36 k allocations: 300.310 KiB, 98.32% compilation time)
496.84883432553846

julia> @time sum_global()
  0.000140 seconds (3.49 k allocations: 70.313 KiB)
496.84883432553846
```

On the first call (`@time sum_global()`) the function gets compiled. (If you've not yet used `@time`
in this session, it will also compile functions needed for timing.)  You should not take the results
of this run seriously. For the second run, note that in addition to reporting the time, it also
indicated that a significant amount of memory was allocated. We are here just computing a sum over all elements in
a vector of 64-bit floats so there should be no need to allocate memory (at least not on the heap which is what `@time` reports).

Unexpected memory allocation is almost always a sign of some problem with your code, usually a
problem with type-stability or creating many small temporary arrays.
Consequently, in addition to the allocation itself, it's very likely
that the code generated for your function is far from optimal. Take such indications seriously
and follow the advice below.

If we instead pass `x` as an argument to the function it no longer allocates memory
(the allocation reported below is due to running the `@time` macro in global scope)
and is significantly faster after the first call:

```julia
julia> x = rand(1000);

julia> function sum_arg(x)
           s = 0.0
           for i in x
               s += i
           end
           return s
       end;

julia> @time sum_arg(x)
  0.006202 seconds (4.18 k allocations: 217.860 KiB, 99.72% compilation time)
496.84883432553846

julia> @time sum_arg(x)
  0.000005 seconds (1 allocation: 16 bytes)
496.84883432553846
```

The 1 allocation seen is from running the `@time` macro itself in global scope. If we instead run
the timing in a function, we can see that indeed no allocations are performed:

```julia
julia> time_sum(x) = @time sum_arg(x);

julia> time_sum(x)
  0.000001 seconds
496.84883432553846
```

In some situations, your function may need to allocate memory as part of its operation, and this
can complicate the simple picture above. In such cases, consider using one of the [tools](@ref tools)
below to diagnose problems, or write a version of your function that separates allocation from
its algorithmic aspects (see Pre-allocating outputs).

```eval_rst

.. note::
    For more serious benchmarking, consider the [BenchmarkTools.jl](https://github.com/JuliaCI/BenchmarkTools.jl)
    package which among other things evaluates the function multiple times in order to reduce noise.
```

## Tools

Julia and its package ecosystem includes tools that may help you diagnose problems and improve
the performance of your code:

  * Profiling allows you to measure the performance of your running code and identify lines
    that serve as bottlenecks. For complex projects, the [ProfileView](https://github.com/timholy/ProfileView.jl)
    package can help you visualize your profiling results.
  * The [Traceur](https://github.com/JunoLab/Traceur.jl) package can help you find common performance problems in your code.
  * Unexpectedly-large memory allocations--as reported by `@time`, `@allocated`, or
    the profiler (through calls to the garbage-collection routines)--hint that there might be issues
    with your code. If you don't see another reason for the allocations, suspect a type problem.
     You can also start Julia with the `--track-allocation=user` option and examine the resulting
    `*.mem` files to see information about where those allocations occur. See Memory allocation analysis.
  * `@code_warntype` generates a representation of your code that can be helpful in finding expressions
    that result in type uncertainty. See `@code_warntype` below.

## Avoid containers with abstract type parameters

When working with parameterized types, including arrays, it is best to avoid parameterizing with
abstract types where possible.

Consider the following:

```julia
julia> a = Real[]
Real[]

julia> push!(a, 1); push!(a, 2.0); push!(a, π)
3-element Vector{Real}:
 1
 2.0
 π = 3.1415926535897...
```

Because `a` is an array of abstract type `Real`, it must be able to hold any
`Real` value. Since `Real` objects can be of arbitrary size and structure, `a` must be
represented as an array of pointers to individually allocated `Real` objects. However, if we instead
only allow numbers of the same type, e.g. `Float64`, to be stored in `a` these can be stored more
efficiently:

```julia
julia> a = Float64[]
Float64[]

julia> push!(a, 1); push!(a, 2.0); push!(a,  π)
3-element Vector{Float64}:
 1.0
 2.0
 3.141592653589793
```

Assigning numbers into `a` will now convert them to `Float64` and `a` will be stored as
a contiguous block of 64-bit floating-point values that can be manipulated efficiently.

If you cannot avoid containers with abstract value types, it is sometimes better to
parametrize with `Any` to avoid runtime type checking. E.g. `IdDict{Any, Any}` performs
better than `IdDict{Type, Vector}`

See also the discussion under Parametric Types.

<div dir="auto"> 

## تعریف‌کردن نوع داده

در بسیاری از زبان‌ها با [قابلیت] اختیاری تعریف نوع، افزودن این تعاریف، روش اصلی برای سریع‌تر اجرا شدن کد است. در جولیا اینگونه نیست. در جولیا، کامپایلر به طور کلی نوع‌های تمام آرگومان‌های تابع، متغیرهای محلی و عبارات را می داند. اگر چه، چند مورد خاص وجود دارد که این تعاریف مفید هستند.
    
### از فیلدهای با نوع انتزاعی پرهیز کنید

نوع‌ها می‌توانند بدون مشخص‌کردن نوع‌های فیلدهای آن‌ها تعیین شوند:

</div>
    
```julia
julia> struct MyAmbiguousType
           a
       end
```

<div dir="auto"> 

 این اجازه می دهد تا `a` از هر نوع باشد. این اغلب می تواند مفید باشد ، اما یک نقطه ضعف هم دارد: برای اشیا از نوع `MyAmbiguousType`، کامپایلر قادر به ساخت کد با عملکرد بالا نخواهد بود. دلیل این امر این است که کامپایلر برای تعیین نحوه ساخت کد از نوع‌های اشیا، و نه مقادیر آن‌ها استفاده می کند. متأسفانه در مورد یک شی از نوع `MyAmbiguousType` چیزهای کمی قابل استنباط است:
 
</div>

```julia
julia> b = MyAmbiguousType("Hello")
MyAmbiguousType("Hello")

julia> c = MyAmbiguousType(17)
MyAmbiguousType(17)

julia> typeof(b)
MyAmbiguousType

julia> typeof(c)
MyAmbiguousType
```

<div dir="auto"> 

مقادیر `b` و `c`  نوع یکسانی دارند، اما بازنمایی اصلی داده‌های آنها در حافظه بسیار متفاوت است. حتی اگر فقط مقادیر عددی را در قسمت `a` ذخیره کرده باشید، این واقعیت که نمایش حافظه `UInt8` با `Float64` متفاوت است، به این معنی است که پردازنده باید با استفاده از دو نوع دستورالعمل مختلف آن‌ها را اداره کند. از آنجا که اطلاعات مورد نیاز در نوع موجود نیست، چنین تصمیماتی باید در زمان اجرا گرفته شوند. این عملکرد را کند می کند.
  
  با اعلام نوع `a` می توانید بهتر عمل کنید. در اینجا، ما بر روی مواردی متمرکز شده‌ایم که ممکن است یکی از نوع‌های مختلف باشد، در این صورت راه حل طبیعی استفاده از پارامترها است. برای مثال:
    
</div>

```julia
julia> mutable struct MyType{T<:AbstractFloat}
           a::T
       end
```

<div dir="auto">

این انتخاب بهتری نسبت به مثال زیر است:

</div>

```julia
julia> mutable struct MyStillAmbiguousType
           a::AbstractFloat
       end
```

<div dir="auto">

زیرا نسخه اول نوع `a` را از نوع شی wrapper object مشخص می کند. برای مثال:
    
</div>

```julia
julia> m = MyType(3.2)
MyType{Float64}(3.2)

julia> t = MyStillAmbiguousType(3.2)
MyStillAmbiguousType(3.2)

julia> typeof(m)
MyType{Float64}

julia> typeof(t)
MyStillAmbiguousType
```

<div dir="auto">

نوع فیلد `a` را می توان به راحتی از نوع `m` تعیین کرد، اما از نوع `t` نمی توان تعیین کرد. در واقع، در `t` امکان تغییر نوع فیلد `a` وجود دارد:

</div>

```julia
julia> typeof(t.a)
Float64

julia> t.a = 4.5f0
4.5f0

julia> typeof(t.a)
Float32
```

<div dir="auto">

در مقابل، هرگاه `m` ساخته شود، نوع `m.a` نمی‌تواند تغییر کند:

</div>

```julia
julia> m.a = 4.5f0
4.5f0

julia> typeof(m.a)
Float64
```

<div dir="auto">

 این واقعیت که نوع `m.a` از روی نوع `m`شناخته شده است - همراه با این واقعیت که نوع آن نمی تواند تابع میانی را تغییر دهد - به کامپایلر اجازه می‌دهد برای اشیایی مانند `m` و نه برای اشیایی مانند `t` کد بسیار بهینه‌سازی شده تولید کند.
  البته، همه این‌ها درست است اگر ما `m` را از نوعی واقعی بسازیم. ما می‌توانیم این را با ساختن صریح آن از یک نوع انتزاعی نقض کنیم:
    
</div>

```julia
julia> m = MyType{AbstractFloat}(3.2)
MyType{AbstractFloat}(3.2)

julia> typeof(m.a)
Float64

julia> m.a = 4.5f0
4.5f0

julia> typeof(m.a)
Float32
```

<div dir="auto">

 برای همه اهداف عملی، چنین اشیایی رفتار یکسانی با `MyStillAmbiguousType` دارند.
  بسیار آموزنده است که این را با تابع ساده

</div>
    
```julia
func(m::MyType) = m.a+1
```

<div dir="auto">

با استفاده از

</div>
    
```julia
code_llvm(func, Tuple{MyType{Float64}})
code_llvm(func, Tuple{MyType{AbstractFloat}})
```

<div dir="auto">

مقایسه کرد.
  به دلیل طولانی بودن، نتایج در اینجا نشان داده نمی شوند، اما ممکن است بخواهید خودتان این کار را انجام دهید. از آنجا که نوع در حالت اول کاملاً مشخص شده است، کامپایلر نیازی به تولید کدی برای حل نوع در زمان اجرا ندارد. این منجر به کد کوتاه‌تر و سریع‌تر می شود.

### از فیلدهایی که دارای container انتزاعی هستند خودداری کنید

روش‌های  best practice مشابه برای نوع‌های `container` نیز کار می‌کند:
    
</div>

```julia
julia> struct MySimpleContainer{A<:AbstractVector}
           a::A
       end

julia> struct MyAmbiguousContainer{T}
           a::AbstractVector{T}
       end
```

<div dir="auto">

برای مثال:

</div>

```julia
julia> c = MySimpleContainer(1:3);

julia> typeof(c)
MySimpleContainer{UnitRange{Int64}}

julia> c = MySimpleContainer([1:3;]);

julia> typeof(c)
MySimpleContainer{Vector{Int64}}

julia> b = MyAmbiguousContainer(1:3);

julia> typeof(b)
MyAmbiguousContainer{Int64}

julia> b = MyAmbiguousContainer([1:3;]);

julia> typeof(b)
MyAmbiguousContainer{Int64}
```

<div dir="auto">

برای `MySimpleContainer`،این شی با نوع و پارامترهای آن کاملاً مشخص شده است، بنابراین کامپایلر می‌تواند توابع بهینه شده‌ای را ایجاد کند. در بیشتر موارد، احتمالاً کافی است.
  
  در حالی که اکنون کامپایلر می‌تواند وظیفه خود را به خوبی انجام دهد، مواردی وجود دارد که شما ممکن است بخواهید کد شما بسته به نوع عنصر `a`، کارهای مختلفی انجام دهد. معمولاً بهترین راه برای رسیدن به این هدف این است که عملیات خاص خود را (در اینجا، `foo`) در یک تابع جداگانه قرار دهید:
    
</div>

```julia
julia> function sumfoo(c::MySimpleContainer)
           s = 0
           for x in c.a
               s += foo(x)
           end
           s
       end
sumfoo (generic function with 1 method)

julia> foo(x::Integer) = x
foo (generic function with 1 method)

julia> foo(x::AbstractFloat) = round(x)
foo (generic function with 2 methods)
```

<div dir="auto">

 این چیز‌ها را ساده نگه می‌دارد در حالی که به کامپایلر اجازه می‌دهد تا در همه موارد کد بهینه تولید کند.
  
  با این حال، مواردی وجود دارد که ممکن است لازم باشد نسخه‌های مختلفی از تابع خارجی را برای نوع‌های مختلف عناصر یا نوع‌های `AbstractVector` از فیلد `a` در `MySimpleContainer` تعیین کنید. می توانید این کار را این‌گونه انجام دهید:
    
</div>

```julia
julia> function myfunc(c::MySimpleContainer{<:AbstractArray{<:Integer}})
           return c.a[1]+1
       end
myfunc (generic function with 1 method)

julia> function myfunc(c::MySimpleContainer{<:AbstractArray{<:AbstractFloat}})
           return c.a[1]+2
       end
myfunc (generic function with 2 methods)

julia> function myfunc(c::MySimpleContainer{Vector{T}}) where T <: Integer
           return c.a[1]+3
       end
myfunc (generic function with 3 methods)
```

```julia
julia> myfunc(MySimpleContainer(1:3))
2

julia> myfunc(MySimpleContainer(1.0:3))
3.0

julia> myfunc(MySimpleContainer([1:3;]))
4
```

<div dir="auto">

### مقدار‌های گرفته شده از مکان‌های با نوع تعیین نشده را حاشیه‌نویسی کنید

 کار با ساختارهای داده‌ای که ممکن است حاوی مقادیر از هر نوع باشند (آرایه های نوع `Array {Any}`) اغلب راحت است. اما، اگر از یکی از این ساختارها استفاده می کنید و به طور اتفاقی نوع یک عنصر را می‌دانید، به اشتراک گذاری این دانش با کامپایلر کمک می کند:
    
</div>

```julia
function foo(a::Array{Any,1})
    x = a[1]::Int32
    b = x+1
    ...
end
```

<div dir="auto">

 در اینجا، ما به طور تصادفی دانستیم که اولین عنصر `a` یک `Int32` است. ساختن حاشیه‌نویسی مانند این مزیت اضافه شده‌ای دارد که اگر مقدار از نوع مورد انتظار نباشد خطای زمان اجرا را ایجاد می‌کند و به طور بالقوه اشکالات خاصی را زودتر می‌گیرد.
  
  درصورتی که نوع `a[1]` دقیقاً مشخص نباشد، `x` را می‌توان از طریق `x = convert(Int32, a[1])::Int32` تعیین کرد. استفاده از تابع تبدیل به `[1]a` این امکان را می‌دهد که `[1]a`هر شی قابل تبدیل به `Int32` باشد (مانند `UInt8`)، بنابراین جامع‌بودن کد را با کم‌کردن قید‌های تعیین‌کردن نوع افزایش می‌دهد. توجه داشته باشید که convert خود برای دستیابی به پایداری نوع، به حاشیه‌نویسی نوع در این زمینه نیاز دارد. دلیل این امر این است که کامپایلر نمی تواند نوع مقدار بازگشتی یک تابع را حدس بزند، حتی تابع convert ،مگر اینکه نوع‌های تمام آرگومان‌های تابع شناخته شده باشد.
  
  حاشیه‌نویسی نوع اگر نوع انتزاعی باشد یا در زمان اجرا ساخته شود، عملکرد را افزایش نمی‌دهد (و در واقع می‌تواند مانع آن شود). دلیل این امر این است که کامپایلر نمی‌تواند از حاشیه‌نویسی برای تخصصی کردن کد بعدی استفاده کند و خود بررسی‌کردن تایپ نیز زمان بر است. به عنوان مثال، در کد:
    
</div>

```julia
function nr(a, prec)
    ctype = prec == 32 ? Float32 : Float64
    b = Complex{ctype}(a)
    c = (b + 1.0f0)::Complex{ctype}
    abs(c)
end
```

<div dir="auto">

حاشیه‌نویسی `c` به عملکرد آسیب می‌رساند. برای نوشتن کد تابع با نوع‌های ساخته شده در زمان اجرا، از تکنیک function-barrier استفاده شده و اطمینان حاصل کنید که نوع ساخته شده در میان نوع‌های آرگومان تابع هسته ظاهر می شود تا عملیات هسته به درستی توسط کامپایلر تخصصی شود. به عنوان مثال، در قطعه فوق، به محض ساخت `b`، می‌توان آن را به یک تابع دیگر `k`، هسته، منتقل کرد. اگر به عنوان مثال، تابع `k`، `b` را به عنوان آرگومان از نوع `Complex {T}` اعلام کند که `T` یک پارامتر نوع است، یک حاشیه‌نویسی نوع در یک عبارت انتساب که در `k` به فرم:
    
</div>

```julia
c = (b + 1.0f0)::Complex{T}
```

<div dir="auto">

 ظاهر می شود، مانع عملکرد نمی‌شود (اما کمکی هم نمی کند) زیرا کامپایلر می‌تواند نوع `c` را در زمان کامپایل `k` تعیین کند.

### حواستان باشد که چه زمانی جولیا از تخصصی‌کردن جلوگیری می‌کند

جولیا به عنوان یک ابتکار عمل، از تخصصی کردن خودکار آرگومان  پارامترهای نوع در سه حالت خاص: `Type` ، `Function` و `Vararg` جلوگیری می‌کند. جولیا همیشه وقتی اختصاصی‌ می‌کند که آرگومان در تابع استفاده شده باشد، اما اگر این آرگومان به یک تابع دیگر پاس داده نشوند، این کار را نمی‌کند. این معمولاً در زمان اجرا هیچ تاثیری بر عملکرد ندارد و [عملکرد کامپایلر را بهبود می بخشد]. اگر فهمیدید که در زمان اجرا در مورد شما در عملکرد تأثیر دارد، می توانید با افزودن یک پارامتر type به متد، تخصص را ایجاد کنید. در اینجا چند نمونه آورده شده است:
    
  این تخصصی نخواهد بود:
    
</div>

```julia
function f_type(t)  # or t::Type
    x = ones(t, 10)
    return sum(map(sin, x))
end
```

<div dir="auto">

 اما این تخصصی است:
    
</div>

```julia
function g_type(t::Type{T}) where T
    x = ones(T, 10)
    return sum(map(sin, x))
end
```

<div dir="auto">

  این تخصصی نخواهد بود:
    
</div>

```julia
f_func(f, num) = ntuple(f, div(num, 2))
g_func(g::Function, num) = ntuple(g, div(num, 2))
```

<div dir="auto">

 اما این تخصصی است:
    
</div>

```julia
h_func(h::H, num) where {H} = ntuple(h, div(num, 2))
```

<div dir="auto">

  این تخصصی نخواهد بود:
    
</div>

```julia
f_vararg(x::Int...) = tuple(x...)
```

<div dir="auto">

 اما این تخصصی است:
    
</div>

```julia
g_vararg(x::Vararg{Int, N}) where {N} = tuple(x...)
```

<div dir="auto">

 فقط کافی است یک پارامتر نوع را برای اجبار به تخصصی کردن معرفی کنید ، حتی اگر نوع‌های دیگر قیدی نداشته باشند. به عنوان مثال، این نیز تخصصی خواهد بود و زمانی مفید خواهد بود که آرگومان‌ها همه از یک نوع نباشند:
    
</div>
    
```julia
h_vararg(x::Vararg{Any, N}) where {N} = tuple(x...)
```

<div dir="auto">

 توجه داشته باشید که `code_typed@` و موارد مشابه همیشه کدهای تخصصی را به شما نشان می دهند، حتی اگر جولیا به طور معمول در آن فراخوانی متد تخصصی نداشته باشد. اگر می خواهید ببینید که آیا در هنگام تغییر نوع‌های آرگومان، تخصصی ایجاد می شود یا خیر، باید method internals را بررسی کنید به بیانی واضح‌تر اگر `((...)which f@)` شامل تخصصی کردن برای آرگومان‌های مورد بحث است.

## توابع را به چندین تعریف تقسیم کنید

نوشتن یک تابع به همان تعداد تابع کوچک، به کامپایلر این امکان را می دهد تا مستقیم‌ترین کد کاربردی را فراخوانی کند، یا حتی آن را درون‌خطی کند.

در اینجا مثالی از "تابع مرکب" آورده شده است که باید به صورت تعریف های متعدد نوشته شود
    
</div>

```julia
using LinearAlgebra

function mynorm(A)
    if isa(A, Vector)
        return sqrt(real(dot(A,A)))
    elseif isa(A, Matrix)
        return maximum(svdvals(A))
    else
        error("mynorm: invalid argument")
    end
end
```

<div dir="auto">

این می تواند به طور خلاصه و کارآمد نوشته شود:
    
</div>

```julia
norm(x::Vector) = sqrt(real(dot(x, x)))
norm(A::Matrix) = maximum(svdvals(A))
```
<div dir="auto">


با این حال باید توجه داشت که کامپایلر در بهینه سازی شاخه‌های مرده در کدی که به عنوان مثال `mynorm` نوشته شده است کاملاً کارآمد است.

## توابعی با پایداری نوع بنویسید

 در صورت امکان،مفید است که اطمینان حاصل کنیم که یک تابع همیشه مقداری از همان نوع را برمی‌گرداند. تعریف زیر را در نظر بگیرید:

</div>
    
```julia
pos(x) = x < 0 ? 0 : x
```

<div dir="auto">

 اگرچه این امر به اندازه کافی بی ضرر به نظر می رسد، اما مشکل این است که `0` یک عدد صحیح است (از نوع `Int`) و `x` ممکن است از هر نوع باشد. بنابراین، بسته به مقدار `x`، این تابع ممکن است مقداری از هر دو نوع را برگرداند. این رفتار مجاز است و ممکن است در بعضی موارد مطلوب باشد. اما به راحتی می توان به صورت زیر برطرف کرد:
    
</div>

```julia
pos(x) = x < 0 ? zero(x) : x
```

<div dir="auto">

 همچنین یک تابع واحد و یک نوع کلی تر از نوع `(x، y)` وجود دارد که `y` را به نوع `x` تبدیل می کند.

## از تغییر نوع متغیر خودداری کنید

 یک مشکل مشابه "پایداری نوع" برای متغیرهایی که به طور مکرر در یک تابع استفاده می شوند وجود دارد
    
</div>

```julia
function foo()
    x = 1
    for i = 1:10
        x /= rand()
    end
    return x
end
```

<div dir="auto">

 متغیر محلی x به عنوان یک عدد صحیح شروع می‌شود و بعد از یک حلقه تکرار به یک عدد اعشاری تبدیل می‌شود (نتیجه عملگر /). این امر بهینه سازی بدنه حلقه را برای کامپایلر دشوارتر می کند. چندین رفع احتمالی وجود دارد
    
  * ابتدا `x` را با `x = 1.0` مقداردهی اولیه کنید
  * نوع x را صریحاً با  `x::Float64 = 1` اعلام کنید
  * از تبدیل صریح `x = oneunit(Float64)` استفاده کنید
  * ابتدا با تکرار حلقه اول، `()x = 1 / rand` را شروع کنید ، سپس برای `i = 2:10` حلقه را تکرار کنید

## توابع هسته را جدا کنید(function barriers)

بسیاری از توابع از الگویی برای انجام برخی تنظیمات و سپس اجرای تکرارهای زیاد برای انجام یک محاسبه اصلی پیروی می‌کنند. در صورت امکان، بهتر است این محاسبات اصلی را در توابع جداگانه قرار دهید. به عنوان مثال، تابع ساختاری زیر، آرایه ای از نوع تصادفی را برمی گرداند:
    
</div>

```julia
julia> function strange_twos(n)
           a = Vector{rand(Bool) ? Int64 : Float64}(undef, n)
           for i = 1:n
               a[i] = 2
           end
           return a
       end;

julia> strange_twos(3)
3-element Vector{Float64}:
 2.0
 2.0
 2.0
```

<div dir="auto">

این باید به صورت زیر نوشته شود:
    
</div>

```julia
julia> function fill_twos!(a)
           for i = eachindex(a)
               a[i] = 2
           end
       end;

julia> function strange_twos(n)
           a = Vector{rand(Bool) ? Int64 : Float64}(undef, n)
           fill_twos!(a)
           return a
       end;

julia> strange_twos(3)
3-element Vector{Float64}:
 2.0
 2.0
 2.0
```

<div dir="auto">

کامپایلر جولیا برای انواع آرگومان‌ها در مرزهای تابع کد را تخصصی  می‌کند، بنابراین در پیاده سازی اصلی، نوع `a` را در حین حلقه نمی‌داند (از آنجا که به صورت تصادفی انتخاب می شود). بنابراین نسخه دوم به طور کلی سریع‌تر است زیرا حلقه داخلی می‌تواند به عنوان بخشی از `fill_twos` برای انواع مختلف `a` دوباره کامپایل شود!

فرم دوم نیز اغلب سبک بهتری است و می تواند منجر به استفاده مجدد از کد شود.

این الگو در چندین مکان در julia Base استفاده می شود. به عنوان مثال ، `vcat` و `hcat` را در `abstractarray.jl` یا تابع `!fill` را ببینید! تابع ، که می توانستیم به جای نوشتن `fill_twos` از آن استفاده کنیم!

توابعی مانند `strange_twos` هنگام کار با داده‌هایی از نوع نامشخص، به عنوان مثال داده‌هایی که از یک فایل ورودی بارگیری می‌شوند که ممکن است شامل اعداد صحیح، اعشاری، رشته ها یا موارد دیگر باشند، اتفاق می‌افتد.

## نوع‌هایی با مقادیر به عنوان پارامتر

فرض کنید شما می‌خواهید یک آرایه `N` بعدی ایجاد کنید که دارای اندازه 3 در هر محور باشد. چنین آرایه‌هایی را می‌توان به صورت زیر ایجاد کرد:
    
</div>

```julia
julia> A = fill(5.0, (3, 3))
3×3 Matrix{Float64}:
 5.0  5.0  5.0
 5.0  5.0  5.0
 5.0  5.0  5.0
```

<div dir="auto">

این روش بسیار خوب کار می‌کند: کامپایلر می‌تواند بفهمد که `A` یک آرایه است `Array{Float64,2}` زیرا نوع مقدار درحال پر شدن (`5.0::Float64`)  و ابعاد (`(3, 3)::NTuple{2,Int}`) را می‌داند. این بدان معنی است که کامپایلر می‌تواند برای هر استفاده بعدی از `A` در همان عملکرد، کد بسیار کارآمدی تولید کند.

اما اکنون فرض کنید می‌خواهید تابعی بنویسید که یک آرایه   ...× 3 × 3 در ابعاد دلخواه ایجاد کند. ممکن است تمایل داشته باشید که یک تابع را بنویسید:
    
</div>

```julia
julia> function array3(fillval, N)
           fill(fillval, ntuple(d->3, N))
       end
array3 (generic function with 1 method)

julia> array3(5.0, 2)
3×3 Matrix{Float64}:
 5.0  5.0  5.0
 5.0  5.0  5.0
 5.0  5.0  5.0
```

<div dir="auto">
    
این کار می‌کند، اما (همانطور که با استفاده از `code_warntype array3(5.0, 2)@` می توانید خودتان تأیید کنید) این است که نمی توان نوع خروجی را حدس زد: آرگومان `N` یک مقدار از نوع `Int` است ، و حدس زدن نوع مقدار آن را از قبل پیش بینی نمی کند (نمی تواند). این به این معنی است که کدی که از خروجی این استفاده می کند باید عملکرد محافظه کارانه‌ای باشد ، اینکه نوع را در هر دسترسی `A` بررسی می‌کند، کد را بسیار کند می‌کند.

اکنون ، یک راه بسیار خوب برای حل چنین مشکلاتی استفاده از [تکنیک function-barrier](ref kernel-functions@) است.
با این حال ، در برخی موارد ممکن است بخواهید به طور کلی بی ثباتی نوع را از بین ببرید. در اینگونه موارد،
یک روش پاس دادن dimensionality به عنوان یک پارامتر است، به عنوان مثال `(){T}Val` ("Value types" را ببینید):
    
</div>

```julia
julia> function array3(fillval, ::Val{N}) where N
           fill(fillval, ntuple(d->3, Val(N)))
       end
array3 (generic function with 1 method)

julia> array3(5.0, Val(2))
3×3 Matrix{Float64}:
 5.0  5.0  5.0
 5.0  5.0  5.0
 5.0  5.0  5.0
```

<div dir="auto">

جولیا یک نسخه تخصصی از `ntuple` دارد که نمونه `Val {:: Int}` را به عنوان پارامتر دوم می پذیرد. با پاس دادن `N` به عنوان یک پارامتر نوع، "مقدار" آن را برای کامپایلر مشخص می کنید. در نتیجه، این نسخه از `array3` به کامپایلر اجازه می‌دهد تا نوع بازگشت را پیش‌بینی کند.

با این حال، استفاده از چنین تکنیک‌هایی می‌تواند به طرز شگفت‌انگیزی ظریف باشد. به عنوان مثال ، اگر `array3` را از تابعی مانند این فراخوانی کنید، فایده‌ای نخواهد داشت:
    
</div>

```julia
function call_array3(fillval, n)
    A = array3(fillval, Val(n))
end
```

<div dir="auto">

در اینجا، شما مجدداً همین مشکل را ایجاد کرده‌اید: کامپایلر نمی‌تواند حدس بزند `n` چیست ، بنابراین نوع `Val (n)` را نمی‌داند. تلاش کردن برای استفاده از `Val`، اما با انجام چنین اشتباهی، به راحتی می‌تواند عملکرد را در بسیاری از شرایط بدتر کند. (فقط در شرایطی که شما به طور موثر `Val` را با ترفند function-barrier ترکیب می‌کنید، تا عملکرد هسته را کارآمدتر کنید، باید از کدی مانند موارد بالا استفاده شود.)

مثالی از کاربرد صحیح `Val` این است:
    
</div>

```julia
function filter3(A::AbstractArray{T,N}) where {T,N}
    kernel = array3(1, Val(N))
    filter(A, kernel)
end
```

<div dir="auto">

در این مثال، `N` به عنوان یک پارامتر پاس داده می شود، بنابراین "مقدار" آن برای کامپایلر شناخته شده است. اساساً، `Val (T)` فقط زمانی کار می کند که `T` یا hard-code / به طور لفظی (`Val (3)`) باشد یا قبلاً در حوزه نوع مشخص شده باشد.

## خطرات سوء استفاده از multiple dispatch (به عبارت دیگر، مواردی بیشتر در مورد نوع‌هایی با مقادیر به عنوان پارامتر)

هنگامی که فرد یاد گرفت که از multiple dispatch قدردانی کند، تمایل قابل فهم برای فراتر رفتن و تلاش برای استفاده از آن برای همه چیز وجود دارد. به عنوان مثال، ممکن است تصور کنید از آن برای ذخیره اطلاعات استفاده کنید. برای مثال:
    
</div>

```
struct Car{Make, Model}
    year::Int
    ...more fields...
end
```

<div dir="auto">

و سپس dispatch روی اشیائی مانند `(سال ، آرکومان‌ها ...)Car {: Honda،: Accord}`.

این ممکن است باارزش باشد در صورتی که هر یک از موارد زیر درست باشد:

  * شما به پردازش فشرده پردازنده در هر `Car` نیاز دارید و اگر از `Make` و `Model` در زمان کامپایل مطلع شوید  تعداد کل `Model` یا `Make`های مختلف مورد استفاده خیلی زیاد نباشد بسیار کارآمدتر می شود.
  *  شما لیست های یکنواختی از همان نوع `Car`  برای پردازش دارید ، بنابراین می توانید همه آنها را در یک `Array{Car{:Honda,:Accord},N}` ذخیره کنید.

هنگامی که مورد دوم برقرار است ، عملکردی که چنین آرایه ای همگن را پردازش می کند می تواند به طور سازنده‌ای تخصصی باشد: جولیا نوع هر عنصر را از قبل می داند (همه اشیا موجود در container از نوع واقعی یکسانی هستند) ، بنابراین جولیا می تواند روش صحیح را "جستجو" کند هنگامی که تابع در حال کامپایل شدن است (برای نیاز نداشتن به بررسی در زمان اجرا) و در نتیجه کد کارآمد برای پردازش کل لیست منتشر می شود.

وقتی اینها برقرار نباشند، احتمالاً هیچ سودی نخواهید برد. بدتر از آن ، "انفجار ترکیبی انواع" نتیجه معکوس خواهد داشت. اگر موارد [i + 1] نوع متفاوتی از مورد [i] داشته باشد، جولیا باید در زمان اجرا به جستجوی نوع بپردازد، روش مناسب را در جداول روش جستجو کند، تصمیم بگیرد (از طریق type intersection) کدام یک مطابقت دارد، تعیین کند که آیا هنوز JIT کامپایل شده است (و اگر نشده این کار را انجام دهد)، و سپس فراخوانی کند. در حقیقت، شما از type- system و JIT-compilation machinery می خواهید که اساساً معادل یک سوئیچ یا جستجوی دیکشنری را در کد خود اجرا کنند.

برخی از معیارهای زمان اجرا با مقایسه type dispatchِ ، جستجوی دیکشنری ، و عبارت "سوییچ" را می توان در لیست پستی یافت.

شاید حتی بدتر از تأثیر زمان اجرا ، تأثیر زمان کامپایل باشد: جولیا توابع تخصصی را برای هر `Car{Make, Model}` متفاوت کامپایل می کند. اگر صدها یا هزاران نوع از این نوع را داشته باشید، پس هر تابعی که چنین جسمی را به عنوان یک پارامتر بپذیرد (از یک تابع `get_year` مرسوم که ممکن است خودتان بنویسید، تا تابع عمومی `!push` در Julia Base) برای این صدها یا هزاران نوع کامپایل دارد. هر یک از این موارد باعث افزایش اندازه حافظه cache کد کامپایل شده، طول لیست های داخلی متدها و غیره می شود. اشتیاق بیش از حد برای  مقادیر به عنوان پارامتر می تواند به راحتی منابع عظیم را هدر دهد.

## دسترسی به آرایه ها به ترتیب حافظه ، در امتداد ستون ها

آرایه های چند بعدی در جولیا به ترتیب ستونی ذخیره می شوند. این بدان معنی است که آرایه ها هر بار یک ستون را روی بقیه قرار می‌دهند. همانطور که در زیر نشان داده شده است ، می توانید با استفاده از تابع `vec` یا syntax `[:]` این را ببینید (توجه داشته باشید که آرایه مرتب شده `[1 3 2 4]` است، نه `[4 3 2 1]`:
    
</div>

```julia
julia> x = [1 2; 3 4]
2×2 Matrix{Int64}:
 1  2
 3  4

julia> x[:]
4-element Vector{Int64}:
 1
 3
 2
 4
```

<div dir="auto">

این قرارداد برای ترتیب آرایه ها در بسیاری از زبانها مانند Fortran ، Matlab و R (برای نام بردن چند مورد) معمول است. گزینه جایگزینی برای ترتیب بر اساس ستون، ترتیب بر اساس ردیف است که قراردادی است که توسط C و Python (`numpy`) در میان زبان‌های دیگر تصویب شده است. به خاطر سپردن ترتیب آرایه ها هنگام اجرای حلقه روی آرایه ها می تواند تأثیرات قابل توجهی در عملکرد داشته باشد. یک قانون کلی که باید به خاطر داشته باشید این است که با آرایه های با ترتیب ستونی، اولین اندیس با سرعت بیشتری تغییر می کند. اساساً این بدان معناست که اگر اندیس داخلی ترین حلقه اولین‌ای باشد که در یک بخش از عبارت ظاهر می‌شود، حلقه سریعتر خواهد بود. به خاطر داشته باشید که اندیس‌گذاری آرایه با: یک حلقه ضمنی است که به طور تکراری به همه عناصر در یک بعد خاص دسترسی پیدا می کند. برای مثال می توان ستون ها را سریعتر از سطرها استخراج کرد.

مثال ساختگی زیر را در نظر بگیرید. تصور کنید که می خواهیم تابعی بنویسیم که یک بردار را ورودی بگیرد و یک ماتریس مربعی را با ردیف ها یا ستون های مشابه بردار ورودی برگرداند. فرض کنید که پر کردن سطری یا ستونی با این نسخه‌ها مهم نیست (شاید بقیه کد را بتوان به راحتی بر این اساس تنظیم کرد). ما می توانیم این کار را حداقل از چهار طریق انجام دهیم (علاوه بر فراخوانی پیشنهادی برای built-in `repeat`):
    
</div>

```julia
function copy_cols(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for i = inds
        out[:, i] = x
    end
    return out
end

function copy_rows(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for i = inds
        out[i, :] = x
    end
    return out
end

function copy_col_row(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for col = inds, row = inds
        out[row, col] = x[row]
    end
    return out
end

function copy_row_col(x::Vector{T}) where T
    inds = axes(x, 1)
    out = similar(Array{T}, inds, inds)
    for row = inds, col = inds
        out[row, col] = x[col]
    end
    return out
end
```

<div dir="auto">

اکنون زمان هر یک از این توابع را با استفاده از همان بردار ورودی تصادفی `10000` در `1` تعیین خواهیم کرد:

    
</div>

```julia
julia> x = randn(10000);

julia> fmt(f) = println(rpad(string(f)*": ", 14, ' '), @elapsed f(x))

julia> map(fmt, [copy_cols, copy_rows, copy_col_row, copy_row_col]);
copy_cols:    0.331706323
copy_rows:    1.799009911
copy_col_row: 0.415630047
copy_row_col: 1.721531501
```

<div dir="auto">

توجه داشته باشید که مجموعه های `copy_col` خیلی سریع‌تر از `copy_rows` است. این انتظار می رود زیرا `copy_cols` به طرح حافظه مبتنی بر ستون `Matrix` احترام می گذارد و آن را هر بار یک ستون پر می کند. علاوه بر این ، `copy_col_row` بسیار سریع‌تر از `copy_row_col` است زیرا از قانون کلی ما پیروی می‌کند که اولین عنصری که در یک بخش از عبارت ظاهر می شود، باید با داخلی‌ترین حلقه همراه شود.

## از قبل تخصیص دادن خروجی ها

اگر تابع شما یک آرایه یا نوع پیچیده دیگری را بازگرداند، ممکن است مجبور به اختصاص حافظه باشد. متأسفانه،اغلب اوقات تخصیص و معکوس آن، جمع آوری زباله، گلوگاه های قابل توجهی است.

گاهی اوقات می توانید با تخصیص از قبل خروجی، نیاز به اختصاص حافظه را روی هر فراخوانی تابع دور بزنید. به عنوان یک مثال پیش پا افتاده، کد زیر را

</div>

```julia
julia> function xinc(x)
           return [x, x+1, x+2]
       end;

julia> function loopinc()
           y = 0
           for i = 1:10^7
               ret = xinc(i)
               y += ret[2]
           end
           return y
       end;
```

<div dir="auto">

با این

</div>
    
```julia
julia> function xinc!(ret::AbstractVector{T}, x::T) where T
           ret[1] = x
           ret[2] = x+1
           ret[3] = x+2
           nothing
       end;

julia> function loopinc_prealloc()
           ret = Vector{Int}(undef, 3)
           y = 0
           for i = 1:10^7
               xinc!(ret, i)
               y += ret[2]
           end
           return y
       end;
```

<div dir="auto">

  مقایسه کنید.

نتایج زمانی:

</div>

```julia
julia> @time loopinc()
  0.529894 seconds (40.00 M allocations: 1.490 GiB, 12.14% gc time)
50000015000000

julia> @time loopinc_prealloc()
  0.030850 seconds (6 allocations: 288 bytes)
50000015000000
```

<div dir="auto">

از قبل تخصیص دادن مزایای دیگری نیز دارد، به عنوان مثال اجازه دادن به مکان فراخوانی  برای کنترل نوع "خروجی" از یک الگوریتم. در مثال بالا، در صورت تمایل می‌توانستیم `SubArray` پاس دهیم  تا یک `Array`.

از قبل تخصیص دادن، می تواند کد شما را زشت‌تر کند، بنابراین ممکن است اندازه گیری عملکرد و برخی قضاوت ها لازم باشد. با این حال، برای توابع "برداری شده" (از نظر عناصر)، از syntax راحت `x. = f. (y)` می توان برای عملیات درجا با fused loops و بدون آرایه های موقتی استفاده کرد (برای تنظیم بردار توابع به dot syntaxمراجعه کنید).
    
</div>

## More dots: Fuse vectorized operations

Julia has a special [dot syntax](@ref man-vectorized) that converts
any scalar function into a "vectorized" function call, and any operator
into a "vectorized" operator, with the special property that nested
"dot calls" are *fusing*: they are combined at the syntax level into
a single loop, without allocating temporary arrays. If you use `.=` and
similar assignment operators, the result can also be stored in-place
in a pre-allocated array (see above).

In a linear-algebra context, this means that even though operations like
`vector + vector` and `vector * scalar` are defined, it can be advantageous
to instead use `vector .+ vector` and `vector .* scalar` because the
resulting loops can be fused with surrounding computations. For example,
consider the two functions:

```
julia> f(x) = 3x.^2 + 4x + 7x.^3;

julia> fdot(x) = @. 3x^2 + 4x + 7x^3 # equivalent to 3 .* x.^2 .+ 4 .* x .+ 7 .* x.^3;
```

Both `f` and `fdot` compute the same thing. However, `fdot`
(defined with the help of the [`@.`](@ref @__dot__) macro) is
significantly faster when applied to an array:

```julia
julia> x = rand(10^6);

julia> @time f(x);
  0.019049 seconds (16 allocations: 45.777 MiB, 18.59% gc time)

julia> @time fdot(x);
  0.002790 seconds (6 allocations: 7.630 MiB)

julia> @time f.(x);
  0.002626 seconds (8 allocations: 7.630 MiB)
```

That is, `fdot(x)` is ten times faster and allocates 1/6 the
memory of `f(x)`, because each `*` and `+` operation in `f(x)` allocates
a new temporary array and executes in a separate loop. (Of course,
if you just do `f.(x)` then it is as fast as `fdot(x)` in this
example, but in many contexts it is more convenient to just sprinkle
some dots in your expressions rather than defining a separate function
for each vectorized operation.)

## Consider using views for slices

In Julia, an array "slice" expression like `array[1:5, :]` creates
a copy of that data (except on the left-hand side of an assignment,
where `array[1:5, :] = ...` assigns in-place to that portion of `array`).
If you are doing many operations on the slice, this can be good for
performance because it is more efficient to work with a smaller
contiguous copy than it would be to index into the original array.
On the other hand, if you are just doing a few simple operations on
the slice, the cost of the allocation and copy operations can be
substantial.

An alternative is to create a "view" of the array, which is
an array object (a `SubArray`) that actually references the data
of the original array in-place, without making a copy. (If you
write to a view, it modifies the original array's data as well.)
This can be done for individual slices by calling `view`,
or more simply for a whole expression or block of code by putting
`@views` in front of that expression. For example:

```julia
julia> fcopy(x) = sum(x[2:end-1]);

julia> @views fview(x) = sum(x[2:end-1]);

julia> x = rand(10^6);

julia> @time fcopy(x);
  0.003051 seconds (3 allocations: 7.629 MB)

julia> @time fview(x);
  0.001020 seconds (1 allocation: 16 bytes)
```

Notice both the 3× speedup and the decreased memory allocation
of the `fview` version of the function.

## Copying data is not always bad

Arrays are stored contiguously in memory, lending themselves to CPU vectorization
and fewer memory accesses due to caching. These are the same reasons that it is recommended
to access arrays in column-major order (see above). Irregular access patterns and non-contiguous views
can drastically slow down computations on arrays because of non-sequential memory access.

Copying irregularly-accessed data into a contiguous array before operating on it can result
in a large speedup, such as in the example below. Here, a matrix and a vector are being accessed at
800,000 of their randomly-shuffled indices before being multiplied. Copying the views into
plain arrays speeds up the multiplication even with the cost of the copying operation.

```julia
julia> using Random

julia> x = randn(1_000_000);

julia> inds = shuffle(1:1_000_000)[1:800000];

julia> A = randn(50, 1_000_000);

julia> xtmp = zeros(800_000);

julia> Atmp = zeros(50, 800_000);

julia> @time sum(view(A, :, inds) * view(x, inds))
  0.412156 seconds (14 allocations: 960 bytes)
-4256.759568345458

julia> @time begin
           copyto!(xtmp, view(x, inds))
           copyto!(Atmp, view(A, :, inds))
           sum(Atmp * xtmp)
       end
  0.285923 seconds (14 allocations: 960 bytes)
-4256.759568345134
```

Provided there is enough memory for the copies, the cost of copying the view to an array is
far outweighed by the speed boost from doing the matrix multiplication on a contiguous array.

## Consider StaticArrays.jl for small fixed-size vector/matrix operations

If your application involves many small (`< 100` element) arrays of fixed sizes (i.e. the size is
known prior to execution), then you might want to consider using the [StaticArrays.jl package](https://github.com/JuliaArrays/StaticArrays.jl).
This package allows you to represent such arrays in a way that avoids unnecessary heap allocations and allows the compiler to
specialize code for the *size* of the array, e.g. by completely unrolling vector operations (eliminating the loops) and storing elements in CPU registers.

For example, if you are doing computations with 2d geometries, you might have many computations with 2-component vectors.  By
using the `SVector` type from StaticArrays.jl, you can use convenient vector notation and operations like `norm(3v - w)` on
vectors `v` and `w`, while allowing the compiler to unroll the code to a minimal computation equivalent to `@inbounds hypot(3v[1]-w[1], 3v[2]-w[2])`.

## Avoid string interpolation for I/O

When writing data to a file (or other I/O device), forming extra intermediate strings is a source
of overhead. Instead of:

```julia
println(file, "$a $b")
```

use:

```julia
println(file, a, " ", b)
```

The first version of the code forms a string, then writes it to the file, while the second version
writes values directly to the file. Also notice that in some cases string interpolation can be
harder to read. Consider:

```julia
println(file, "$(f(a))$(f(b))")
```

versus:

```julia
println(file, f(a), f(b))
```

## Optimize network I/O during parallel execution

When executing a remote function in parallel:

```julia
using Distributed

responses = Vector{Any}(undef, nworkers())
@sync begin
    for (idx, pid) in enumerate(workers())
        @async responses[idx] = remotecall_fetch(foo, pid, args...)
    end
end
```

is faster than:

```julia
using Distributed

refs = Vector{Any}(undef, nworkers())
for (idx, pid) in enumerate(workers())
    refs[idx] = @spawnat pid foo(args...)
end
responses = [fetch(r) for r in refs]
```

The former results in a single network round-trip to every worker, while the latter results in
two network calls - first by the `@spawnat` and the second due to the `fetch`
(or even a `wait`).
The `fetch`/`wait` is also being executed serially resulting in an overall poorer performance.

## Fix deprecation warnings

A deprecated function internally performs a lookup in order to print a relevant warning only once.
This extra lookup can cause a significant slowdown, so all uses of deprecated functions should
be modified as suggested by the warnings.

## Tweaks

These are some minor points that might help in tight inner loops.

  * Avoid unnecessary arrays. For example, instead of `sum([x,y,z])` use `x+y+z`.
  * Use `abs2(z)` instead of `abs(z)^2` for complex `z`. In general, try to rewrite
    code to use `abs2` instead of `abs` for complex arguments.
  * Use `div(x,y)` for truncating division of integers instead of `trunc(x/y)`, `fld(x,y)`
    instead of `floor(x/y)`, and `cld(x,y)` instead of `ceil(x/y)`.

## Performance Annotations

Sometimes you can enable better optimization by promising certain program properties.

  * Use `@inbounds` to eliminate array bounds checking within expressions. Be certain before doing
    this. If the subscripts are ever out of bounds, you may suffer crashes or silent corruption.
  * Use `@fastmath` to allow floating point optimizations that are correct for real numbers, but lead
    to differences for IEEE numbers. Be careful when doing this, as this may change numerical results.
    This corresponds to the `-ffast-math` option of clang.
  * Write `@simd` in front of `for` loops to promise that the iterations are independent and may be
    reordered.  Note that in many cases, Julia can automatically vectorize code without the `@simd` macro;
    it is only beneficial in cases where such a transformation would otherwise be illegal, including cases
    like allowing floating-point re-associativity and ignoring dependent memory accesses (`@simd ivdep`).
    Again, be very careful when asserting `@simd` as erroneously annotating a loop with dependent iterations
    may result in unexpected results. In particular, note that `setindex!` on some `AbstractArray` subtypes is
    inherently dependent upon iteration order. **This feature is experimental**
    and could change or disappear in future versions of Julia.

The common idiom of using 1:n to index into an AbstractArray is not safe if the Array uses unconventional indexing,
and may cause a segmentation fault if bounds checking is turned off. Use `LinearIndices(x)` or `eachindex(x)`
instead (see also [Arrays with custom indices](@ref man-custom-indices)).

```eval_rst

.. note::
    While `@simd` needs to be placed directly in front of an innermost `for` loop, both `@inbounds` and `@fastmath`
    can be applied to either single expressions or all the expressions that appear within nested blocks of code, e.g.,
    using `@inbounds begin` or `@inbounds for ...`.
```

Here is an example with both `@inbounds` and `@simd` markup (we here use `@noinline` to prevent
the optimizer from trying to be too clever and defeat our benchmark):

```julia
@noinline function inner(x, y)
    s = zero(eltype(x))
    for i=eachindex(x)
        @inbounds s += x[i]*y[i]
    end
    return s
end

@noinline function innersimd(x, y)
    s = zero(eltype(x))
    @simd for i = eachindex(x)
        @inbounds s += x[i] * y[i]
    end
    return s
end

function timeit(n, reps)
    x = rand(Float32, n)
    y = rand(Float32, n)
    s = zero(Float64)
    time = @elapsed for j in 1:reps
        s += inner(x, y)
    end
    println("GFlop/sec        = ", 2n*reps / time*1E-9)
    time = @elapsed for j in 1:reps
        s += innersimd(x, y)
    end
    println("GFlop/sec (SIMD) = ", 2n*reps / time*1E-9)
end

timeit(1000, 1000)
```

On a computer with a 2.4GHz Intel Core i5 processor, this produces:

```
GFlop/sec        = 1.9467069505224963
GFlop/sec (SIMD) = 17.578554163920018
```

(`GFlop/sec` measures the performance, and larger numbers are better.)

Here is an example with all three kinds of markup. This program first calculates the finite difference
of a one-dimensional array, and then evaluates the L2-norm of the result:

```julia
function init!(u::Vector)
    n = length(u)
    dx = 1.0 / (n-1)
    @fastmath @inbounds @simd for i in 1:n #by asserting that `u` is a `Vector` we can assume it has 1-based indexing
        u[i] = sin(2pi*dx*i)
    end
end

function deriv!(u::Vector, du)
    n = length(u)
    dx = 1.0 / (n-1)
    @fastmath @inbounds du[1] = (u[2] - u[1]) / dx
    @fastmath @inbounds @simd for i in 2:n-1
        du[i] = (u[i+1] - u[i-1]) / (2*dx)
    end
    @fastmath @inbounds du[n] = (u[n] - u[n-1]) / dx
end

function mynorm(u::Vector)
    n = length(u)
    T = eltype(u)
    s = zero(T)
    @fastmath @inbounds @simd for i in 1:n
        s += u[i]^2
    end
    @fastmath @inbounds return sqrt(s)
end

function main()
    n = 2000
    u = Vector{Float64}(undef, n)
    init!(u)
    du = similar(u)

    deriv!(u, du)
    nu = mynorm(du)

    @time for i in 1:10^6
        deriv!(u, du)
        nu = mynorm(du)
    end

    println(nu)
end

main()
```

On a computer with a 2.7 GHz Intel Core i7 processor, this produces:

```
$ julia wave.jl;
  1.207814709 seconds
4.443986180758249

$ julia --math-mode=ieee wave.jl;
  4.487083643 seconds
4.443986180758249
```

Here, the option `--math-mode=ieee` disables the `@fastmath` macro, so that we can compare results.

In this case, the speedup due to `@fastmath` is a factor of about 3.7. This is unusually large
– in general, the speedup will be smaller. (In this particular example, the working set of the
benchmark is small enough to fit into the L1 cache of the processor, so that memory access latency
does not play a role, and computing time is dominated by CPU usage. In many real world programs
this is not the case.) Also, in this case this optimization does not change the result – in
general, the result will be slightly different. In some cases, especially for numerically unstable
algorithms, the result can be very different.

The annotation `@fastmath` re-arranges floating point expressions, e.g. changing the order of
evaluation, or assuming that certain special cases (inf, nan) cannot occur. In this case (and
on this particular computer), the main difference is that the expression `1 / (2*dx)` in the function
`deriv` is hoisted out of the loop (i.e. calculated outside the loop), as if one had written
`idx = 1 / (2*dx)`. In the loop, the expression `... / (2*dx)` then becomes `... * idx`, which
is much faster to evaluate. Of course, both the actual optimization that is applied by the compiler
as well as the resulting speedup depend very much on the hardware. You can examine the change
in generated code by using Julia's `code_native` function.

Note that `@fastmath` also assumes that `NaN`s will not occur during the computation, which can lead to surprising behavior:

```julia
julia> f(x) = isnan(x);

julia> f(NaN)
true

julia> f_fast(x) = @fastmath isnan(x);

julia> f_fast(NaN)
false
```

## Treat Subnormal Numbers as Zeros

Subnormal numbers, formerly called [denormal numbers](https://en.wikipedia.org/wiki/Denormal_number),
are useful in many contexts, but incur a performance penalty on some hardware. A call `set_zero_subnormals(true)`
grants permission for floating-point operations to treat subnormal inputs or outputs as zeros,
which may improve performance on some hardware. A call `set_zero_subnormals(false)` enforces
strict IEEE behavior for subnormal numbers.

Below is an example where subnormals noticeably impact performance on some hardware:

```julia
function timestep(b::Vector{T}, a::Vector{T}, Δt::T) where T
    @assert length(a)==length(b)
    n = length(b)
    b[1] = 1                            # Boundary condition
    for i=2:n-1
        b[i] = a[i] + (a[i-1] - T(2)*a[i] + a[i+1]) * Δt
    end
    b[n] = 0                            # Boundary condition
end

function heatflow(a::Vector{T}, nstep::Integer) where T
    b = similar(a)
    for t=1:div(nstep,2)                # Assume nstep is even
        timestep(b,a,T(0.1))
        timestep(a,b,T(0.1))
    end
end

heatflow(zeros(Float32,10),2)           # Force compilation
for trial=1:6
    a = zeros(Float32,1000)
    set_zero_subnormals(iseven(trial))  # Odd trials use strict IEEE arithmetic
    @time heatflow(a,1000)
end
```

This gives an output similar to

```
  0.002202 seconds (1 allocation: 4.063 KiB)
  0.001502 seconds (1 allocation: 4.063 KiB)
  0.002139 seconds (1 allocation: 4.063 KiB)
  0.001454 seconds (1 allocation: 4.063 KiB)
  0.002115 seconds (1 allocation: 4.063 KiB)
  0.001455 seconds (1 allocation: 4.063 KiB)
```

Note how each even iteration is significantly faster.

This example generates many subnormal numbers because the values in `a` become an exponentially
decreasing curve, which slowly flattens out over time.

Treating subnormals as zeros should be used with caution, because doing so breaks some identities,
such as `x-y == 0` implies `x == y`:

```julia
julia> x = 3f-38; y = 2f-38;

julia> set_zero_subnormals(true); (x - y, x == y)
(0.0f0, false)

julia> set_zero_subnormals(false); (x - y, x == y)
(1.0000001f-38, false)
```

In some applications, an alternative to zeroing subnormal numbers is to inject a tiny bit of noise.
 For example, instead of initializing `a` with zeros, initialize it with:

```julia
a = rand(Float32,1000) * 1.f-9
```

## `@code_warntype`

The macro `@code_warntype` (or its function variant `code_warntype`) can sometimes
be helpful in diagnosing type-related problems. Here's an example:

```julia
julia> @noinline pos(x) = x < 0 ? 0 : x;

julia> function f(x)
           y = pos(x)
           return sin(y*x + 1)
       end;

julia> @code_warntype f(3.2)
Variables
  #self#::Core.Const(f)
  x::Float64
  y::UNION{FLOAT64, INT64}

Body::Float64
1 ─      (y = Main.pos(x))
│   %2 = (y * x)::Float64
│   %3 = (%2 + 1)::Float64
│   %4 = Main.sin(%3)::Float64
└──      return %4
```

Interpreting the output of `@code_warntype`, like that of its cousins `@code_lowered`,
`@code_typed`, `@code_llvm`, and `@code_native`, takes a little practice.
Your code is being presented in form that has been heavily digested on its way to generating
compiled machine code. Most of the expressions are annotated by a type, indicated by the `::T`
(where `T` might be `Float64`, for example). The most important characteristic of `@code_warntype`
is that non-concrete types are displayed in red; since this document is written in Markdown, which has no color,
in this document, red text is denoted by uppercase.

At the top, the inferred return type of the function is shown as `Body::Float64`.
The next lines represent the body of `f` in Julia's SSA IR form.
The numbered boxes are labels and represent targets for jumps (via `goto`) in your code.
Looking at the body, you can see that the first thing that happens is that `pos` is called and the
return value has been inferred as the `Union` type `UNION{FLOAT64, INT64}` shown in uppercase since
it is a non-concrete type. This means that we cannot know the exact return type of `pos` based on the
input types. However, the result of `y*x`is a `Float64` no matter if `y` is a `Float64` or `Int64`
The net result is that `f(x::Float64)` will not be type-unstable
in its output, even if some of the intermediate computations are type-unstable.

How you use this information is up to you. Obviously, it would be far and away best to fix `pos`
to be type-stable: if you did so, all of the variables in `f` would be concrete, and its performance
would be optimal. However, there are circumstances where this kind of *ephemeral* type instability
might not matter too much: for example, if `pos` is never used in isolation, the fact that `f`'s
output is type-stable (for `Float64` inputs) will shield later code from the propagating
effects of type instability. This is particularly relevant in cases where fixing the type instability
is difficult or impossible. In such cases, the tips above (e.g., adding type annotations and/or
breaking up functions) are your best tools to contain the "damage" from type instability.
Also, note that even Julia Base has functions that are type unstable.
For example, the function `findfirst` returns the index into an array where a key is found,
or `nothing` if it is not found, a clear type instability. In order to make it easier to find the
type instabilities that are likely to be important, `Union`s containing either `missing` or `nothing`
are color highlighted in yellow, instead of red.

The following examples may help you interpret expressions marked as containing non-leaf types:

  * Function body starting with `Body::UNION{T1,T2})`
      * Interpretation: function with unstable return type
      * Suggestion: make the return value type-stable, even if you have to annotate it

  * `invoke Main.g(%%x::Int64)::UNION{FLOAT64, INT64}`
      * Interpretation: call to a type-unstable function `g`.
      * Suggestion: fix the function, or if necessary annotate the return value

  * `invoke Base.getindex(%%x::Array{Any,1}, 1::Int64)::ANY`
      * Interpretation: accessing elements of poorly-typed arrays
      * Suggestion: use arrays with better-defined types, or if necessary annotate the type of individual
        element accesses

  * `Base.getfield(%%x, :(:data))::ARRAY{FLOAT64,N} WHERE N`
      * Interpretation: getting a field that is of non-leaf type. In this case, `ArrayContainer` had a
        field `data::Array{T}`. But `Array` needs the dimension `N`, too, to be a concrete type.
      * Suggestion: use concrete types like `Array{T,3}` or `Array{T,N}`, where `N` is now a parameter
        of `ArrayContainer`

## Performance of captured variable

Consider the following example that defines an inner function:
```julia
function abmult(r::Int)
    if r < 0
        r = -r
    end
    f = x -> x * r
    return f
end
```

Function `abmult` returns a function `f` that multiplies its argument by
the absolute value of `r`. The inner function assigned to `f` is called a
"closure". Inner functions are also used by the
language for `do`-blocks and for generator expressions.

This style of code presents performance challenges for the language.
The parser, when translating it into lower-level instructions,
substantially reorganizes the above code by extracting the
inner function to a separate code block.  "Captured" variables such as `r`
that are shared by inner functions and their enclosing scope are
also extracted into a heap-allocated "box" accessible to both inner and
outer functions because the language specifies that `r` in the
inner scope must be identical to `r` in the outer scope even after the
outer scope (or another inner function) modifies `r`.

The discussion in the preceding paragraph referred to the "parser", that is, the phase
of compilation that takes place when the module containing `abmult` is first loaded,
as opposed to the later phase when it is first invoked. The parser does not "know" that
`Int` is a fixed type, or that the statement `r = -r` transforms an `Int` to another `Int`.
The magic of type inference takes place in the later phase of compilation.

Thus, the parser does not know that `r` has a fixed type (`Int`).
nor that `r` does not change value once the inner function is created (so that
the box is unneeded).  Therefore, the parser emits code for
box that holds an object with an abstract type such as `Any`, which
requires run-time type dispatch for each occurrence of `r`.  This can be
verified by applying `@code_warntype` to the above function.  Both the boxing
and the run-time type dispatch can cause loss of performance.

If captured variables are used in a performance-critical section of the code,
then the following tips help ensure that their use is performant. First, if
it is known that a captured variable does not change its type, then this can
be declared explicitly with a type annotation (on the variable, not the
right-hand side):
```julia
function abmult2(r0::Int)
    r::Int = r0
    if r < 0
        r = -r
    end
    f = x -> x * r
    return f
end
```
The type annotation partially recovers lost performance due to capturing because
the parser can associate a concrete type to the object in the box.
Going further, if the captured variable does not need to be boxed at all (because it
will not be reassigned after the closure is created), this can be indicated
with `let` blocks as follows.
```julia
function abmult3(r::Int)
    if r < 0
        r = -r
    end
    f = let r = r
            x -> x * r
    end
    return f
end
```
The `let` block creates a new variable `r` whose scope is only the
inner function. The second technique recovers full language performance
in the presence of captured variables. Note that this is a rapidly
evolving aspect of the compiler, and it is likely that future releases
will not require this degree of programmer annotation to attain performance.
In the mean time, some user-contributed packages like
[FastClosures](https://github.com/c42f/FastClosures.jl) automate the
insertion of `let` statements as in `abmult3`.

## Checking for equality with a singleton

When checking if a value is equal to some singleton it can be
better for performance to check for identicality (`===`) instead of
equality (`==`). The same advice applies to using `!==` over `!=`.
These type of checks frequently occur e.g. when implementing the iteration
protocol and checking if `nothing` is returned from `iterate`.
