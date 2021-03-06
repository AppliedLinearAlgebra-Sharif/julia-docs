# اعداد مختلط و گویا

جولیا شامل انواع از پیش تعریف‌شده برای اعداد گویا و حقیقی است و تمام عملیت‌‌های استاندارد ریاضی و توابع ابتدایی روی آن‌‌ها را پشتیبانی می‌کند. تبدیل این متغیر‌ها به گونه‌ای پیاده‌سازی شده که هر ترکیبی از تایپ‌های عددی از پیش تعریف شده، همانگونه که انتظار داریم عمل خواهند کرد.


## اعداد مختلط

از ثابت سراسری `im` برای نشان دادن عدد مختلط *i* استفاده می‌کنیم که نشان‌دهنده عدد اصلی اعداد مختلط یعنی جذر ۱- است. ریاضیدانان و مهندسان معمولا از نمادهای i و j برای این ثابت سراسری استفاده می‌کنند که بخاطر استفاده‌ی زیاد این نمادها در اندیس گذاری، از عبارت im برای این ثابت استفاده شده است. از آنجا که جولیا اجازه می‌دهد حروف عددی با شناسه‌ها درکنار هم (به عنوان ضریب) قرار بگیرند تا مشابه نمادگذاری سنتی ریاضی باشند:
 
```jlia
julia> 1+2im
1 + 2im
```

شما می‌تواند تمام عملیات‌های حسابی و منطقی را با اعداد مختلط نیز انجام دهید:

```julia
julia> (1 + 2im)*(2 - 3im)
8 + 1im

julia> (1 + 2im)/(1 - 2im)
-0.6 + 0.8im

julia> (1 + 2im) + (1 - 2im)
2 + 0im

julia> (-3 + 2im) - (5 - 1im)
-8 + 3im

julia> (-1 + 2im)^2
-3 - 4im

julia> (-1 + 2im)^2.5
2.729624464784009 - 6.9606644595719im

julia> (-1 + 2im)^(1 + 1im)
-0.27910381075826657 + 0.08708053414102428im

julia> 3(2 - 5im)
6 - 15im

julia> 3(2 - 5im)^2
-63 - 60im

julia> 3(2 - 5im)^-1.0
0.20689655172413796 + 0.5172413793103449im
```

ساز و کار ارتقای جولیا به گونه‌ای است که اطمینان حاصل می‌کند که ترکیب عملوندهای مختلف از انواع مختلف، همانگونه که انتظار داریم کار کنند:

```julia
julia> 2(1 - 1im)
2 - 2im

julia> (2 + 3im) - 1
1 + 3im

julia> (1 + 2im) + 0.5
1.5 + 2.0im

julia> (2 + 3im) - 0.5im
2.0 + 2.5im

julia> 0.75(1 + 2im)
0.75 + 1.5im

julia> (2 + 3im) / 2
1.0 + 1.5im

julia> (1 - 3im) / (2 + 2im)
-0.5 - 1.0im

julia> 2im^2
-2 + 0im

julia> 1 + 3/4im
1.0 - 0.75im
```

دقت کنید  `3/4im == 3/(4*im) == -(3/4*im)` برقرار است، زیرا یک ضریب حرفی، اولویت بیشتری نسبت به تقسیم دارد.

توابع استاندارد برای اعمال تغییر روی مقادیر مختلط:

```julia
julia> z = 1 + 2im
1 + 2im

julia> real(1 + 2im) # real part of z
1

julia> imag(1 + 2im) # imaginary part of z
2

julia> conj(1 + 2im) # complex conjugate of z
1 - 2im

julia> abs(1 + 2im) # absolute value of z
2.23606797749979

julia> abs2(1 + 2im) # squared absolute value
5

julia> angle(1 + 2im) # phase angle in radians
1.1071487177940904
```

می‌دانیم که مقدار قدر مطلق یک عدد گنگ (`abs`) فاصله‌ی آن  از صفر است. `abs2` مقدار مربع قدرمطلق را می‌دهد و برای اعداد مختلط کاربرد به خصوص‌تری دارد زیرا از گرفتن ریشه‌ی مربع عدد جلوگیری می‌کند. `angle` مقدار زاویه‌ی فاز را برحسب رادیان برمی‌گرداند.
 سایر توابع ابتدایی نیز به طور کامل برای اعداد مختلط تعریف شده اند:

```julia
julia> sqrt(1im)
0.7071067811865476 + 0.7071067811865475im

julia> sqrt(1 + 2im)
1.272019649514069 + 0.7861513777574233im

julia> cos(1 + 2im)
2.0327230070196656 - 3.0518977991517997im

julia> exp(1 + 2im)
-1.1312043837568135 + 2.4717266720048188im

julia> sinh(1 + 2im)
-0.4890562590412937 + 1.4031192506220405im
```

توجه داشته باشید که توابع ریاضی معمولاً وقتی روی اعداد حقیقی اعمال می شوند مقادیر حقیقی را برمی گردانند و وقتی روی اعداد مختلط اعمال می شوند مقادیر مختلط را برمی گردانند. به طور مثال، `sqrt` وقتی روی `-1` اعمال می‌شود رفتار متفاوتی با `-1 + 0im` دارد، هرچند که  `-1 == -1 + 0im`برقرار است:

```julia
julia> sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).
Stacktrace:
[...]

julia> sqrt(-1 + 0im)
0.0 + 1.0im
```

ضرایب حرفی هنگام ساختن عدد مختلط کار نمی‌کنند، در عوض ضرب باید به صورت صریح انجام شود:

```julia
julia> a = 1; b = 2; a + b*im
1 + 2im
```
هرچند روش گفته شده پیشنهاد نمی‌شود، در عوض از تابع به‌ صرفه‌ و ساده‌تر  `complex` برای ساختن عدد مختلط برحسب مقادیر حقیقی و مختلط آن استفاده کنید:

```ulia
julia> a = 1; b = 2; complex(a, b)
1 + 2im
```
این نوع ساختن، از عملیات‌های ضرب و جمع جلوگیری می‌کند.

مقادیر `inf` و `NaN`  روی بخش‌های حقیقی و مختلط عدد نیز،‌ منتشر می‌شوند:
```julia
julia> 1 + Inf*im
1.0 + Inf*im

julia> 1 + NaN*im
1.0 + NaN*im
```

## اعداد گویا

جولیا از نوع  اعداد گویا نیز پشتیبانی می‌کند تا بتواند نسبت دقیق اعداد صحیح را نشان دهد. اعداد گویا توسط عملگر `//` ساخته می‌شوند:

```julia
julia> 2//3
2//3
```

اگر صورت و مخرج یک عدد گویا، شامل مقسوم علیه مشترک باشند، این عدد نسبت به بزرگترین مقسوم علیه مشترک ساده می‌شود، به طوری که در نهایت مخرج منفی نشود:

```julia
julia> 6//9
2//3

julia> -4//8
-1//2

julia> 5//-15
-1//3

julia> -4//-12
1//3
```

فرم نرمال‌سازی شده‌‌ی نسبت اعداد صحیح منحصربه‌فرد است، بنابراین برای بررسی برابری دو عدد، می‌توان برابری صورت و مخرج آن‌ها را بررسی کرد. صورت و مخرج استاندارد یک عدد گویا را می‌توان  با استفاده از `numerator` و `denominator` به دست‌آورد:

```julia
julia> numerator(2//3)
2

julia> denominator(2//3)
3
```

معمولا محاسبه‌ی دقیق صورت و مخرح لاز م نیست، زیرا عملیت‌های حسابی و مقایسه‌ای برای اعداد گویا پیاده‌سازی شده‌اند:

```julia
julia> 2//3 == 6//9
true

julia> 2//3 == 9//27
false

julia> 3//7 < 1//2
true

julia> 3//4 > 2//3
true

julia> 2//4 + 1//6
2//3

julia> 5//12 - 1//4
1//6

julia> 5//8 * 3//12
5//32

julia> 6//5 / 10//7
21//25
```
اعداد گویا را می‌توان به راحتی به عداد اعشاری (ممیز شناور) تبدیل کرد:

```julia
julia> float(3//4)
0.75
```
عملیات تبدیل از اعداد گویا به اعداد اعشاری برای هر مقدار صحیح `a` و `b` مقادیر آن‌ها حفظ می‌کند به جز حالت `a == 0` و `b == 0`:

```julia
julia> a = 1; b = 2;

julia> isequal(float(a//b), a/b)
true
```
ساختن مقادیر گویای بینهایت نیز پشتیبانی می‌شود:

```julia
julia> 5//0
1//0

julia> x = -3//0
-1//0

julia> typeof(x)
Rational{Int64}
```
اما ساختن مقدار گویای `NaN` امکان‌پذیر نیست:

```ulia
julia> 0//0
ERROR: ArgumentError: invalid rational: zero(Int64)//zero(Int64)
Stacktrace:
[...]
```
و مطابق انتظار، ساز و کار تبدیل امکان برهمکنش این نوع عدد با باقی انواع عددی را امکان‌پذیر می‌کند:

```julia
julia> 3//5 + 1
8//5

julia> 3//5 - 0.5
0.09999999999999998

julia> 2//7 * (1 + 2im)
2//7 + 4//7*im

julia> 2//7 * (1.5 + 2im)
0.42857142857142855 + 0.5714285714285714im

julia> 3//2 / (1 + 2im)
3//10 - 3//5*im

julia> 1//2 + 2im
1//2 + 2//1*im

julia> 1 + 2//3im
1//1 - 2//3*im

julia> 0.5 == 1//2
true

julia> 0.33 == 1//3
false

julia> 0.33 < 1//3
true

julia> 1//3 - 0.33
0.0033333333333332993
```


