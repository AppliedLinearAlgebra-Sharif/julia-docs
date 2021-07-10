#  <div dir="rtl"> تعبیه جولیا  </div>

<div dir="rtl"> همانطور که در Calling C و Fortran Code , دیده ایم جولیا یک راه ساده و به صرفه دارد که توابع را که در C نوشته شده اند را صدا بزند. اما موقعیت هایی وجود دارند که برعکس این روند نیاز است یعنی صدا زدن توابع جولیا از زبان C. این کار می تواند استفاده شود تا جولیا را با یک پروژه c/c++ بزرگتر ادغام کند بدون نیاز به دوباره نوشتن همه چیز در C/C++. جولیا یک API C دارد تا این کار را ممکن کند.همانند تقریبا همه زبان های برنامه نویسی که امکان این را دارند که توابع C را صدا بزنند , API C جولیا هم می تواند استفاده شود برای ساخت پل های بیش تر به زبان ها  (برای مثال صدا زدن جولیا از Python یا #C)   </div>

## <div dir="rtl">  تعبیه سطح بالا  </div>

__<div dir="rtl">  توجه:  </div>__
<div dir="rtl">  این قسمت تعبیه کد جولیا را در زبان C روی سیستم عامل های شبه یونیکس پوشش می دهد. برای انجام دادن این کار روی ویندوز لطفا بخش بعد را نگاه کنید. </div>
<div dir="rtl"> با یک برنامه ساده C شروع می کنیم که ابتدا جولیا را مقدار دهی اولیه می کند و سپس کد جولیا را صدا می زند:


```c
#include <julia.h>
JULIA_DEFINE_FAST_TLS() // only define this once, in an executable (not in a shared library) if you want fast code.

int main(int argc, char *argv[])
{
    /* required: setup the Julia context */
    jl_init();

    /* run Julia commands */
    jl_eval_string("print(sqrt(2.0))");

    /* strongly recommended: notify Julia that the
         program is about to terminate. this allows
         Julia time to cleanup pending write requests
         and run all finalizers
    */
    jl_atexit_hook(0);
    return 0;
}
```
<div dir="rtl">  برای اینکه این برنامه را بسازید باید مسیر هدر جولیا را در مسیر include قراردهید و با `libjulia` لینک کنید. برای مثال, وقتی که جولیا در `$JULIA_DIR` نصب شد می توانید برنامه تست بالا را یعنی `test.c` را با `gcc` با استفاده از کد زیر اجرا کنید:</div>

```
gcc -o test -fPIC -I$JULIA_DIR/include/julia -L$JULIA_DIR/lib -Wl,-rpath,$JULIA_DIR/lib test.c -ljulia
```
<div dir="rtl"> از سوی دیگر به برنامه `embedding.c` در درخت منبع (source tree) جولیا در فولدر `test/embedding/`نگاه کنید.</div>
<div dir="rtl">  فایل برنامه `cli/loader_exe.c` یک مثال ساده دیگر است از اینکه چطور گزینه های `jl_options` را تنظیم کنیددرحالیکه با `libjulia` لینک می کنید.</div>   

<div dir="rtl"> اولین چیزی که باید انجام شود قبل صدا زدن هر تابع دیگر جولیا در C این است که جولیا مقدار دهی اولیه شود. این کار با صدا زدن `jl_init` انجام می شود که تلاش می کند که به صورت خودکار محل نصب جولیا را تشخیص دهد. اگر شما می خواهید که یک مکان دلخواه را مشخص کنید یا مشخص کنید که کدام سیستم ایمیج لود شود از `jl_init_with_image` استفاده کنید.</div>   

    
<div dir="rtl"> عبارت دوم در برنامه test با استفاده از صدا زدن `jl_eval_string` یک عبارت جولیا را بررسی و اجرا می کند.</div>

<div dir="rtl"> قبل از اینکه برنامه اجرا شود به شدت توصیه می شود که `jl_atexit_hook` را صدا بزنید.  برنامه بالا این عبارت را قبل از بازگشت از `main` صدا می زند. </div>  


```eval_rst

.. توجه::
    در حال حاضر لینک کردن با کتاب خانه اشتراکی `libjulia` به صورت پویا نیاز به رد شدن از `RTLD_GLOBAL` دارد. در پایتون این کار به صورت زیر است: 
    
    .. code-block:: julia

        >>> julia=CDLL('./libjulia.dylib',RTLD_GLOBAL)
        >>> julia.jl_init.argtypes = []
        >>> julia.jl_init()
        250593296

```

```eval_rst

.. توجه::
    اگر برنامه جولیا نیاز به دسترسی کد هایی از برنامه اصلی داشته باشد ممکن است نیاز باشد که پرچم لینک دهنده(linker flag)  `-Wl,--export-dynamic`  روی لینوکس در زمان اجرا علاوه بر آن هایی که توسط `julia-config.jl` تولید شده اند که در پایین توضیح داده شده است اضافه شود. این کار زمان اجرای کتابخانه اشتراکی واجب نیست. 
```
### استفاده از julia-config برای تعیین کردن پارامتر های ساخت به صورت خودکار 
فایل `julia-config.jl` ساخته شده است که کمک کند در مشخص کردن اینکه چه پارامتر های ساختی برای یک برنامه ای که جولیا را تعبیه کرده است لازم است. این فایل از پارامتر های ساخت و از پیکر بندی سیستم توزیع خاص جولیا که توسط آن فراخوانی می شود استفاده می کند تا پرچم های اجرا (compiler flags)  های ضروری را برای برنامه ای که جولیا را تعبیه می کند صادر کند تا با آن توزیع تعامل کند. این فایل در دایرکتوری داده های اشتراکی جولیا قرار دارد.  
    
#### مثال

```c
#include <julia.h>

int main(int argc, char *argv[])
{
    jl_init();
    (void)jl_eval_string("println(sqrt(2.0))");
    jl_atexit_hook(0);
    return 0;
}
```

#### در خط فرمان (Command Line)

یک استفاده ساده از این فایل از طریق خط فرمان (Command Line) است. فرض کنید که `julia-config.jl` در آدرس `/usr/local/julia/share/julia` قرار گرفته است در این صورت این فایل می تواند از طریق خط فرمان (Command Line) به صورت مستقیم فراخوانی شود و هر ترکیبی از سه پرچم (flag) را داشته باشد :   

```
/usr/local/julia/share/julia/julia-config.jl
Usage: julia-config [--cflags|--ldflags|--ldlibs]
```

اگر سورس مثال بالا در فایل `embed_example.c` ذخیره شود در این صورت دستور بعدی آن را به یک برنامه در حال اجرا روی لینوس و ویندوز (محیط MSYS2) کامپایل میکند یا اگر روی OS/X هستید کافیست `clang` را جایگزین `gcc` کنید.:
    
```
/usr/local/julia/share/julia/julia-config.jl --cflags --ldflags --ldlibs | xargs gcc embed_example.c
```
#### استفاده در ساخت فایل ها

اما در حالت عمومی تعبیه پروژه ها پیچیده تر از مثال بالا هستند بنابراین قسمت بعدی امکان پشتیبانی عمومی ساخت فایل را می دهد با فرض ساخت GNU به این دلیل  استفاده از بسط درشت (macro expansion) **shell**. علاوه بر این اگر چه ممکن است که `julia-config.jl` چندین بار در آدرس `/usr/local` پیدا شود این حالت ضرورتا پیش نمی آید اما جولیا می تواند استفاده شود تا `julia-config.jl` را قرار دهد و ساخت فایل (makefile)  می تواند برای این کار استفاده شود. در این جا مثال بالا گسترش پیدا کرده است تا در ساخت فایل استفاده شود:

```
JL_SHARE = $(shell julia -e 'print(joinpath(Sys.BINDIR, Base.DATAROOTDIR, "julia"))')
CFLAGS   += $(shell $(JL_SHARE)/julia-config.jl --cflags)
CXXFLAGS += $(shell $(JL_SHARE)/julia-config.jl --cflags)
LDFLAGS  += $(shell $(JL_SHARE)/julia-config.jl --ldflags)
LDLIBS   += $(shell $(JL_SHARE)/julia-config.jl --ldlibs)

all: embed_example
```

حال دستور ساخت تنها دستور `make` است.

## تعبیه سطح بالا روی ویندوز از طریق ویژوال استودیو
    
اگر متغیر محیطی `JULIA_DIR` تنظیم نشده باشد آن را قبل از شروع کردن ویژوال استودیو با استفاده از پنل سیستم (System Panel) اضافه کنید. پوشه `bin` تحت JULIA_DIR باید در مسیر سیستم (system PATH) باشد.

با باز کردن ویژوال استودیو و ساختن یک پروژه برنامه کنسولی  (Console Application) جدید شروع می کنیم. به اخر هدر فایل 'stdafx.h' خط های بعدی را اضافه کنید: 
    
```c
#include <julia.h>
```
سپس تابع main() را در پروژه با این کد جایگزین کنید:
    
```c
int main(int argc, char *argv[])
{
    /* required: setup the Julia context */
    jl_init();

    /* run Julia commands */
    jl_eval_string("print(sqrt(2.0))");

    /* strongly recommended: notify Julia that the
         program is about to terminate. this allows
         Julia time to cleanup pending write requests
         and run all finalizers
    */
    jl_atexit_hook(0);
    return 0;
}
```
قدم بعدی این است که پروژه را تنظیم کنید تا فایل های include جولیا را و کتابخانه ها را پیدا کنید. مهم است که بدانید که نسخه نصب شده جولیا شما ۳۲ بیت است یا ۶۴ بیت. هر هر پیکر بندی پلتفرم را که با نسخه نصب شده جولیا تطابق ندارد قبل از ادامه دادن حذف کنید.

با استفاده از دیالوگ ویژگی های پروژه (Properties)  به `C/C++` | `General` بروید و `$(JULIA_DIR)\include\julia\` را به ویژگی مسیرهای اضافی include (Additional Include Directories) اضافه کنید. سپس به قسمت `Linker` | `General` بروید و `$(JULIA_DIR)\lib` را به ویژگی مسیرهای اضافی کتابخانه (Additional Library Directories) اضافه کنید. در آخر تحت `Linker` | `Input` به لیست کتابخانه ها `libjulia.dll.a;libopenlibm.dll.a;` را اضافه کنید.

در این مرحله پروژه باید ساخته شود و اجرا شود.

## انواع تبدیل

برنامه های واقعی تنها لازم ندارند که عبارت ها را اجرا کنند بلکه نیاز دارند که مقادیر خود را به برنامه میزبان برگرداند. `jl_eval_string` یک `jl_value_t*` را بر می گرداند که یک نشانگر است به یک شی جولیا پشته اختصاص داده شده (heap-allocated). ذخیره کردن انواع ساده داده ها برای مثال `Float64` به این روش `boxing` گفته می شود و استخراج داده های اولیه ذخیره شده `unboxing` گفته می شود. برنامه نمونه گسترش یافته ما که ریشه دوم  را در جولیا محاسبه می کند و نتیجه را در زبان C دوباره می خواند به شکل زیر است:

```c
jl_value_t *ret = jl_eval_string("sqrt(2.0)");

if (jl_typeis(ret, jl_float64_type)) {
    double ret_unboxed = jl_unbox_float64(ret);
    printf("sqrt(2.0) in C: %e \n", ret_unboxed);
}
else {
    printf("ERROR: unexpected return type from sqrt(::Float64)\n");
}
```

برای اینکه چک کنیم که ایا  `ret` یک نوع مشخص جولیا است می توانیم از توابع `jl_isa` یا `jl_typeis` و یا `jl_is_...` استفاده کنیم.
با تایپ کردن `typeof(sqrt(2.0))` در Julia shell می توانیم ببینیم که نوع بازگشت شده `Float64` (`double` در C) است. برای اینکه مقدار ذخیره شده جولیا را تبدیل کنیم به یک رقم اعشاری (double) در C تابع `jl_unbox_float64` در قطعه کد بالا استفاده می شود.      

توابع متناظر `jl_box_...` برای تبدیل به نوع دیگری استفاده می شود :

```c
jl_value_t *a = jl_box_float64(3.0);
jl_value_t *b = jl_box_float32(3.0f);
jl_value_t *c = jl_box_int32(3);
```
همانطور که خواهیم دید ذخیره سازی (boxing) برای صدا زدن توابع جولیا با ارگومان های خاص لازم است.

## صدا زدن توابع جولیا

در حالی که `jl_eval_string` به زبان C اجازه می دهد که نتیجه یک عبارت جولیا را بگیرد اجازه رد شدن ارگومان های محاسبه شده در C را به جولیا نمی دهد.برای این کار نیاز دارید که به صورت مستقیم توابع جولیا را با استفاده از `jl_call` فراخوانی کنید.

```c
jl_function_t *func = jl_get_function(jl_base_module, "sqrt");
jl_value_t *argument = jl_box_float64(2.0);
jl_value_t *ret = jl_call1(func, argument);
```
در قدم اول یک فهم از تابع `sqrt`جولیا با صدا زدن `jl_get_function` بازیابی می شود.
اولین ارگومانی که به `jl_get_function` فرستاده می شود یک نشانگر به ماژول `Base` است که `sqrt` در آن تعریف شده است. سپس مقدار اعشاری (double) توسط `jl_box_float64` ذخیره (boxing) می شود. در مرحله آخر تابع با استفاده از `jl_call1` صدا زده می شود. توابع  `jl_call0` و `jl_call2` و `jl_call3` هم وجود دارند برای مدیریت کردن آسان تعداد مختلف آرگومان ها. برای فرستادن تعداد بیش تر آرگومان ها از `jl_call` استفاده کنید :

```
jl_value_t *jl_call(jl_function_t *f, jl_value_t **args, int32_t nargs)
```
آرگومان دوم یعنی `args` یک آرایه از آرگومان های `jl_value_t*` است و `nargs` تعداد آرگومان ها است.

## مدیریت حافظه

    همانطور که دیدیم اشیای جولیا در C به عنوان نشانگر نمایش داده می شوند. این موضوع این سوال را مطرح می کند که چه کسی مسئول آزاد کردن این اشیاء هست.

معمولا اشیای جولیا توسط زباله رو (garbage collector (GC)) آزاد می شوند اما GC بطور خودکار نمی داند که ما یک ارجاع به مقدار جولیا از C را نگه داشته ایم. این بدین معنی است GC می تواند اشیا را از دست شما آزاد کند. و منجر به نمایش نشانگر های نامعتبر شود.
    
GC فقط زمانی اجرا می شود که اشیا جولیا تخصیص داده شده اند. فراخوانی شبیه `jl_box_float64` تخصیص را انجام می دهد و تخصیص ممکن است در حین اجرای جولیا در هر نقطه ای اتفاق بیافتد. هرچند معمولا استفاده از نشانگر بین فراخوانی `jl_...` امن است. اما برای اطمینان از زنده نگه داشتن مقادیر از فراخوانی های  `jl_...` ما باید به جولیا بگوییم که ما هنوز یک ارجاع به مقادیر ریشه ای جولیا ([root](https://www.cs.purdue.edu/homes/hosking/690M/p611-fenichel.pdf)) داریم که یک پروسه به نام (GC rooting) است.    ریشه یابی یک مقدار این اطمینان را می دهد که زباله رو(garbage collector) تصادفا یک مقدار را به عنوان مقدار استفاده نشده تشخیص داده و حافظه را ازادسازی کند. این می تواند با استفاده از دستور (macro) `JL_GC_PUSH` اتفاق بیافتد.

    
```c
jl_value_t *ret = jl_eval_string("sqrt(2.0)");
JL_GC_PUSH1(&ret);
// Do something with ret
JL_GC_POP();
```
فراخوانی `JL_GC_POP`  ارجاع های ایجاد شده توسط `JL_GC_PUSH` قبلی را آزاد می کند. توجه کنید که `JL_GC_PUSH` ارجاع ها را در استک C نگه داری میکند. پس می بایستی دقیقا جفت شده باشد با `JL_GC_POP` قبل از این که از scope خارج شود.یعنی قبل از اینکه تابع چیزی را برگرداند یا در غیر اینصورت کنترل جریان بلوکی را که `JL_GC_PUSH` در آن فراخوانی شده بود ترک می کند.

چندین مقدار جولیا می توانند یکجا فشرده (push) شوند با استفاده از دستورهای (macros) `JL_GC_PUSH2` , `JL_GC_PUSH3` , `JL_GC_PUSH4` , `JL_GC_PUSH5` و `JL_GC_PUSH6`.
برای فشرده کردن (push) یک آرایه از مقادیر جولیا می توانید از دستور (macro)  `JL_GC_PUSHARGS`     استفاده کنید که می تواند به شکل زیر  به کار برده شود: 

```c
jl_value_t **args;
JL_GC_PUSHARGS(args, 2); // args can now hold 2 `jl_value_t*` objects
args[0] = some_value;
args[1] = some_other_value;
// Do something with args (e.g. call jl_... functions)
JL_GC_POP();
```
هر scope  فقط یکبار می تواند `JL_GC_PUSH*` را صدا کند.از این رو اگر همه متغیر ها نتوانند یکباره با یکبار فراخوانی `JL_GC_PUSH*` فشرده (push) شوند یا اگر بیش تر از شش متغیر برای فشرده (push) شدن وجود داشته باشد و استفاده از آرایه ای از آرگومان ها قابل انتخاب نباشد  در این صورت می توان از بلوک های داخلی استفاده کرد :

```c
jl_value_t *ret1 = jl_eval_string("sqrt(2.0)");
JL_GC_PUSH1(&ret1);
jl_value_t *ret2 = 0;
{
    jl_function_t *func = jl_get_function(jl_base_module, "exp");
    ret2 = jl_call1(func, ret1);
    JL_GC_PUSH1(&ret2);
    // Do something with ret2.
    JL_GC_POP();    // This pops ret2.
}
JL_GC_POP();    // This pops ret1.
```
اگر لازم است که یک نشانگر به یک متغیر را بین توابع یا scope های بلوک نگه داریم پس غیر ممکن است که از `JL_GC_PUSH*` استفاده کنیم.در این شرایط لازم است که یک ارجاع به متغیر را  در scope جهانی (global) جولیا بسازیم و نگه داری کنیم.یک راه ساده برای اجرای این کار این است که از `IdDict` جهانی (global) که ارجاع را نگه می دارد استفاده کنیم برای جلوگیری از تخلیه تخصیص توسط GC.   هر چند این متد با انواع قابل تغییر (mutable) فقط بدرستی کار می کند.
    
```c
// This functions shall be executed only once, during the initialization.
jl_value_t* refs = jl_eval_string("refs = IdDict()");
jl_function_t* setindex = jl_get_function(jl_base_module, "setindex!");

...

// `var` is the variable we want to protect between function calls.
jl_value_t* var = 0;

...

// `var` is a `Vector{Float64}`, which is mutable.
var = jl_eval_string("[sqrt(2.0); sqrt(4.0); sqrt(6.0)]");

// To protect `var`, add its reference to `refs`.
jl_call3(setindex, refs, var, var);
```
اگر متغیر غیر قابل تغییر (immutable) باشد لازم است تا در بسته (container) قابل تغییر معادل قرارداده شود و یا ترجیحا در یک `RefValue{Any}` قبل از اینکه به `IdDict`. وارد (push) شود. در این روش بسته باید با استفاده از کد C ایجاد و یا پر شود. برای مثال فانکشن `jl_new_struct`. اگر بسته توسط `jl_call*` ایجاد شده باشد پس لازم است که شما نشانگر را مجددا بارگذاری کنید تا در کد C استفاده شود.

```c
// This functions shall be executed only once, during the initialization.
jl_value_t* refs = jl_eval_string("refs = IdDict()");
jl_function_t* setindex = jl_get_function(jl_base_module, "setindex!");
jl_datatype_t* reft = (jl_datatype_t*)jl_eval_string("Base.RefValue{Any}");

...

// `var` is the variable we want to protect between function calls.
jl_value_t* var = 0;

...

// `var` is a `Float64`, which is immutable.
var = jl_eval_string("sqrt(2.0)");

// Protect `var` until we add its reference to `refs`.
JL_GC_PUSH1(&var);

// Wrap `var` in `RefValue{Any}` and push to `refs` to protect it.
jl_value_t* rvar = jl_new_struct(reft, var);
JL_GC_POP();

jl_call3(setindex, refs, rvar, rvar);
```
به GC می تواند اجازه داده بشود تا یک متغیر را از حالت تخصیص خارج کند به وسیله حذف ارجاع به آن از `refs` با استفاده از تابع `delete!` به شرطی که هیچ ارجاع دیگری به متغیر در هیچ جایی نگه داری نشود :
    
```c
jl_function_t* delete = jl_get_function(jl_base_module, "delete!");
jl_call2(delete, refs, rvar);
```
به عنوان یک جایگزین برای موارد خیلی ساده می توان فقط یک بسته جهانی (global) از نوع `Vector{Any}` ایجاد کرد و المنت ها را هر زمان که واجب بود از آن واکشی کند یا حتی می توان یک متغیر جهانی (global) برای هر نشانگر ساخت با استفاده از :
    
```c
jl_set_global(jl_main_module, jl_symbol("var"), var);
```
### بروز رسانی فیلد های اشیای مدیریت شده توسط GC (GC-managed objects)

زباله رو با این فرض اجرا می شود که از هر نسل قدیمی نشانگر های اشیا تا نسل جدید آگاه است. هر زمانی که یک اشاره گر بروز می شود این فرض از بین می رود و باید به زباله رو توسط تابع`jl_gc_wb` (مانع نوشتن) (write barrier)  مانند زیر اطلاع داده شود :

```c
jl_value_t *parent = some_old_value, *child = some_young_value;
((some_specific_type*)parent)->field = child;
jl_gc_wb(parent, child);
```
در حالت عادی غیر ممکن است  که پیش بینی کنید چه مقدارهایی در زمان اجرا قدیمی خواهد بود. پس مانع نوشتن (write barrier) باید پس از همه ذخیره سازی های واضح درج شود. یک استثنا قابل توجه این است که اگر شی `parent` تنها تخصیص داده شده باشد و زباله روبی از آن پس اجرا نشده باشد. به یاد داشته باشید که اغلب توابع `jl_...` می توانند بعضی وقت ها زباله روبی را فرا بخوانند.

مانع نوشتن (write barrier) همچنین برای آرایه هایی از نشانگر ها هنگام بروز رسانی داده هایشان به صورت مستقیم لازم است.
برای مثال :

```c
jl_array_t *some_array = ...; // e.g. a Vector{Any}
void **data = (void**)jl_array_data(some_array);
jl_value_t *some_value = ...;
data[0] = some_value;
jl_gc_wb(some_array, some_value);
```
### دستکاری کردن زباله روب

چند تابع برای کنترل GC وجود دارد. در حالت های نرمال این ها الزامی نیستند.


| Function             | Description                                  |
|:-------------------- |:-------------------------------------------- |
| `jl_gc_collect()`    | اجبار به اجرای GC                           
| `jl_gc_enable(0)`    | از کار انداختن GC و بازگرداندن state قبلی به عنوان عدد صحیح
| `jl_gc_enable(1)`    | به کار انداختن GC و بازگرداندن state قبلی به عنوان عدد صحیح
| `jl_gc_is_enabled()` | بازگرداندن state فعلی به عنوان عدد صحیح 

## کار با آرایه ها
    
جولیا و C می توانند بدون کپی کردن داده های آرایه را به اشتراک بگذارند. مثال بعدی نشان می دهد که این چگونه کار می کند.

آرایه های جولیا در C با نوع داده `jl_array_t*` نشان داده می شوند. به صورت پایه ای `jl_array_t` ساختاری است که شامل این موارد می شود:

  * اطلاعات در مورد نوع دیتا
  * یک نشانگر به بلوک دیتا
  * اطلاعات در مورد اندازه آرایه

برای ساده بودن با یک آرایه یک بعدی شروع میکنیم. ایجاد یک آرایه شامل المنت ها Float64 با طول ده به صورت زیر است :

```c
jl_value_t* array_type = jl_apply_array_type((jl_value_t*)jl_float64_type, 1);
jl_array_t* x          = jl_alloc_array_1d(array_type, 10);
```
 از سوی دیگر اگر شما آرایه را تخصیص داده اید می توانید یک بسته بندی (wrapper) نازک اطراف داده ها تولید کنید :

```c
double *existingArray = (double*)malloc(sizeof(double)*10);
jl_array_t *x = jl_ptr_to_array_1d(array_type, existingArray, 10, 0);
```
آخرین آرگومان یک شاخص منطقی (boolean) است که نشان می دهد آیا جولیا باید مالکیت داده را داشته باشد یا نه. اگر این آرگومان غیر صفر باشد GC `free` را روی نشانگر داده وقتی که آرایه دیگر ارجاع داده نمی شود صدا می زند.

برای دسترسی به دیتا x می توانیم از `jl_array_data` استفاده کنیم :

```c
double *xData = (double*)jl_array_data(x);
```
حال می توانیم آرایه را پرکنیم :

```c
for(size_t i=0; i<jl_array_len(x); i++)
    xData[i] = i;
```
اکنون یک تابع جولیا را که یک عملیات در جا را روی `x` اجرا می کند صدا می زنیم :
                                   
```c
jl_function_t *func = jl_get_function(jl_base_module, "reverse!");
jl_call1(func, (jl_value_t*)x);
```
با پرینت آرایه می توان مشخص کرد که المنت های `x` رزرو شده اند.

### دسترسی به آرایه های بازگشت شده

اگر یک تابع جولیا یک آرایه را برگرداند مقدار بازگشتی  `jl_eval_string` و  `jl_call` می تواند به یک   `jl_array_t*` شکل  داده شود :
                                 
```c
jl_function_t *func  = jl_get_function(jl_base_module, "reverse");
jl_array_t *y = (jl_array_t*)jl_call1(func, (jl_value_t*)x);
```
اکنون محتوی `y` مانند قبل  قابل دسترسی است  با استفاده از `jl_array_data` مثل همیشه مطمئن شوید که یک ارجاع  به آرایه را وقتی که در حال استفاده است نگه دارید.           

### آرایه های چندبعدی

آرایه های چند بعدی جولیا در حافظه به ترتیب ستون اصلی مرتب می شوند. اینجا کدی قرار دارد که یک آرایه 2 بعدی را ایجاد می کند و به ویژگی های آن دسترسی پیدا دارد :

```c
// Create 2D array of float64 type
jl_value_t *array_type = jl_apply_array_type(jl_float64_type, 2);
jl_array_t *x  = jl_alloc_array_2d(array_type, 10, 5);

// Get array pointer
double *p = (double*)jl_array_data(x);
// Get number of dimensions
int ndims = jl_array_ndims(x);
// Get the size of the i-th dim
size_t size0 = jl_array_dim(x,0);
size_t size1 = jl_array_dim(x,1);

// Fill array with data
for(size_t i=0; i<size1; i++)
    for(size_t j=0; j<size0; j++)
        p[j + size0*i] = i + j;
```
دقت کنید که درحالی که آرایه های جولیا اندیس گذاری پایه یک (i-based indexing) را استفاده می کنند API C ا ندیس گذاری  پایه صفر (0-based indexing) را استفاده می کند. (برای مثال برای صدا زدن `jl_array_dim` ) برای خواندن اصطلاحی (idiomotic) کد C.

## اکسپشن ها
                             
کد جولیا می توانداکسپشن هایی را بدهد . برای مثال  در نظر بگیرید :

```c
jl_eval_string("this_function_does_not_exist()");
```
این فراخوانی به نظر می رسد کاری انجام نمی دهد. اما می توان بررسی کرد  که آیا اکسپشن داده شده است  یا خیر :

```c
if (jl_exception_occurred())
    printf("%s \n", jl_typeof_str(jl_exception_occurred()));
```
اگر شما API C جولیا را از یک زبان که اکسپشن  ها را پشتیانی کند استفاده می کنید ( برای مثال (python  , C , C++ این کار خوبی  است که هر فراخوانی به `libjulia` را به یک تابع دهیم تا چک کند که آیا یک اکسپشن داده شده است یا نه و آن اکسپشن را دوباره به زبان میزبان بدهد.

### ارسال اکسپشن های جولیا

زمان نوشتن توابع قابل فراخوانی جولیا ممکن است لازم شود تا آرگومان ها را اعتبار سنجی کنید و اکسپشن هایی برای نشان دادن خطاها داده شود. یک کنترل نوع (type) معمولی به این شکل است :

```c
if (!jl_typeis(val, jl_float64_type)) {
    jl_type_error(function_name, (jl_value_t*)jl_float64_type, val);
}
```
اکسپشن های عمومی می توانند با استفاده از توابع ایجاد شوند :

```c
void jl_error(const char *str);
void jl_errorf(const char *fmt, ...);
```
`jl_error` یک رشته C را می گیرد و `jl_errorf` مانند `printf` صدا زده می شود :

```c
jl_errorf("argument x = %d is too large", x);
```
که در این مثال `x` یک عدد صحیح در نظر گرفته شده است.
