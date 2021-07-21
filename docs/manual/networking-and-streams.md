# شبکه و مسیرها
جولیا یک رابط قدرتمند برای کار کردن با اشیا مسیری ورودی/خروجی مثل ترمینال‌ها، پایپ‌ها و سوکت‌های TCP فراهم می‌کند. این رابط، اگر چه در سطح دستگاه غیرهمزمان است، اما به صورت همزمان به برنامه‌نویس ارائه می‌شود و معمولا هم توجه به عملیات‌های غیرهمزمان لایه‌های زیرین ضروری نیست. این ویژگی به وسیله‌ی استفاده‌ی مکرر از رشته‌های همکارانه‌ی جولیا
([coroutine](@ref man-tasks))
حاصل می‌شود.

## مسیر پایه‌ای ورودی/خروجی

همه‌ی مسیر‌های جولیا حداقل به شکل یکی از متدهای `read` یا `write` ظاهر می‌شوند که مسیر را به عنوان ورودی اول‌شان می‌گیرند. برای مثال:

```julia
julia> write(stdout, "Hello World");  # suppress return value 11 with ;
Hello World
julia> read(stdin, Char)

'\n': ASCII/Unicode U+000a (category Cc: Other, control)
```


توجه کنید که `write` ۱۱ را خروجی می‌دهد که برابر است با تعداد بایت‌های `Hello World` که  در مسیر `stdout` نوشته شده است ولی این مقدار خروجی به وسیله‌ی `;` کنترل می‌شود.


در اینجا انتر دوباره فشار داده شد تا جولیا خط بعدی را بخواند. حالا، همانطور که در این مثال می‌بینید، `write` داده‌ای را که قرار است بنویسد به عنوان ورودی دومش می‌گیرد در حالی که `read` نوع داده‌ای که قرار است بخواند را به عنوان ورودی دوم می‌گیرد.

برای مثال، برای خواندن یک آرایه ساده از نوع بایت، می‌توانیم این‌گونه عمل کنیم:

```julia
julia> x = zeros(UInt8, 4)
4-element Array{UInt8,1}:
 0x00
 0x00
 0x00
 0x00

julia> read!(stdin, x)
abcd
4-element Array{UInt8,1}:
 0x61
 0x62
 0x63
 0x64
```

هر چند، از آن جا که این کار کمی زحمت دارد، چندین متد مناسب دیگر آماده شده است. برای مثال، کد بالا را می‌توانستیم به این شکل بنویسیم:

```julia
julia> read(stdin, 4)
abcd
4-element Array{UInt8,1}:
 0x61
 0x62
 0x63
 0x64
```

یا اگر می‌خواستیم به جای این کار، کل سطر را بخوانیم:

```julia
julia> readline(stdin)
abcd
"abcd"
```

توجه کنید بسته به تنظیمات ترمینال شما، TTY شما ممکن است خط بافر شده باشد و بنابراین ممکن است یک انتر اضافی قبل از ارسال داده‌ها به جولیا لازم باشد.


برای خواندن هر خط از `stdin` می‌توانید از `eachline` استفاده کنید:

```julia
for line in eachline(stdin)
    print("Found $line")
end
```

یا از `read` اگر می‌خواستید به جای این کار با کاراکتر بخوانید:

```julia
while !eof(stdin)
    x = read(stdin, Char)
    println("Found: $x")
end
```

## ورودی/خروچی متنی

توجه کنید که متد `write` که در بالا به آن اشاره شد روی مسیرهای دودویی عمل می‌کند. به طور خاص، مقادیر هیچ نمایش متنی متعارفی تبدیل نمی‌شود بلکه به شکل زیر نوشته می‌شود:

```julia
julia> write(stdout, 0x61);  # suppress return value 1 with ;
a
```

توجه کنید که `a` به وسیله‌ی تابع `write` در `stdout` نوشته شده است و مقدار خروجی برابر با ‍‍`1` است (زیرا `0x61` یک بایت است).

برای ورودی/خروجی متنی بسته به نیازهای‌تان از متدهای `print` یا `show` استفاده کنید (برای دیدن این دو متد و جزئیات تفاوت بین آن‌ها مستندات را ببینید):

```julia
julia> print(stdout, 0x61)
97
```
برای اطلاعات بیشتر در مورد چگونگی پیاده‌سازی متدهای نمایش به شکل دلخواه
[نمایش دلخواه](@ref man-custom-pretty-printing)
را ببینید.

## ویژگی‌های متنی خروجی IO

گاهی اوقات خروجی IO می تواند از امکان انتقال اطلاعات متنی به متدهای نمایش بهره‌مند شود. شی `IOContext` این بستر را برای به اشتراک گذاشتن فراداده‌ی دلخواه با یک شی IO فراهم می‌کند. برای مثال، `:compact => true` یک پارامتر راهنما به شی IO اضافه می‌کند که روش نمایش مورد استفاده باید خروجی کوتاه‌تری چاپ کند (اگر چنین کاری انجام‌شدنی باشد). برای دیدن لیست ویژگی‌های متعارف مستندات را ببینید)

## کار با فایل

مثل بسیاری از محیط‌های دیگر، جولیا دارای یک تابع `open` است که یک نام فایل می‌گیرد و یک شی `IOStream` برمی‌گرداند که با استفاده از آن می‌توانید از آن فایل بخوانید یا در آن بنویسید. برای مثال اگر ما یک فایل به نام `hello.txt` داشته باشیم که محتوایش `Hello, World!` باشد:


```julia
julia> f = open("hello.txt")
IOStream(<file hello.txt>)

julia> readlines(f)
1-element Array{String,1}:
 "Hello, World!"
```

اگر می‌خواهید در یک فایل چیزی بنویسید، می‌توانید آن را با نشانه‌ی `"w"` باز کنید:

```julia
julia> f = open("hello.txt","w")
IOStream(<file hello.txt>)

julia> write(f,"Hello again.")
12
```

اگر بعد از اجرای کد بالا محتوای فایل `hello.txt` را بررسی کنید، متوجه خواهید شد که خالی است. در واقع هنوز هیچ چیزی بر روی حافظه نوشته نشده است. این به این خاطر است که `IOStream` باید قبل از این که نوشته بر روی حافظه وارد شود، بسته شود:

```julia
julia> close(f)
```

بررسی مجدد `hello.txt` نشان خواهد داد که محتوای آن تغییر کرده است.

باز کردن یک فایل، اعمال یک سری تغییرات بر محتوای آن و سپس بستن آن یک الگوی بسیار مرسوم است. برای راحت‌تر کردن این کار فراخوانی دیگری از `open` وجود دارد که یک تابع را به عنوان ورودی اول خود و اسم فایل را به عنوان ورودی دوم می‌گیرد، فایل را باز می‌کند، تابع را با ورودی فایل مورد نظر فراخوانی می‌کند و سپس دوباره آن را می‌بندد. برای مثال، تابع داده شده:

```julia
function read_and_capitalize(f::IOStream)
    return uppercase(read(f, String))
end
```

می‌توانید به این شکل فرخوانی کنید:

```julia
julia> open(read_and_capitalize, "hello.txt")
"HELLO AGAIN."
```


برای باز کردن `hello.txt` تابع `read_and_capitalize` را روی آن فراخوانی کنید، فایل `hello.txt` را ببندید و محتوایی را که حروف به حروف بزرگ تبدیل شدن را خروجی بگیرید.

برای جلوگیری از تعریف یک تابع مشخص اسم‌دار، می‌توانید از دستور `do` استفاده کنید که یک تابع مخفی مجازی می‌سازد:

```julia
julia> open("hello.txt") do f
           uppercase(read(f, String))
       end
"HELLO AGAIN."
```

## یک مثال ساده از TCP

اجازه دهید سراغ یک مثال ساده از به‌کار‌گیری سوکت‌های TCP برویم.
این ساختار در یک بسته‌ی کتابخانه‌‌ی استاندارد به نام `Sockets` موجود است.
ابتدا بیایید یک سرور ساده بسازیم:


```
julia> using Sockets

julia> @async begin
           server = listen(2000)
           while true
               sock = accept(server)
               println("Hello World\n")
           end
       end
Task (runnable) @0x00007fd31dc11ae0
```

برای کسانی که با API سوکت Unix آشنایی دارند، نام متد آشنا خواهد بود، اگر چه استفاده‌ی آن‌ها قدری ساده‌تر از API خام سوکت Unix است. فراخوانی اول `listen` سروری خواهد ساخت که آماده‌ی دریافت کانکشن ورودی روی یک پورت مشخص، (۲۰۰۰) در این مثال، 
است. همچنین ممکن است همین تابع برای ساختن انواع مختلف سرور استفاده شود:


```julia
julia> listen(2000) # Listens on localhost:2000 (IPv4)
Sockets.TCPServer(active)

julia> listen(ip"127.0.0.1",2000) # Equivalent to the first
Sockets.TCPServer(active)

julia> listen(ip"::1",2000) # Listens on localhost:2000 (IPv6)
Sockets.TCPServer(active)

julia> listen(IPv4(0),2001) # Listens on port 2001 on all IPv4 interfaces
Sockets.TCPServer(active)

julia> listen(IPv6(0),2001) # Listens on port 2001 on all IPv6 interfaces
Sockets.TCPServer(active)

julia> listen("testsocket") # Listens on a UNIX domain socket
Sockets.PipeServer(active)

julia> listen("\\\\.\\pipe\\testsocket") # Listens on a Windows named pipe
Sockets.PipeServer(active)
```
توجه کنید که نوع خروجی فراخوانی آخر متفاوت است. این به خاطر این است که این سرور به TCP گوش نمی‌دهد. بلکه به یک سوکت تحت عنوان پایپ (ویندوز) یا UNIX گوش می‌دهد. همچنین توجه کنید که ویندوزی که تحت عنوان پایپ است یک الگوی مشخص دارد  مثل اسم پیشوند (`\\.\pipe\`) که به طور یکتا
[نوع فایل](https://docs.microsoft.com/windows/desktop/ipc/pipe-names).
را مشخص می‌کند.
The difference between TCP and named pipes or
UNIX domain sockets is subtle and has to do with the `accept` and `connect`
methods. The `accept` method retrieves a connection to the client that is connecting on
the server we just created, while the `connect` function connects to a server using the
specified method. The `connect` function takes the same arguments as `listen`,
so, assuming the environment (i.e. host, cwd, etc.) is the same you should be able to pass the same
arguments to `connect` as you did to listen to establish the connection. So let's try that
out (after having created the server above):

```julia
julia> connect(2000)
TCPSocket(open, 0 bytes waiting)

julia> Hello World
```

As expected we saw "Hello World" printed. So, let's actually analyze what happened behind the
scenes. When we called `connect`, we connect to the server we had just created. Meanwhile,
the accept function returns a server-side connection to the newly created socket and prints "Hello
World" to indicate that the connection was successful.

A great strength of Julia is that since the API is exposed synchronously even though the I/O is
actually happening asynchronously, we didn't have to worry about callbacks or even making sure that
the server gets to run. When we called `connect` the current task waited for the connection
to be established and only continued executing after that was done. In this pause, the server
task resumed execution (because a connection request was now available), accepted the connection,
printed the message and waited for the next client. Reading and writing works in the same way.
To see this, consider the following simple echo server:

```
julia> @async begin
           server = listen(2001)
           while true
               sock = accept(server)
               @async while isopen(sock)
                   write(sock, readline(sock, keep=true))
               end
           end
       end
Task (runnable) @0x00007fd31dc12e60

julia> clientside = connect(2001)
TCPSocket(RawFD(28) open, 0 bytes waiting)

julia> @async while isopen(clientside)
           write(stdout, readline(clientside, keep=true))
       end
Task (runnable) @0x00007fd31dc11870

julia> println(clientside,"Hello World from the Echo Server")
Hello World from the Echo Server
```

As with other streams, use `close` to disconnect the socket:

```julia
julia> close(clientside)
```

## Resolving IP Addresses

One of the `connect` methods that does not follow the `listen` methods is
`connect(host::String,port)`, which will attempt to connect to the host given by the `host` parameter
on the port given by the `port` parameter. It allows you to do things like:

```julia
julia> connect("google.com", 80)
TCPSocket(RawFD(30) open, 0 bytes waiting)
```

At the base of this functionality is `getaddrinfo`, which will do the appropriate address
resolution:

```julia
julia> getaddrinfo("google.com")
ip"74.125.226.225"
```

## Asynchronous I/O


All I/O operations exposed by `Base.read` and `Base.write` can be performed
asynchronously through the use of [coroutines](@ref man-tasks). You can create a new coroutine to
read from or write to a stream using the `@async` macro:

```julia
julia> task = @async open("foo.txt", "w") do io
           write(io, "Hello, World!")
       end;

julia> wait(task)

julia> readlines("foo.txt")
1-element Array{String,1}:
 "Hello, World!"
```

It's common to run into situations where you want to perform multiple asynchronous operations
concurrently and wait until they've all completed. You can use the `@sync` macro to cause
your program to block until all of the coroutines it wraps around have exited:

```julia
julia> using Sockets

julia> @sync for hostname in ("google.com", "github.com", "julialang.org")
           @async begin
               conn = connect(hostname, 80)
               write(conn, "GET / HTTP/1.1\r\nHost:$(hostname)\r\n\r\n")
               readline(conn, keep=true)
               println("Finished connection to $(hostname)")
           end
       end
Finished connection to google.com
Finished connection to julialang.org
Finished connection to github.com
```
