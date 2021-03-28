# شروع به کار

نصب جولیا ساده است و می‌توانید این کار را با استفاده از باینری‌های از پیش کامپایل شده و یا با کامپایل کد انجام دهید.
با دنبال کردن دستورالعمل های [این صفحه](https://julialang.org/downloads/) جولیا را بارگیری و نصب کنید.

ساده ترین راه برای یادگیری و آزمایش کار با جولیا، اجرای خود جولیا REPL (مخفف: read-eval-print loop) است. برای اجرای آن یا بر روی آن دابل کلیک کنید و یا از خط فرمان سیستم `julia` را صدا بزنید.

```@eval
io = IOBuffer()
Base.banner(io)
banner = String(take!(io))
import Markdown
Markdown.parse("```\n\$ julia\n\n$(banner)\njulia> 1 + 2\n3\n\njulia> ans\n3\n```")
```
برای خروج از این محیط تعاملی می‌توانید از ترکیب `CTRL-D`(گرفتن کلید کنترل و سپس فشردن کلید `D`) یا فرمان `()exit` را وارد کنید. جولیا پس از اجرا شدن در محیط تعاملی یک بنر را نمایش می‌دهد و در پایین آن منتظر ورودی کاربر می‌شود. زمانی که کاربر یک دستور کامل را به جولیا بدهد(مثل `2+1`) و سپس کلید `enter` را فشار دهد، محیط تعاملی آن را بررسی کرده و مقدار آن را نمایش می‌دهد. اگر در محیط تعاملی پس از دستور خود سمیکالن `;` بگذارید و کلید `enter` را بفشارید، مقدار کد شما(خروجی بیان) نمایش داده نمی‌شود. متغیر `ans` مقدار آخرین دستور شما را نشان می‌دهد و فرقی ندارد که قبلا نمایش داده شده یا از نمایش آن بوسیله `;` جلوگیری شده است. متغیر `ans` فقط در محیط تعاملی کار می‌کند و در صورت های دیگری که کد جولیا را اجرا می‌کنید کارایی ندارد.

برای ارزیابی عملکرد یک سورس کد در فایلی با نام `file.jl` باید `include("file.jl")` را بنویسید.

اگر می‌خواهید کد درون فایل را به صورت غیر تعاملی اجرا کنید باید در خط فرمان خود فایل را به عنوان اولین آرگومان(ورودی) دستور `julia` قرار دهید:

```
$ julia script.jl arg1 arg2...
```

همانطور که در مثال بالا مشاهده می‌کنید، آرگومان‌های بعدی خط فرمان به عنوان آرگومان‌های برنامه `script.jl` تفسیر می‌شوند و  به ثابت جهانی `ARGS` پاس داده می‌شوند. نام اسکریپت نیز خودش به عنوان ثابت جهانی `PROGRAM_FILE` پاس داده می‌شود. توجه داشته باشید زمانی که دستور `julia` در خط فرمان با عبارت `e-` اجرا می‌شود نیز `ARGS` مقداردهی می‌شود اما مقدار `PROGRAM_FILE` خالی می‌ماند. به عنوان مثال، برای فقط چاپ آرگومان‌های داده شده به یک اسکریپت، شما می‌توانید این کار را انجام دهید:

```
$ julia -e 'println(PROGRAM_FILE); for x in ARGS; println(x); end' foo bar

foo
bar
```

یا می‌توانید کد را درون اسکریپتی قرار داده سپس آن را اجرا کنید:

```
$ echo 'println(PROGRAM_FILE); for x in ARGS; println(x); end' > script.jl
$ julia script.jl foo bar
script.jl
foo
bar
```

نماد `--` می‌تواند میان آرگومان های اختصاص داده شده به اسکریپت و آرگومان های اختصاص داده شده به جولیا تفاوت ایجاد کند:

```
$ julia --color=yes -O -- foo.jl arg1 arg2..
```

برای اطلاعات بیشتر درباره نوشتن اسکریپت‌های جولیا به [اسکریپت نویسی](faq.html#scripting) مراجعه کنید.

جولیا با هر دو آرگومان `p-` یا `machine-file--` می‌تواند در حالت پردازش موازی شروع به کار کند. `p n-` به اندازه `n‍` کارگر اضافه اجرا می‌کند در حالی که `machine-file file--` به ازای هر خط از فایل `file` یک کارگر را اجرا می‌کند. ماشین های تعریف شده برای `file` باید از طریق یک ورود `ssh` بدون نیاز به رمز در دسترس قرار داشته باشند که جولیا نیز باید در همان مکان به عنوان میزبان فعلی نصب شده باشد. هر ماشینی که تعریف می‌شود فرم `[count*][user@]host[:port] [bind_addr[:port]]` را می‌گیرد. پیش فرض `user` همان کاربر جاری سیستم و `port` همان پورت استاندارد ssh است. `count` میزان کارگرهایی است که بر روی گره ایجاد می‌شوند که مقدار پیشفرض آن یک است. آرگومان اختیاری `bind-to bind_addr[:port]` آدرس آی پی و پورت دیگر کارگرها برای ارتباط با این کارگر را مشخص می‌کند.

اگر کدی دارید که می‌خواهید هر زمان که جولیا اجرا می‌شود آن هم اجرا شود، کافیست آن را در `julia/config/startup.jl./~` قرار دهید:

```
$ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.julia/config/startup.jl
$ julia
Greetings! 你好! 안녕하세요?

...
```

توجه داشته باشید که اگرچه زمانی که برای اولین بار جولیا را اجرا کنید باید یک دایرکتوری `~/.julia` ایجاد شده باشد، ممکن است لازم باشد که پوشه `~/.julia/config` و فایل `~/.julia/config/startup.jl` را خودتان ایجاد کنید تا بتوانید از آن‌ها استفاده کنید.

راه های مختلفی برای اجرای کد جولیا وجود دارد و شبیه گزینه هایی هستند که برای برنامه های پرل(`perl`) و روبی(`ruby`) ارائه شده اند:

```
julia [switches] -- [programfile] [args...]
```

|سوئیچ                                 |توضیحات|
|:---                                   |:---|
|&lrm;`-v`, `--version`                 |اطلاعات نسخه را نمایش می‌دهد|
|&lrm;`-h`, `--help`                          |گزینه‌های خط فرمان را نمایش می‌دهد|
|&lrm;`--project[={<dir>\|@.}]`              |Set <dir\> as the home project/environment. The default @. option will search through parent directories until a Project.toml or JuliaProject.toml file is found.|
|&lrm;`-J`, `--sysimage <file>`&lrm;             |Start up with the given system image file|
|&lrm;`-H`, `--home <dir>`&lrm;                   |Set location of `julia` executable|
|&lrm;`--startup-file={yes\|no}`             |Load `~/.julia/config/startup.jl`|
|&lrm;`--handle-signals={yes\|no}`           |Enable or disable Julia's default signal handlers|
|&lrm;`--sysimage-native-code={yes\|no}`     |Use native code from system image if available|
|&lrm;`--compiled-modules={yes\|no}`         |Enable or disable incremental precompilation of modules|
|&lrm;`-e`, `--eval <expr>`&lrm;                  |مقدار &lrm;`<expr>`&lrm; را محاسبه می‌کند|
|&lrm;`-E`, `--print <expr>`&lrm;                 |Evaluate `<expr>`&lrm; and display the result|
|&lrm;`-L`, `--load <file>`&lrm;                  |Load `<file>`&lrm; immediately on all processors|
|&lrm;`-t`, `--threads {N\|auto}`            |Enable N threads; `auto` currently sets N to the number of local CPU threads but this might change in the future|
|&lrm;`-p`, `--procs {N\|auto}`              |Integer value N launches N additional local worker processes; `auto` launches as many workers as the number of local CPU threads (logical cores)|
|&lrm;`--machine-file <file>`&lrm;                |Run processes on hosts listed in `<file>`&lrm;|
|&lrm;`-i`                                   |Interactive mode; REPL runs and `isinteractive()` is true|
|&lrm;`-q`, `--quiet`                        |Quiet startup: no banner, suppress REPL warnings|
|&lrm;`--banner={yes\|no\|auto}`             |Enable or disable startup banner|
|&lrm;`--color={yes\|no\|auto}`              |Enable or disable color text|
|&lrm;`--history-file={yes\|no}`             |Load or save history|
|&lrm;`--depwarn={yes\|no\|error}`           |Enable or disable syntax and method deprecation warnings (`error` turns warnings into errors)|
|&lrm;`--warn-overwrite={yes\|no}`           |Enable or disable method overwrite warnings|
|&lrm;`-C`, `--cpu-target <target>`&lrm;          |Limit usage of CPU features up to `<target>`&lrm;; set to `help` to see the available options|
|&lrm;`-O`, `--optimize={0,1,2,3}`           |Set the optimization level (default level is 2 if unspecified or 3 if used without a level)|
|&lrm;`-g`, `-g <level>`&lrm;                     |Enable / Set the level of debug info generation (default level is 1 if unspecified or 2 if used without a level)|
|&lrm;`--inline={yes\|no}`                   |Control whether inlining is permitted, including overriding `@inline` declarations|
|&lrm;`--check-bounds={yes\|no}`             |Emit bounds checks always or never (ignoring declarations)|
|&lrm;`--math-mode={ieee,fast}`              |Disallow or enable unsafe floating point optimizations (overrides @fastmath declaration)|
|&lrm;`--code-coverage={none\|user\|all}`    |Count executions of source lines|
|&lrm;`--code-coverage`                      |equivalent to `--code-coverage=user`|
|&lrm;`--track-allocation={none\|user\|all}` |Count bytes allocated by each source line|
|&lrm;`--track-allocation`                   |equivalent to `--track-allocation=user`|

---
**سازگاری Julia 1.1**

در جولیا 1.0 ، گزینه پیش فرض `--project=@.` از پوشه root مخزن Git برای فایل `Project.toml` جستجو نمی‌کرد. از جولیا 1.1 به بعد، می‌کند.

---

## منابع

لیستی از منابع یادگیری مفید برای کمک به کاربران جدید را می توانید در صفحه [یادگیری](https://julialang.org/learning/) وب سایت اصلی جولیا بیابید.
