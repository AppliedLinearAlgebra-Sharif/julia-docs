# Frequently Asked Questions(سوالات پرتکرار)

## General

### Is Julia named after someone or something?(ایا اسم جولیا برگرفته از شخص یا چیزی است؟)

خیر

### Why don't you compile Matlab/Python/R/… code to Julia?(چرا کد متلب و ار و پایتون روی جولیا اجرا نمی شود؟)
<div dir="auto">
 از انجایی که بسیاری از مردم با سینتکس بقیه زبان های داینامیک برناهم نویسی اشنا هستند و بسیاری از کد ها به این زبانها نوشته شده است،طبیعی است که تعجب کنید چرا سیتکس زبان هایی مثل 
 mathlab
 یا
 python
 را نگرفتیم و با 
 backend
 جولیا به اصطلاح 
 transpile
 کنیم تا تمام مزایای عملکردی جولیا را داشته باشیم بدون اینکه نیاز باشد برنامه نویس یک زبان جدید یاد بگیرد .
 ساده است ؟ بله؟
 <br>
 مسئله اولیه این است که هیچ چیز خاصی درباره  کامپایلر جولیا وجو ندارد ما از یک کامپایلر 
 LLVM
 بدون هیچ چیز اضافه ای که برنامه نویس های دیگر درباره ان نمیدانند استفاده نکردیم .
 قطعا کامپایلر جولیا ار خیلی جهات به مراتب ساده تر از زبانهایی مثل 
 PyPy or LuaJIT
 است.
 مزایای
 عملکرد جولیا 
 برگرفته از قسمت 
 front-end 
 ان است.
 معنا شناسی 
 (semantics)
 ان این اجازه را میدهد که یک برنامه نویس خوب این اجازه را به کامپایلز بدهد که کدهای بهتری از لحظ موثر بودن و لایه های مموری تولید کند.
 اگر شما تا به حال کامپایل کردن کد های 
 mathlab
 یا
 python
 را در جولیا امتحان کرده باشید،کامپایلر زبان محدود میشود و نمیتواند کد های بهتری نسبت به کامپابلر همان زبان پایتون یا جنریت و تولید کند.
 (و شاید هم بدتر)
 نکته کلیدی عملکرد فهمیدن کد این است که چرا کامپابلر های پایتون  مثل 
  Numba
 یا
 Pythran
 تنها تلاش میکنند که زیرمجموعه کوچکی از زبان را اپتیمایز کنند مثل 
 عمل هایی بر روی ارایه و اعداد در 
 numpy
 و برای همین زیرمجموعه هم خیلی کمتر از برنامه نویسان جولیا کار را انجام میدهند .
 افرادی که روی این پروژه ها کار میکنند بسیار باهوش هستند و 
 کارهای شگفت انگیزی انجام داده اند .
 اما مقاوم سازی مجدد یک کامپایلر بر روی زبانی که برای تفسیر
 (interpreting)
 طراحی شده است ، یک مشکل بسیار دشوار است.
 <br>
 مزیت جولیا نسبت  این است که این عملکرد خوب و اپتیمایز بر روی یک زیرمجموعه تایپ ها و عملگر های 
 “built-in”
 محدود نشده است و برنامه نویس میتواند یک یک برنامه بسطح بالا با تایپ عمومی بنویسد که بر روی 
 انواع مختلف 
 type-generic code
 توسط کاربر تعریف شده درحلیکه هنوز سریع و از نظر حتفظه کم حافظه است.
 تایپ ها در زبانهایی مثل پایتون به سادگی اطلاعات زیادی  با این توان در اختیار کامپایلر قرار نمیدهند 
 .
 و تا زمانی که از انها برای 
 front-end
 جولیا استفاده میکنید  از این ویژگی نیمتوانید بهر ببرید.
 <br>
 به دلایل مشابه 
automated translation
 نیز برای جولیا یک کد ناخوانا و ارام به نسبت و نه اصطلاحی 
 (منظور این است که مختص به جولیا باشد.)
 تولید می کند که نقطه شروعی برای یک زبان
 (native)
مثل جولیا
 نیست.
 <br>
 در طورف دیگر ویژگی 
*interoperability*
 یک زبان  بسیار بدردبخور است .
 ما می خواهیم از کد با کیفیت بالا موجود به زبانهای دیگر از جولیا (و بالعکس) بهره برداری کنیم!
بهترین راه برای اینکار یک 
 transpiler
 نیست بلکه از طریق ویژگی هایی است که در خود زبان تعبیه شده 
 (easy inter-language calling facilities)
 ما به سختی روی این موضوع کار کردیم  از 
 <span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">ccall</a></span>
 داخلی برای ارتباط  با زبان 
 C
 و کتابخانه های 
Fortran
 به
پکیج های 
 <span><a href="https://github.com/JuliaInterop)/">juliaInterop</a></span>
که زبان جولیا را به پایتون و متلب و سی پلاس پلاس و غیره مرتبط میکنند.
 
</div>

## Sessions and the REPL

### How do I delete an object in memory?(چگونه یک شی را از مموری پاک کنیم ؟)
<div dir="auto">
 جولیا مانند 
 mathlab
 یک تابع 
 clear
 ندارد . 
 وقتی که یک اسم در یک 
 session
 جولیا تعریف شد.
 (
 دقیق تر در ماژول 
 main
 )
 همیشه انجا وجود دارد.
<br>
اگر استفاده از مموری جز نگرانی های شماست ،میتوانید همیشه اشیای خود را با ان هایی که فضا یا مموری کمتری می گیرند جا به جا کنید.
 <br>
 برای مثال اگر 
 A
 یک ارایه با سایز اردر گیگابایت باشد و دیگر در برنامه نیازی به ان نباشد،میتوانید با دستور 
   A = nothing  
 مموری را خالی کنید.
 و ان فضا دفعه بعدی که 
 garbage collector
 اجرا شد فضا را خالی میکند.
 و همینطور میتوانید با تابع زیر ان  را مجبور به اجرا کنید .
 <br>
 <span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">GC.gc()</a></span>
 که در 
 Base
 قرار دارد 
 همین طور استفاده از 
 َA
 به احتمال زیاد در این حالت منجر به  خطا میشود.
 زیرا بیشتر توابع بر روی تایپ 
 nothing 
 تعریف نشده اند .
 
</div>

### How can I modify the declaration of a type in my session?(چگونه تایپ را عوض میکنم؟)
<div dir="auto">
 شاید شما نوعی را تعریف کرده باشید و سپس متوجه شوید که باید یک قسمت جدید اضافه کنید.
 اگر اینکار را بر روی 
 REPL 
 امتحان کرده باشید ، خطای زیر را دریافت می کنید
 :
</div>


```
ERROR: invalid redefinition of constant MyType
```
<div dir="auto">
 تایپ ها در ماژول 
 main
 نمیتوانند دوباره تعریف شوند.
 <br>
 اگرچه این کار در هنگام نوشتن کد جدید ناخوشایند است ، اما یک راه حل عالی وجود دارد.
ماژول ها میتوانند با دوباره تعریف کردنشان جا به جا شوند.
 پس اگر شما کد خود را دورن یک ماژول بپیچید
 به اصطلاح
 wrap
 کنید میتوانید ثابت ها و تایپ ها را دوباره تعریف کنید.
، 
 نمی توانید نام نوع را در "Main" وارد کنید و سپس انتظار داشته باشید
برای اینکه بتوانید در آنجا دوباره تعریف کنید ، اما می توانید از نام ماژول برای حل محدوده استفاده کنید. در دیگر
سخن  ، در حالی که شما ممکن است از گردش کار مانند این استفاده کنید:
</div>

```julia
include("mynewcode.jl")              # this defines a module MyModule
obj1 = MyModule.ObjConstructor(a, b)
obj2 = MyModule.somefunction(obj1)
# Got an error. Change something in "mynewcode.jl"
include("mynewcode.jl")              # reload the module
obj1 = MyModule.ObjConstructor(a, b) # old objects are no longer valid, must reconstruct
obj2 = MyModule.somefunction(obj1)   # this time it worked!
obj3 = MyModule.someotherfunction(obj2, c)
...
```

## [Scripting]

### How do I check if the current file is being run as the main script?(چگونه چک کنم این فایل دارد روی جریان اصلی اجرا میشود یا نه؟)
<div dir="auto">
 زمانی که یک فایل با استفاده از 
 julia file.jl
 بر روی 
 main script
 اجرا میشود،
یک نفر ممکن است بخواهد عملکردهای دیگری اضافه بر سازمان مانند کنترل ارگومتن در
 command line
 را فعال کند .
 روشی برای  تعیین اینکه یک فایل به این صورت اجرا شده است ، این است که ایا
</div>
 
`abspath(PROGRAM_FILE) == @__FILE__` is `true`.

### [How do I catch CTRL-C in a script?]
<div dir="auto">
 اجرا کردن یک اسکریپت جولیا با استفاده از 
 julia file.jl
 اکسپشن 
 <span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">InterruptExeption</a></span>
 را پرت نمیکند.
زمانی که شما این پروسه را با زدن 
 CTRL-C
(SIGINT)
 تمام میکنید.
 برای اجرا کردن کد قبل از بستن یک اسکریپت جولیا که ممکن است با 
 CTRL-C
 ویا بدون ان ایجاد شده باشد،
از 
<span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">atexit</a></span>
 استفاده کنید همین طور ، میتوانید از
julia -e 'include(popfirst!(ARGS))
file.jl
برای اجرا کردن یک اسکریپت زمانی که میتوانید اکسپشن 
 <span><a href="https://docs.julialang.org/en/v1/manual/methods/)/">InterruptExeption</a></span>
 را در بلاک 
 try
 catch 
 کنید.
</div>

### How do I pass options to `julia` using `#!/usr/bin/env`?
<div dir="auto">
 پاس دادن اپشن ها به 
 `julia`
در اصطلاح 
 shebang
 گفته میشود.
#!/usr/bin/env julia --startup-file=no
 ممکن است در بعضی از پلتفرم ها مثل لینوکس کار نکند.
 که این به این خاطر است که ارگومانی که در 
shebang
 کار 
 parsing
 بر روی ان انجام شده است  نسبت به پلتفرم مستقل نیست و به ان بستگی دارد.
 یک راه قابل اطمینان برای پاس دادن اپشن ها به 
`julia`
 در یک اسکریپت قابل اجرا میتواند این باشد که اسکریپت را مانند 
 bash 
 اسکریت شروع کنند و از 
 exec
 برای انتقال پروسه به 
 julia
 استفاده کنند.
</div>

```julia
#!/bin/bash
#=
exec julia --color=yes --startup-file=no "${BASH_SOURCE[0]}" "$@"
=#

@show ARGS  # put any Julia code here
```

<div dir="auto">
 در مثال بالا ، کدی که بین 
 #=
 و
 =#
 بود به صورت 
 `bash`
script
 اجرا شد.
 جولیا به این بخش کاری ندارد تا وقتیی که یک کامنت چند خطه برای جولیاست.
 کد های جولیا بعد از علامت 
   =#  
 توسط
 bash
 ایگنور میشوند (ران نمیشوند)
 تا زمانی که در
 parse
 کردن فایل متوقف شود زمانی که 
 state
 ما یعنی 
 exec
 میرسد.
 <br>
 توجه کنید برای 
</div>


    [catch CTRL-C](@ref catch-ctrl-c)
    
    
  <div dir="auto">
    بر روی اسکریپت میتوانید 
</div>
   
    ```julia
    #!/bin/bash
    #=
    exec julia --color=yes --startup-file=no -e 'include(popfirst!(ARGS))' \
        "${BASH_SOURCE[0]}" "$@"
    =#

    @show ARGS  # put any Julia code here
    ```
    instead. Note that with this strategy [`PROGRAM_FILE`](@ref) will not be set.

## Functions(توابع)

### I passed an argument `x` to a function, modified it inside that function, but on the outside, the variable `x` is still unchanged. Why?(چرا من یک متغیر را درون تابع تغییر دادم ولی بیرون ان تغییر نکرده است؟)

<div dir="auto">
 فرض کنید چنین تابعی را صدا کرده اید
 :
</div>

```jldoctest
julia> x = 10
10

julia> function change_value!(y)
           y = 17
       end
change_value! (generic function with 1 method)

julia> change_value!(x)
17

julia> x # x is unchanged!
10
```

<div dir="auto">
 در جولیا،
مقداری که به یک متغیر نسبت داده شده یا 
 binding of a variable
 x
 نمیتواند در اثر پاس دادن به یک ارگومان تابع تغییر کند .
 وقتی تابع
  hange_value!(x)
 در مثال بالا را صدا می زنیم،متغیر 
 y
 یک متغیر است که به سادگی ساخته شده است .
 که اول در تابع یک مقدار به مقدار 
 x
 می دهد  مقدار 10 
 سپس 
 y
 مقدار 17 میگیرد.
 هنگامی که متغیر 
 x
 در لایه خارجی هنوز دست نخورده است.
 <br>
 درحالیکه،اگر 
 x
 به یک شی از ارایه 
 assign
 میشد 
 (یا هر تایپ 
 mutable(
 از داخل تابع ،نمیتوانید اشاره گر  
 x
 را عوض کنید یا به اصطلاح 
 unbind
 اش کنید ولی میتوانید مقدارش را عوض کنید.
 برای مثال
 :
</div>

```jldoctest
julia> x = [1,2,3]
3-element Vector{Int64}:
 1
 2
 3

julia> function change_array!(A)
           A[1] = 5
       end
change_array! (generic function with 1 method)

julia> change_array!(x)
5

julia> x
3-element Vector{Int64}:
 5
 2
 3
```

<div dir="auto">
 اینجا ما یک تابع 
 change_array!
 ساختیم که به خانه اول ارایه مقدار 
 '5'
 را نسبت میدهد.
 دقت کنید بعد از فراخوانی تابع،
 x
 هنوز به همان ارایه اشاره میکند.
 ولی مقادیر ارایه فرق کرده است .
 متغیر های
 A
 و
 x
 دو اشاره گر متمایز بودند که به یک ارایه تغییر پیدا کرده اشاره میکردند.
</div>

### Can I use `using` or `import` inside a function?(ایا میتوانیم از این دو لفظ درون  تابع استفاده کنیم؟)

<div dir="auto">
 نه ! ما اجازه نداریم که از کلیدواژه های 
 using
 یا
 import
 درون تابع استفاده کنیم.
 اگر میخواهید که یک ماژول را ایمپورت کنید ولی فقط از سمبل های درون  یک تابع مشخص یا مجموعه ای از تابع ها استفاده کنید.
 دو تا راه دارید
 :
 <br>
 <br>
 1.از ایمپورت استفاده کنید:
</div>

   ```julia
   import Foo
   function bar(...)
       # ... refer to Foo symbols via Foo.baz ...
   end
   ```
<div dir="auto">
 این کد ماژول 
 Foo
 را لود میکند و یک متغیر 
 Foo
 را تعریف میکند .
 که به ماژول برمیگردد  ولی هنوز هیچکدام از سیمبل های دیگر از ماژول را به 
 namespace
 فعلی ایمپورت نمیکند.
 شما با  اسم های واجدشرایط 
 qualified names `Foo.bar`
 به 
 Foo
 اشاره می کنید.
 <br>
 2.تابع خود را در یک ماژول قرار دهید:
</div>

   ```julia
   module Bar
   export bar
   using Foo
   function bar(...)
       # ... refer to Foo.baz as simply baz ....
   end
   end
   using Bar
   ```

<div dir="auto">
 این کد تمام 
 symbol
 ها را از
 Foo
 ایمپورت میکند  ولی فقط درون ماژول 
 Bar.
</div>

### What does the `...` operator do?

#### The two uses of the `...` operator: slurping and splatting
<div dir="auto">
 بسیاری از افرادی که به تازگی با جولیا کد میزنند،در استفاده از عملگر 
 ...
 گیج میشوند.
 یکی از دلایلی که این عملگر پیچیده است این است که با توجه به کانتکس مفهوم متفاوتی دارد.
</div>

#### `...` combines many arguments into one argument in function definitions
<div dir="auto">
 برخلاف استفاده عملگر
 ...
 که اجازه میدهد هرچقدر ارکومان که خواستیم ورودی بدهیم،همان عملگر همچنین برای ایجاد یک تابع که فقط یک ارگومان داردکه باید از سایر ارگومان ها جدا باشدوقتی در کانتکستفراخوانی تابع از ان صحبت می شود.
 این استفاده از این عملگر 
 slurping
 نام دارد
 :
</div>

```jldoctest
julia> function printargs(args...)
           println(typeof(args))
           for (i, arg) in enumerate(args)
               println("Arg #$i = $arg")
           end
       end
printargs (generic function with 1 method)

julia> printargs(1, 2, 3)
Tuple{Int64, Int64, Int64}
Arg #1 = 1
Arg #2 = 2
Arg #3 = 3
```
<div dir="auto">
 اگر جولیا یک زبان بود که با استفاده از کاراکتر های
 ASCII
 حرف های بیشتری میساخت،
عملگر 
 slurping
 باید به جای 
 ...
 به صورت 
<-...
 نوشته میشد.
</div>

#### `...` splits one argument into many different arguments in function calls(جدا کردن یک ارگومان از دیگر ارگومان ها در هنگام صدا کردن ان )

<div dir="auto">
 برخلاف استفاده عملگر
 ...
 که اجازه میدهد هرچقدر ارکومان که خواستیم ورودی بدهیم،همان عملگر همچنین برای ایجاد یک تابع که فقط یک ارگومان داردکه باید از سایر ارگومان ها جدا باشدوقتی در کانتکستفراخوانی تابع از ان صحبت می شود.
 این استفاده از این عملگر 
 splatting
 نام دارد
 :
 
</div>

```jldoctest
julia> function threeargs(a, b, c)
           println("a = $a::$(typeof(a))")
           println("b = $b::$(typeof(b))")
           println("c = $c::$(typeof(c))")
       end
threeargs (generic function with 1 method)

julia> x = [1, 2, 3]
3-element Vector{Int64}:
 1
 2
 3

julia> threeargs(x...)
a = 1::Int64
b = 2::Int64
c = 3::Int64
```
<div dir="auto">
 اگر جولیا یک زبان بود که با استفاده از کاراکتر های
 ASCII
 حرف های بیشتری میساخت،
عملگر 
 splatting
 باید به جای 
 ...
 به صورت 
 ...->
 نوشته میشد.
</div>

### What is the return value of an assignment?
<div dir="auto">
 عملگر 
 =
 همیشه نتیجه محاسبه  سمت راست خود را برمیگرداند
 :
</div>

```jldoctest
julia> function threeint()
           x::Int = 3.0
           x # returns variable x
       end
threeint (generic function with 1 method)

julia> function threefloat()
           x::Int = 3.0 # returns 3.0
       end
threefloat (generic function with 1 method)

julia> threeint()
3

julia> threefloat()
3.0
```
<div dir="auto">
 و به طور مشابه 
 :
</div>


```jldoctest
julia> function threetup()
           x, y = [3, 3]
           x, y # returns a tuple
       end
threetup (generic function with 1 method)

julia> function threearr()
           x, y = [3, 3] # returns an array
       end
threearr (generic function with 1 method)

julia> threetup()
(3, 3)

julia> threearr()
2-element Vector{Int64}:
 3
 3
```

## Types, type declarations, and constructors

### [What does "type-stable" mean?]

It means that the type of the output is predictable from the types of the inputs.  In particular,
it means that the type of the output cannot vary depending on the *values* of the inputs. The
following code is *not* type-stable:

```jldoctest
julia> function unstable(flag::Bool)
           if flag
               return 1
           else
               return 1.0
           end
       end
unstable (generic function with 1 method)
```

It returns either an `Int` or a [`Float64`](@ref) depending on the value of its argument.
Since Julia can't predict the return type of this function at compile-time, any computation
that uses it must be able to cope with values of both types, which makes it hard to produce
fast machine code.

### [Why does Julia give a `DomainError` for certain seemingly-sensible operations?]

Certain operations make mathematical sense but result in errors:

```jldoctest
julia> sqrt(-2.0)
ERROR: DomainError with -2.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]
```

This behavior is an inconvenient consequence of the requirement for type-stability.  In the case
of [`sqrt`](@ref), most users want `sqrt(2.0)` to give a real number, and would be unhappy if
it produced the complex number `1.4142135623730951 + 0.0im`.  One could write the [`sqrt`](@ref)
function to switch to a complex-valued output only when passed a negative number (which is what
[`sqrt`](@ref) does in some other languages), but then the result would not be [type-stable](@ref man-type-stability)
and the [`sqrt`](@ref) function would have poor performance.

In these and other cases, you can get the result you want by choosing an *input type* that conveys
your willingness to accept an *output type* in which the result can be represented:

```jldoctest
julia> sqrt(-2.0+0im)
0.0 + 1.4142135623730951im
```

### How can I constrain or compute type parameters?

The parameters of a [parametric type](@ref Parametric-Types) can hold either
types or bits values, and the type itself chooses how it makes use of these parameters.
For example, `Array{Float64, 2}` is parameterized by the type `Float64` to express its
element type and the integer value `2` to express its number of dimensions.  When
defining your own parametric type, you can use subtype constraints to declare that a
certain parameter must be a subtype ([`<:`](@ref)) of some abstract type or a previous
type parameter.  There is not, however, a dedicated syntax to declare that a parameter
must be a _value_ of a given type — that is, you cannot directly declare that a
dimensionality-like parameter [`isa`](@ref) `Int` within the `struct` definition, for
example.  Similarly, you cannot do computations (including simple things like addition
or subtraction) on type parameters.  Instead, these sorts of constraints and
relationships may be expressed through additional type parameters that are computed
and enforced within the type's [constructors](@ref man-constructors).

As an example, consider
```julia
struct ConstrainedType{T,N,N+1} # NOTE: INVALID SYNTAX
    A::Array{T,N}
    B::Array{T,N+1}
end
```
where the user would like to enforce that the third type parameter is always the second plus one. This can be implemented with an explicit type parameter that is checked by an [inner constructor method](@ref man-inner-constructor-methods) (where it can be combined with other checks):
```julia
struct ConstrainedType{T,N,M}
    A::Array{T,N}
    B::Array{T,M}
    function ConstrainedType(A::Array{T,N}, B::Array{T,M}) where {T,N,M}
        N + 1 == M || throw(ArgumentError("second argument should have one more axis" ))
        new{T,N,M}(A, B)
    end
end
```
This check is usually *costless*, as the compiler can elide the check for valid concrete types. If the second argument is also computed, it may be advantageous to provide an [outer constructor method](@ref man-outer-constructor-methods) that performs this calculation:
```julia
ConstrainedType(A) = ConstrainedType(A, compute_B(A))
```

### [Why does Julia use native machine integer arithmetic?]

Julia uses machine arithmetic for integer computations. This means that the range of `Int` values
is bounded and wraps around at either end so that adding, subtracting and multiplying integers
can overflow or underflow, leading to some results that can be unsettling at first:

```jldoctest
julia> x = typemax(Int)
9223372036854775807

julia> y = x+1
-9223372036854775808

julia> z = -y
-9223372036854775808

julia> 2*z
0
```

Clearly, this is far from the way mathematical integers behave, and you might think it less than
ideal for a high-level programming language to expose this to the user. For numerical work where
efficiency and transparency are at a premium, however, the alternatives are worse.

One alternative to consider would be to check each integer operation for overflow and promote
results to bigger integer types such as [`Int128`](@ref) or [`BigInt`](@ref) in the case of overflow.
Unfortunately, this introduces major overhead on every integer operation (think incrementing a
loop counter) – it requires emitting code to perform run-time overflow checks after arithmetic
instructions and branches to handle potential overflows. Worse still, this would cause every computation
involving integers to be type-unstable. As we mentioned above, [type-stability is crucial](@ref man-type-stability)
for effective generation of efficient code. If you can't count on the results of integer operations
being integers, it's impossible to generate fast, simple code the way C and Fortran compilers
do.

A variation on this approach, which avoids the appearance of type instability is to merge the
`Int` and [`BigInt`](@ref) types into a single hybrid integer type, that internally changes representation
when a result no longer fits into the size of a machine integer. While this superficially avoids
type-instability at the level of Julia code, it just sweeps the problem under the rug by foisting
all of the same difficulties onto the C code implementing this hybrid integer type. This approach
*can* be made to work and can even be made quite fast in many cases, but has several drawbacks.
One problem is that the in-memory representation of integers and arrays of integers no longer
match the natural representation used by C, Fortran and other languages with native machine integers.
Thus, to interoperate with those languages, we would ultimately need to introduce native integer
types anyway. Any unbounded representation of integers cannot have a fixed number of bits, and
thus cannot be stored inline in an array with fixed-size slots – large integer values will always
require separate heap-allocated storage. And of course, no matter how clever a hybrid integer
implementation one uses, there are always performance traps – situations where performance degrades
unexpectedly. Complex representation, lack of interoperability with C and Fortran, the inability
to represent integer arrays without additional heap storage, and unpredictable performance characteristics
make even the cleverest hybrid integer implementations a poor choice for high-performance numerical
work.

An alternative to using hybrid integers or promoting to BigInts is to use saturating integer arithmetic,
where adding to the largest integer value leaves it unchanged and likewise for subtracting from
the smallest integer value. This is precisely what Matlab™ does:

```
>> int64(9223372036854775807)

ans =

  9223372036854775807

>> int64(9223372036854775807) + 1

ans =

  9223372036854775807

>> int64(-9223372036854775808)

ans =

 -9223372036854775808

>> int64(-9223372036854775808) - 1

ans =

 -9223372036854775808
```

At first blush, this seems reasonable enough since 9223372036854775807 is much closer to 9223372036854775808
than -9223372036854775808 is and integers are still represented with a fixed size in a natural
way that is compatible with C and Fortran. Saturated integer arithmetic, however, is deeply problematic.
The first and most obvious issue is that this is not the way machine integer arithmetic works,
so implementing saturated operations requires emitting instructions after each machine integer
operation to check for underflow or overflow and replace the result with [`typemin(Int)`](@ref)
or [`typemax(Int)`](@ref) as appropriate. This alone expands each integer operation from a single,
fast instruction into half a dozen instructions, probably including branches. Ouch. But it gets
worse – saturating integer arithmetic isn't associative. Consider this Matlab computation:

```
>> n = int64(2)^62
4611686018427387904

>> n + (n - 1)
9223372036854775807

>> (n + n) - 1
9223372036854775806
```

This makes it hard to write many basic integer algorithms since a lot of common techniques depend
on the fact that machine addition with overflow *is* associative. Consider finding the midpoint
between integer values `lo` and `hi` in Julia using the expression `(lo + hi) >>> 1`:

```jldoctest
julia> n = 2^62
4611686018427387904

julia> (n + 2n) >>> 1
6917529027641081856
```

See? No problem. That's the correct midpoint between 2^62 and 2^63, despite the fact that `n + 2n`
is -4611686018427387904. Now try it in Matlab:

```
>> (n + 2*n)/2

ans =

  4611686018427387904
```

Oops. Adding a `>>>` operator to Matlab wouldn't help, because saturation that occurs when adding
`n` and `2n` has already destroyed the information necessary to compute the correct midpoint.

Not only is lack of associativity unfortunate for programmers who cannot rely it for techniques
like this, but it also defeats almost anything compilers might want to do to optimize integer
arithmetic. For example, since Julia integers use normal machine integer arithmetic, LLVM is free
to aggressively optimize simple little functions like `f(k) = 5k-1`. The machine code for this
function is just this:

```julia-repl
julia> code_native(f, Tuple{Int})
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 1
  leaq  -1(%rdi,%rdi,4), %rax
  popq  %rbp
  retq
  nopl  (%rax,%rax)
```

The actual body of the function is a single `leaq` instruction, which computes the integer multiply
and add at once. This is even more beneficial when `f` gets inlined into another function:

```julia-repl
julia> function g(k, n)
           for i = 1:n
               k = f(k)
           end
           return k
       end
g (generic function with 1 methods)

julia> code_native(g, Tuple{Int,Int})
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 2
  testq %rsi, %rsi
  jle L26
  nopl  (%rax)
Source line: 3
L16:
  leaq  -1(%rdi,%rdi,4), %rdi
Source line: 2
  decq  %rsi
  jne L16
Source line: 5
L26:
  movq  %rdi, %rax
  popq  %rbp
  retq
  nop
```

Since the call to `f` gets inlined, the loop body ends up being just a single `leaq` instruction.
Next, consider what happens if we make the number of loop iterations fixed:

```julia-repl
julia> function g(k)
           for i = 1:10
               k = f(k)
           end
           return k
       end
g (generic function with 2 methods)

julia> code_native(g,(Int,))
  .text
Filename: none
  pushq %rbp
  movq  %rsp, %rbp
Source line: 3
  imulq $9765625, %rdi, %rax    # imm = 0x9502F9
  addq  $-2441406, %rax         # imm = 0xFFDABF42
Source line: 5
  popq  %rbp
  retq
  nopw  %cs:(%rax,%rax)
```

Because the compiler knows that integer addition and multiplication are associative and that multiplication
distributes over addition – neither of which is true of saturating arithmetic – it can optimize
the entire loop down to just a multiply and an add. Saturated arithmetic completely defeats this
kind of optimization since associativity and distributivity can fail at each loop iteration, causing
different outcomes depending on which iteration the failure occurs in. The compiler can unroll
the loop, but it cannot algebraically reduce multiple operations into fewer equivalent operations.

The most reasonable alternative to having integer arithmetic silently overflow is to do checked
arithmetic everywhere, raising errors when adds, subtracts, and multiplies overflow, producing
values that are not value-correct. In this [blog post](https://danluu.com/integer-overflow/), Dan
Luu analyzes this and finds that rather than the trivial cost that this approach should in theory
have, it ends up having a substantial cost due to compilers (LLVM and GCC) not gracefully optimizing
around the added overflow checks. If this improves in the future, we could consider defaulting
to checked integer arithmetic in Julia, but for now, we have to live with the possibility of overflow.

In the meantime, overflow-safe integer operations can be achieved through the use of external libraries
such as [SaferIntegers.jl](https://github.com/JeffreySarnoff/SaferIntegers.jl). Note that, as stated
previously, the use of these libraries significantly increases the execution time of code using the
checked integer types. However, for limited usage, this is far less of an issue than if it were used
for all integer operations. You can follow the status of the discussion
[here](https://github.com/JuliaLang/julia/issues/855).


### What are the possible causes of an `UndefVarError` during remote execution?

As the error states, an immediate cause of an `UndefVarError` on a remote node is that a binding
by that name does not exist. Let us explore some of the possible causes.

```julia-repl
julia> module Foo
           foo() = remotecall_fetch(x->x, 2, "Hello")
       end

julia> Foo.foo()
ERROR: On worker 2:
UndefVarError: Foo not defined
Stacktrace:
[...]
```

The closure `x->x` carries a reference to `Foo`, and since `Foo` is unavailable on node 2,
an `UndefVarError` is thrown.

Globals under modules other than `Main` are not serialized by value to the remote node. Only a reference is sent.
Functions which create global bindings (except under `Main`) may cause an `UndefVarError` to be thrown later.

```julia-repl
julia> @everywhere module Foo
           function foo()
               global gvar = "Hello"
               remotecall_fetch(()->gvar, 2)
           end
       end

julia> Foo.foo()
ERROR: On worker 2:
UndefVarError: gvar not defined
Stacktrace:
[...]
```

In the above example, `@everywhere module Foo` defined `Foo` on all nodes. However the call to `Foo.foo()` created
a new global binding `gvar` on the local node, but this was not found on node 2 resulting in an `UndefVarError` error.

Note that this does not apply to globals created under module `Main`. Globals under module `Main` are serialized
and new bindings created under `Main` on the remote node.

```julia-repl
julia> gvar_self = "Node1"
"Node1"

julia> remotecall_fetch(()->gvar_self, 2)
"Node1"

julia> remotecall_fetch(varinfo, 2)
name          size summary
––––––––– –––––––– –––––––
Base               Module
Core               Module
Main               Module
gvar_self 13 bytes String
```

This does not apply to `function` or `struct` declarations. However, anonymous functions bound to global
variables are serialized as can be seen below.

```julia-repl
julia> bar() = 1
bar (generic function with 1 method)

julia> remotecall_fetch(bar, 2)
ERROR: On worker 2:
UndefVarError: #bar not defined
[...]

julia> anon_bar  = ()->1
(::#21) (generic function with 1 method)

julia> remotecall_fetch(anon_bar, 2)
1
```

### Why does Julia use `*` for string concatenation? Why not `+` or something else?

The [main argument](@ref man-concatenation) against `+` is that string concatenation is not
commutative, while `+` is generally used as a commutative operator. While the Julia community
recognizes that other languages use different operators and `*` may be unfamiliar for some
users, it communicates certain algebraic properties.

Note that you can also use `string(...)` to concatenate strings (and other values converted
to strings); similarly, `repeat` can be used instead of `^` to repeat strings. The
[interpolation syntax](@ref string-interpolation) is also useful for constructing strings.

## Packages and Modules

### What is the difference between "using" and "import"?

There is only one difference, and on the surface (syntax-wise) it may seem very minor. The difference
between `using` and `import` is that with `using` you need to say `function Foo.bar(..` to
extend module Foo's function bar with a new method, but with `import Foo.bar`,
you only need to say `function bar(...` and it automatically extends module Foo's function bar.

The reason this is important enough to have been given separate syntax is that you don't want
to accidentally extend a function that you didn't know existed, because that could easily cause
a bug. This is most likely to happen with a method that takes a common type like a string or integer,
because both you and the other module could define a method to handle such a common type. If you
use `import`, then you'll replace the other module's implementation of `bar(s::AbstractString)`
with your new implementation, which could easily do something completely different (and break
all/many future usages of the other functions in module Foo that depend on calling bar).

## Nothingness and missing values

### [How does "null", "nothingness" or "missingness" work in Julia?]

Unlike many languages (for example, C and Java), Julia objects cannot be "null" by default.
When a reference (variable, object field, or array element) is uninitialized, accessing it
will immediately throw an error. This situation can be detected using the
[`isdefined`](@ref) or [`isassigned`](@ref Base.isassigned) functions.

Some functions are used only for their side effects, and do not need to return a value. In these
cases, the convention is to return the value `nothing`, which is just a singleton object of type
`Nothing`. This is an ordinary type with no fields; there is nothing special about it except for
this convention, and that the REPL does not print anything for it. Some language constructs that
would not otherwise have a value also yield `nothing`, for example `if false; end`.

For situations where a value `x` of type `T` exists only sometimes, the `Union{T, Nothing}`
type can be used for function arguments, object fields and array element types
as the equivalent of [`Nullable`, `Option` or `Maybe`](https://en.wikipedia.org/wiki/Nullable_type)
in other languages. If the value itself can be `nothing` (notably, when `T` is `Any`),
the `Union{Some{T}, Nothing}` type is more appropriate since `x == nothing` then indicates
the absence of a value, and `x == Some(nothing)` indicates the presence of a value equal
to `nothing`. The [`something`](@ref) function allows unwrapping `Some` objects and
using a default value instead of `nothing` arguments. Note that the compiler is able to
generate efficient code when working with `Union{T, Nothing}` arguments or fields.

To represent missing data in the statistical sense (`NA` in R or `NULL` in SQL), use the
[`missing`](@ref) object. See the [`Missing Values`](@ref missing) section for more details.

In some languages, the empty tuple (`()`) is considered the canonical
form of nothingness. However, in julia it is best thought of as just
a regular tuple that happens to contain zero values.

The empty (or "bottom") type, written as `Union{}` (an empty union type), is a type with
no values and no subtypes (except itself). You will generally not need to use this type.

## Memory

### Why does `x += y` allocate memory when `x` and `y` are arrays?

In Julia, `x += y` gets replaced during parsing by `x = x + y`. For arrays, this has the consequence
that, rather than storing the result in the same location in memory as `x`, it allocates a new
array to store the result.

While this behavior might surprise some, the choice is deliberate. The main reason is the presence
of immutable objects within Julia, which cannot change their value once created.  Indeed, a
number is an immutable object; the statements `x = 5; x += 1` do not modify the meaning of `5`,
they modify the value bound to `x`. For an immutable, the only way to change the value is to reassign
it.

To amplify a bit further, consider the following function:

```julia
function power_by_squaring(x, n::Int)
    ispow2(n) || error("This implementation only works for powers of 2")
    while n >= 2
        x *= x
        n >>= 1
    end
    x
end
```

After a call like `x = 5; y = power_by_squaring(x, 4)`, you would get the expected result: `x == 5 && y == 625`.
 However, now suppose that `*=`, when used with matrices, instead mutated the left hand side.
 There would be two problems:

  * For general square matrices, `A = A*B` cannot be implemented without temporary storage: `A[1,1]`
    gets computed and stored on the left hand side before you're done using it on the right hand side.
  * Suppose you were willing to allocate a temporary for the computation (which would eliminate most
    of the point of making `*=` work in-place); if you took advantage of the mutability of `x`, then
    this function would behave differently for mutable vs. immutable inputs. In particular, for immutable
    `x`, after the call you'd have (in general) `y != x`, but for mutable `x` you'd have `y == x`.

Because supporting generic programming is deemed more important than potential performance optimizations
that can be achieved by other means (e.g., using explicit loops), operators like `+=` and `*=`
work by rebinding new values.

## [Asynchronous IO and concurrent synchronous writes]

### Why do concurrent writes to the same stream result in inter-mixed output?

While the streaming I/O API is synchronous, the underlying implementation is fully asynchronous.

Consider the printed output from the following:

```jldoctest
julia> @sync for i in 1:3
           @async write(stdout, string(i), " Foo ", " Bar ")
       end
123 Foo  Foo  Foo  Bar  Bar  Bar
```

This is happening because, while the `write` call is synchronous, the writing of each argument
yields to other tasks while waiting for that part of the I/O to complete.

`print` and `println` "lock" the stream during a call. Consequently changing `write` to `println`
in the above example results in:

```jldoctest
julia> @sync for i in 1:3
           @async println(stdout, string(i), " Foo ", " Bar ")
       end
1 Foo  Bar
2 Foo  Bar
3 Foo  Bar
```

You can lock your writes with a `ReentrantLock` like this:

```jldoctest
julia> l = ReentrantLock();

julia> @sync for i in 1:3
           @async begin
               lock(l)
               try
                   write(stdout, string(i), " Foo ", " Bar ")
               finally
                   unlock(l)
               end
           end
       end
1 Foo  Bar 2 Foo  Bar 3 Foo  Bar
```

## Arrays

### What are the differences between zero-dimensional arrays and scalars?

Zero-dimensional arrays are arrays of the form `Array{T,0}`. They behave similar
to scalars, but there are important differences. They deserve a special mention
because they are a special case which makes logical sense given the generic
definition of arrays, but might be a bit unintuitive at first. The following
line defines a zero-dimensional array:

```
julia> A = zeros()
0-dimensional Array{Float64,0}:
0.0
```

In this example, `A` is a mutable container that contains one element, which can
be set by `A[] = 1.0` and retrieved with `A[]`. All zero-dimensional arrays have
the same size (`size(A) == ()`), and length (`length(A) == 1`). In particular,
zero-dimensional arrays are not empty. If you find this unintuitive, here are
some ideas that might help to understand Julia's definition.

* Zero-dimensional arrays are the "point" to vector's "line" and matrix's
  "plane". Just as a line has no area (but still represents a set of things), a
  point has no length or any dimensions at all (but still represents a thing).
* We define `prod(())` to be 1, and the total number of elements in an array is
  the product of the size. The size of a zero-dimensional array is `()`, and
  therefore its length is `1`.
* Zero-dimensional arrays don't natively have any dimensions into which you
  index -- they’re just `A[]`. We can apply the same "trailing one" rule for them
  as for all other array dimensionalities, so you can indeed index them as `A[1]`, `A[1,1]`, etc; see
  [Omitted and extra indices](@ref).

It is also important to understand the differences to ordinary scalars. Scalars
are not mutable containers (even though they are iterable and define things
like `length`, `getindex`, *e.g.* `1[] == 1`). In particular, if `x = 0.0` is
defined as a scalar, it is an error to attempt to change its value via
`x[] = 1.0`. A scalar `x` can be converted into a zero-dimensional array
containing it via `fill(x)`, and conversely, a zero-dimensional array `a` can
be converted to the contained scalar via `a[]`. Another difference is that
a scalar can participate in linear algebra operations such as `2 * rand(2,2)`,
but the analogous operation with a zero-dimensional array
`fill(2) * rand(2,2)` is an error.

### Why are my Julia benchmarks for linear algebra operations different from other languages?

You may find that simple benchmarks of linear algebra building blocks like

```julia
using BenchmarkTools
A = randn(1000, 1000)
B = randn(1000, 1000)
@btime $A \ $B
@btime $A * $B
```

can be different when compared to other languages like Matlab or R.

Since operations like this are very thin wrappers over the relevant BLAS functions, the reason for the discrepancy is very likely to be

1. the BLAS library each language is using,

2. the number of concurrent threads.

Julia compiles and uses its own copy of OpenBLAS, with threads currently capped at `8` (or the number of your cores).

Modifying OpenBLAS settings or compiling Julia with a different BLAS library, eg [Intel MKL](https://software.intel.com/en-us/mkl), may provide performance improvements. You can use [MKL.jl](https://github.com/JuliaComputing/MKL.jl), a package that makes Julia's linear algebra use Intel MKL BLAS and LAPACK instead of OpenBLAS, or search the discussion forum for suggestions on how to set this up manually. Note that Intel MKL cannot be bundled with Julia, as it is not open source.

## Computing cluster

### How do I manage precompilation caches in distributed file systems?

When using `julia` in high-performance computing (HPC) facilities, invoking
_n_ `julia` processes simultaneously creates at most _n_ temporary copies of
precompilation cache files. If this is an issue (slow and/or small distributed
file system), you may:

1. Use `julia` with `--compiled-modules=no` flag to turn off precompilation.
2. Configure a private writable depot using `pushfirst!(DEPOT_PATH, private_path)`
   where `private_path` is a path unique to this `julia` process.  This
   can also be done by setting environment variable `JULIA_DEPOT_PATH` to
   `$private_path:$HOME/.julia`.
3. Create a symlink from `~/.julia/compiled` to a directory in a scratch space.

## Julia Releases

### Do I want to use the Stable, LTS, or nightly version of Julia?

The Stable version of Julia is the latest released version of Julia, this is the version most people will want to run.
It has the latest features, including improved performance.
The Stable version of Julia is versioned according to [SemVer](https://semver.org/) as v1.x.y.
A new minor release of Julia corresponding to a new Stable version is made approximately every 4-5 months after a few weeks of testing as a release candidate.
Unlike the LTS version the a Stable version will not normally recieve bugfixes after another Stable version of Julia has been released.
However, upgrading to the next Stable release will always be possible as each release of Julia v1.x will continue to run code written for earlier versions.

You may prefer the LTS (Long Term Support) version of Julia if you are looking for a very stable code base.
The current LTS version of Julia is versioned according to SemVer as v1.0.x;
this branch will continue to recieve bugfixes until a new LTS branch is chosen, at which point the v1.0.x series will no longer recieved regular bug fixes and all but the most conservative users will be advised to upgrade to the new LTS version series.
As a package developer, you may prefer to develop for the LTS version, to maximize the number of users who can use your package.
As per SemVer, code written for v1.0 will continue to work for all future LTS and Stable versions.
In general, even if targetting the LTS, one can develop and run code in the latest Stable version, to take advantage of the improved performance; so long as one avoids using new features (such as added library functions or new methods).

You may prefer the nightly version of Julia if you want to take advantage of the latest updates to the language, and don't mind if the version available today occasionally doesn't actually work.
As the name implies, releases to the nightly version are made roughly every night (depending on build infrastructure stability).
In general nightly released are fairly safe to use—your code will not catch on fire.
However, they may be occasional regressions and or issues that will not be found until more thorough pre-release testing.
You may wish to test against the nightly version to ensure that such regressions that affect your use case are caught before a release is made.

Finally, you may also consider building Julia from source for yourself. This option is mainly for those individuals who are comfortable at the command line, or interested in learning.
If this describes you, you may also be interested in reading our [guidelines for contributing](https://github.com/JuliaLang/julia/blob/master/CONTRIBUTING.md).

Links to each of these download types can be found on the download page at [https://julialang.org/downloads/](https://julialang.org/downloads/).
Note that not all versions of Julia are available for all platforms.

### How can I transfer the list of installed packages after updating my version of Julia?

Each minor version of julia has its own default [environment](https://docs.julialang.org/en/v1/manual/code-loading/#Environments-1). As a result, upon installing a new minor version of Julia, the packages you added using the previous minor version will not be available by default. The environment for a given julia version is defined by the files `Project.toml` and `Manifest.toml` in a folder matching the version number in `.julia/environments/`, for instance, ` .julia/environments/v1.3`.

If you install a new minor version of Julia, say `1.4`, and want to use in its default environment the same packages as in a previous version (e.g. `1.3`), you can copy the contents of the file `Project.toml` from the `1.3` folder to `1.4`. Then, in a session of the new Julia version, enter the "package management mode" by typing the key `]`, and run the command [`instantiate`](https://julialang.github.io/Pkg.jl/v1/api/#Pkg.instantiate).

This operation will resolve a set of feasible packages from the copied file that are compatible with the target Julia version, and will install or update them if suitable. If you want to reproduce not only the set of packages, but also the versions you were using in the previous Julia version, you should also copy the `Manifest.toml` file before running the Pkg command `instantiate`. However, note that packages may define compatibility constraints that may be affected by changing the version of Julia, so the exact set of versions you had in `1.3` may not work for `1.4`.
