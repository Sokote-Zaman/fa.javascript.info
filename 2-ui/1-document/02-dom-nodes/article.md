libs:
  - d3
  - domtree

---

# درخت DOM

ستون فقرات سند html تگ‌ها (tag) هستند.

طبق مدل شیءگرای سند (Document Object Model یا DOM)، هر تگ HTML یک شیء است. تگ‌های داخلی "فرزندانِ" تگ‌های دربرگیرنده هستند. متن داخل تگ نیز یک شیء است.

همه این اشیاء با استفاده از جاوااسکریپت قابل دسترس هستند و ما می‌توانیم از آن‌ها برای تغییر صفحه استفاده کنیم.

به عنوان مثال، `document.body` شیءای است که تگ `<body>` را نشان می‌دهد.

با اجرای این کد، `<body>` به مدت 3 ثانیه قرمز می‌شود:

```js run
document.body.style.background = 'red'; // پس زمینه را قرمز کن

setTimeout(() => document.body.style.background = '', 3000); // برگرد
```

در اینجا ما از `style.background` برای تغییر رنگ پس زمینه `document.body` استفاده کردیم، اما ویژگی‌های زیادِ دیگری وجود دارد، مانند:

- `innerHTML` -- HTML محتویاتِ گره
- `offsetWidth` -- عرض گره (بر حسب پیکسل)
- غیره و ...

به زودی راه‌های بیشتری برای دستکاری DOM یاد خواهیم گرفت، اما ابتدا باید ساختار آن را بدانیم.

## نمونه ای از DOM

بیایید با سند ساده زیر شروع کنیم:

```html run no-beautify
<!DOCTYPE HTML>
<html>
<head>
  <title>درباره گوزن شمالی</title>
</head>
<body>
  حقیقت درباره گوزن شمالی.
</body>
</html>
```

شیء DOM سند HTML را به عنوان یک ساختار درختی از تگ‌ها نشان می‌دهد. اینگونه به نظر می‌رسد:

<div class="domtree"></div>

<script>
let node1 = {"name":"HTML","nodeType":1,"children":[{"name":"HEAD","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"\n  "},{"name":"TITLE","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"درباره گوزن شمالی"}]},{"name":"#text","nodeType":3,"content":"\n"}]},{"name":"#text","nodeType":3,"content":"\n"},{"name":"BODY","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"\n  حقیقت درباره گوزن شمالی.\n\n\n"}]}]}

drawHtmlTree(node1, 'div.domtree', 690, 320);
</script>

```online
در تصویر بالا، می‌توانید روی گره‌های عنصر کلیک کنید و فرزندان آن‌ها باز می‌شوند.
```

هر گره‌ی درخت یک شیء است.

تگ‌ها *گره‌های عنصر* (عناصر) هستند و ساختار درختی را تشکیل می‌دهند: `<html>` در ریشه است، سپس `<head>` و `<body>` فرزندان آن هستند و ....

متن داخل عناصر *گره‌های متنی (text nodes)* را تشکیل می‌دهد که با عنوان `text#` برچسب‌گذاری شده‌اند. یک گره متنی فقط شامل یک رشته است. ممکن است فرزندی نداشته باشد و همیشه برگ درخت باشد.

به عنوان مثال تگِ `<title>` متن `"درباره‌ی گوزن شمالی"` را دارد.

لطفاً به کاراکترهای خاص در گره‌های متن توجه کنید:

- یک خط جدید: `↵` (در جاوااسکریپت به صورت `n\` شناخته می‌شود.)
- یک فاصله(space) : `␣`

فاصله ها و خطوط جدید کاراکترهای کاملاً معتبری هستند، مانند حروف و اعداد. آن‌ها گره‌های متنی را تشکیل می‌دهند و بخشی از DOM می‌شوند. بنابراین، برای مثال، در مثال بالا، تگ `<head>` حاوی چند فاصله قبل از `<title>` است، و آن متن به یک گره `text#` تبدیل می‌شود (که فقط حاوی یک خط جدید و چند فاصله است).

فقط دو استثناء سطح بالا وجود دارد:
1. فاصله‌ها و خطوط جدید قبل از `<head>` به دلایل تاریخی نادیده گرفته می‌شوند.
2. اگر چیزی را بعد از `</body>` قرار دهیم، در پایان به طور خودکار به داخل `body` منتقل می‌شود، زیرا مشخصات HTML ایجاب می‌کند که تمام محتوا باید داخل `<body>` باشد. بنابراین هیچ فاصله‌ای بعد از `</body>` وجود ندارد.

در موارد دیگر همه چیز سر راست است -- اگر در سند فاصله وجود داشته باشد(دقیقاً مانند هر کاراکتری)، آن‌ها به گره‌های متنی در DOM تبدیل می‌شوند، و اگر آنها را حذف کنیم، وجود نخواهند داشت.

در اینجا هیچ گره متنی فقط-فاصله وجود ندارد:

```html no-beautify
<!DOCTYPE HTML>
<html><head><title>درباره‌ی گوزن شمالی</title></head><body>حقیقت در مورد گوزن شمالی.</body></html>
```

<div class="domtree"></div>

<script>
let node2 = {"name":"HTML","nodeType":1,"children":[{"name":"HEAD","nodeType":1,"children":[{"name":"TITLE","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"About elk"}]}]},{"name":"BODY","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"The truth about elk."}]}]}

drawHtmlTree(node2, 'div.domtree', 690, 210);
</script>

```smart header="فاصله‌ها در شروع/پایان رشته و گره‌های متنی فقط-فاصله معمولاً در ابزارها پنهان می‌شوند"
ابزارهای مرورگر (به زودی پوشش داده خواهد شد) که با DOM کار می‌کنند معمولاً فاصله‌ها را در ابتدا/انتهای متن و گره‌های متنِ خالیِ بین تگ‌ها (خطوط جدید)  را نشان نمی‌دهند.

ابزارهای توسعه دهنده(ابزارهای مرورگر) از این طریق فضای صفحه را حفظ می‌کنند.

در ادامه‌ی تصاویر DOM ، گاهی اوقات زمانی که نامربوط هستند آن‌ها را حذف می‌کنیم. چنین فضاهایی معمولاً بر نحوه نمایش سند تأثیر نمی‌گذارد.
```

## تصحیح خودکار

اگر مرورگر با HTML نادرست مواجه شود، هنگام ساخت DOM به طور خودکار آن را تصحیح می‌کند.

به عنوان مثال، تگ بالا همیشه `<html>` است. حتی اگر در سند وجود نداشته باشد، در DOM وجود خواهد داشت، زیرا مرورگر آن را ایجاد می‌کند. `<body>` هم همینطور.

به عنوان مثال، اگر فایل HTML تنها کلمه `"Hello"` باشد، مرورگر آن را درون `<html>` و `<body>` می‌پیچد و `<head>` ضروری را اضافه می‌کند و DOM اینطور خواهد بود:


<div class="domtree"></div>

<script>
let node3 = {"name":"HTML","nodeType":1,"children":[{"name":"HEAD","nodeType":1,"children":[]},{"name":"BODY","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"Hello"}]}]}

drawHtmlTree(node3, 'div.domtree', 690, 150);
</script>

هنگام تولید DOM، مرورگرها به طور خودکار خطاهای سند را پردازش می‌کنند، تگ‌ها را می‌بندند و غیره.

سندی با تگ‌های بسته نشده:

```html no-beautify
<p>سلام
<li>مامان
<li>و
<li>بابا
```

...وقتی مرورگر تگ‌ها را می‌خواند و قسمت‌های از دست رفته را بازیابی می‌کند، به یک DOM معمولی تبدیل می‌شود:

<div class="domtree"></div>

<script>
let node4 = {"name":"HTML","nodeType":1,"children":[{"name":"HEAD","nodeType":1,"children":[]},{"name":"BODY","nodeType":1,"children":[{"name":"P","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"Hello"}]},{"name":"LI","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"Mom"}]},{"name":"LI","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"and"}]},{"name":"LI","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"Dad"}]}]}]}

drawHtmlTree(node4, 'div.domtree', 690, 360);
</script>

````warn header="جداول همیشه تگ `<tbody>` دارند."
یک "مورد خاص" جالب، جداول است. بر اساس مشخصات DOM، آنها باید تگ `<tbody>` داشته باشند، اما متن HTML ممکن است آن را از قلم انداخته باشد. سپس مرورگر به صورت خودکار `<tbody>` را در DOM ایجاد می‌کند.

برای HTML:

 ```html no-beautify
 <table id="table"><tr><td>1</td></tr></table>
 ```

ساختار DOM اینطور خواهد بود:
<div class="domtree"></div>

<script>
let node5 = {"name":"TABLE","nodeType":1,"children":[{"name":"TBODY","nodeType":1,"children":[{"name":"TR","nodeType":1,"children":[{"name":"TD","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"1"}]}]}]}]};

drawHtmlTree(node5,  'div.domtree', 600, 200);
</script>

می‌بینی؟ `<tbody>` از ناکجاآباد ظاهر شد. هنگام کار با جداول باید این را در نظر داشته باشیم تا از غافلگیری جلوگیری کنیم.
````

## انواع دیگر گره‌ها

علاوه بر عناصر و گره‌های متن، انواع دیگر گره‌ها نیز وجود دارد.

به عنوان مثال، کامنت‌ها:

```html
<!DOCTYPE HTML>
<html>
<body>
  حقیقت در مورد گوزن.
  <ol>
    <li>گوزن شمالی باهوش است</li>
*!*
    <!-- comment -->
*/!*
    <li>...و حیوانی حیله‌گر!</li>
  </ol>
</body>
</html>
```

<div class="domtree"></div>

<script>
let node6 = {"name":"HTML","nodeType":1,"children":[{"name":"HEAD","nodeType":1,"children":[]},{"name":"BODY","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"\n  حقیقت در مورد گوزن.\n  "},{"name":"OL","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"\n    "},{"name":"LI","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"گوزن شمالی باهوش است"}]},{"name":"#text","nodeType":3,"content":"\n    "},{"name":"#comment","nodeType":8,"content":"comment"},{"name":"#text","nodeType":3,"content":"\n    "},{"name":"LI","nodeType":1,"children":[{"name":"#text","nodeType":3,"content":"...و حیوانی حیله‌گر!"}]},{"name":"#text","nodeType":3,"content":"\n  "}]},{"name":"#text","nodeType":3,"content":"\n\n\n"}]}]};

drawHtmlTree(node6, 'div.domtree', 690, 500);
</script>

ما می‌توانیم در اینجا یک نوع گره درختی جدید ببینیم -- *گره comment*، با برچسب `comment#`، بین دو گره متن.

ممکن است فکر کنیم -- چرا یک comment به DOM اضافه شده است؟ به هیچ وجه بر نمایش بصری تأثیر نمی‌گذارد. اما یک قانون وجود دارد - اگر چیزی در HTML است، باید در درخت DOM نیز باشد.

**همه چیز در HTML، حتی comments، بخشی از DOM می‌شود.**

حتی دستور `<...DOCTYPE!>` در همان ابتدای HTML نیز یک گره DOM است. درست قبل از `<html>` در درخت DOM قرار دارد. تعداد کمی از مردم در مورد آن می‌دانند. ما قرار نیست به آن دست بزنیم، حتی آن را روی نمودارها نمی‌کشیم، اما وجود دارد..

شی `document` که کل سند را نشان می‌دهد، به طور رسمی، یک گره DOM نیز هست.

12 نوع گره وجود دارد. در عمل ما معمولا با 4 مورد از آنها کار می‌کنیم [12 node types](https://dom.spec.whatwg.org/#node) :

1. `document` -- "DOM" نقطه‌ی ورود به
2. (گره‌های عنصر) -- بلوک‌های ساختار درختی , HTML تگ‌های 
3. حاوی متن -- (گره‌های متن)
4. (کامنت‌ها) -- بخواند DOM گاهی اوقات می‌توانیم اطلاعات را در آنجا قرار دهیم، نشان داده نمی‌شود، اما جاوااسکریپت می‌تواند آن را از

## خودتان آن را ببینید

برای دیدن ساختار Dom به صورت بی‌وقفه (real-time)، امتحان کنید [Live DOM Viewer](http://software.hixie.ch/utilities/js/live-dom-viewer/). فقط در سند تایپ کنید و  درجا به عنوان یک DOM نشان داده خواهد شد.

راه دیگر برای کشف DOM استفاده از ابزارهای توسعه دهنده مرورگر است. در واقع، این همان چیزی است که ما هنگام توسعه استفاده می‌کنیم.

برای انجام این کار،این صفحه را باز کنید [elk.html](elk.html), ابزارهای توسعه دهنده مرورگر را فعال کنید و به زبانه  Elements بروید.

باید شبیه به این باشه:

![](elk.svg)

می‌توانید DOM را ببینید، روی عناصر کلیک کنید، جزئیات آنها را ببینید و غیره.

لطفاً توجه داشته باشید که ساختار DOM در ابزارهای توسعه دهنده ساده شده است. گره‌های متن فقط به صورت متن نشان داده می‌شوند. و هیچ گره متنی "خالی" (فقط فاصله) وجود ندارد. این خوب است، زیرا اغلب ما به گره‌های عنصر علاقه‌مندیم..

با کلیک روی دکمه‌ی <span class="devtools" style="background-position:-328px -124px"></span> در گوشه سمت چپ بالا می‌توانیم با استفاده از ماوس (یا سایر دستگاه‌های اشاره‌گر) یک گره از صفحه وب انتخاب کرده و آن را «بازرسی (inspect)» کنیم (در زبانه Elements به آن scroll کنید). وقتی یک صفحه HTML بزرگ (و DOM بزرگ مربوطه) داریم و می‌خواهیم جای یک عنصر خاص را در آن ببینیم، عالی عمل می‌کند.

راه دیگر برای انجام این کار فقط کلیک راست بر روی یک صفحه وب و انتخاب "Inspect" در منوی context است.

![](inspect.svg)

در قسمت سمت راست ابزارها زیر زبانه‌های زیر وجود دارد:
- **Styles** -- می‌توانیم CSS را ببینیم که قانون به قانون بر عنصر فعلی اعمال می‌شود، از جمله قوانین داخلی (خاکستری رنگ). تقریباً همه چیز را می‌توان در جای خود ویرایش کرد، از جمله ابعاد/حاشیه های بیرونی/حاشیه‌های داخلی از طریق جعبه‌ی زیرین.
- **Computed** -- برای مشاهده CSS اعمال شده بر روی عنصر توسط ویژگی: برای هر ویژگی می‌توانیم قانونی که به آن می‌دهد را ببینیم (از جمله وراثت CSS و از این دست موارد).
- **Event Listeners** -- برای دیدن event listener متصل به عناصر DOM (ما آن‌ها را در قسمت بعدی آموزش پوشش خواهیم داد).
- ...و غیره.

بهترین راه برای مطالعه آنها کلیک کردن در داخل آنها است. اکثر مقادیر درجا قابل ویرایش هستند.

## تعامل با کنسول

همانطور که ما روی DOM کار می‌کنیم، ممکن است بخواهیم جاوا اسکریپت را نیز روی آن اعمال کنیم. مانند: یک گره بگیرید و کدی را برای تغییر آن اجرا کنید تا نتیجه را ببینید. در اینجا چند نکته برای جابجایی بین زبانه Elements و کنسول وجود دارد.

برای شروع:

1. اولین `<li>` را در زبانه Elements انتخاب کنید.
2. بزنید `key:Esc` -- کنسول درست زیر زبانه Elements باز می شود.

اکنون آخرین عنصر انتخاب شده به عنوان `0$` در دسترس است، عنصر انتخاب شده قبلی `1$` و غیره است.

ما می‌توانیم دستورات را روی آنها اجرا کنیم. به عنوان مثال <span dir="ltr">`$0.style.background = 'red'`</span>، آیتمِ لیست انتخاب شده را قرمز می‌کند، مانند این:

![](domconsole0.svg)

به این ترتیب می‌توان یک گره از Elements را در کنسول دریافت کرد.

یک راه برگشت هم هست اگر متغیری وجود دارد که به یک گره DOM ارجاع می‌دهد، می‌توانیم از دستور `inspect(node)` در کنسول استفاده کنیم تا آن را در قسمت Elements ببینیم.

یا فقط می‌توانیم گره DOM را در کنسول خروجی بگیریم و "درجا" کاوش کنیم، مانند `document.body` در زیر:

![](domconsole1.svg)

این البته برای اهداف اشکال‌زدایی است. از فصل بعدی با استفاده از جاوااسکریپت به DOM دسترسی پیدا می‌کنیم و آن را اصلاح می‌کنیم.

ابزارهای توسعه دهنده مرورگر کمک بزرگی در توسعه هستند: ما می‌توانیم DOM را کاوش کنیم، چیزهایی را امتحان کنیم و ببینیم چه مشکلی پیش می‌آید.

## خلاصه

یک سند HTML/XML داخل مرورگر به عنوان درخت DOM نشان داده می‌شود.

- تگ‌ها به گره‌های عنصر تبدیل می‌شوند و ساختار را تشکیل می‌دهند.
- متن به گره‌های متنی تبدیل می‌شود.
- ... و غیره، همه چیز در HTML جایگاه خود را در DOM دارد، حتی کامنت‌ها.

ما می‌توانیم از ابزارهای توسعه دهنده برای بازرسی (Inspect) DOM و اصلاح آن به صورت دستی استفاده کنیم.

در اینجا به اصول اولیه، پرکاربردترین و مهم‌ترین اقدامات برای شروع پرداختیم. در <https://developers.google.com/web/tools/chrome-devtools> اسناد گسترده‌ای در مورد ابزارهای برنامه نویس Chrome وجود دارد. بهترین راه برای یادگیری ابزارها این است که آن‌ها امتحان کنید، منوها را بخوانید: اکثر گزینه‌ها واضح هستند. بعداً، وقتی آنها را به طور کلی شناختید، اسناد را بخوانید و بقیه را انتخاب کنید.

گره‌های DOM دارای ویژگی‌ها و روش‌هایی هستند که به ما امکان می‌دهند بین آن‌ها پیمایش کنیم، آن‌ها را اصلاح کنیم، در صفحه حرکت کنیم و موارد دیگر. در فصل‌های بعدی به آن‌ها خواهیم پرداخت.
