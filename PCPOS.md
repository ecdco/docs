---
id: PCPOS
title: راهنمای فنی اتصال PC به POS دماوند
sidebar_label: اتصال PC به POS
custom_edit_url: https://github.com/ecdco/docs/edit/master/PCPOS.md
---

## مقدمه

این مستند به چگونگی ارتباط بین PC (صندوق‌های فروشگاهی) با کارت‌خوان‌های دماوند می‌پردازد. به متقاضیان پس از ثبت درخواست کارت‌خوان و تایید از سوی شرکت الکترونیک کارت دماوند، بر اساس نوع برنامه سمت PC آن‌ها یک نسخه کتابخوانه به زبان NET. (پسوند dll) و JAVA (پسوند jar) تحویل داده می‌شود.

شمای کلی چگونگی ارتباط در تصویر زیر قابل مشاهده می‌باشد:

![شمای کلی چگونگی ارتباط PC POS دماوند](/img/pcpos/pcpos-1.png)

## شروع ارتباط و ارسال درخواست تراکنش

مراحل کار به شرح زیر است:

-  ایجاد یک شی از کلاس ‍‍‍```Serial```
-  تنظیم مقدارهای مربوط به اتصال از پورت سریال و مورد نیاز نوع تراکنش درخواستی از کلاس ‍‍‍```Serial```
-  شروع ارتباط
-  انجام تراکنش در سمت کارت‌خوان
-  دریافت نتیجه تراکنش

### نسخه NET.

یک نمونه شی از کلاس ‍‍‍```Serial``` ایجاد می‌شود:
```csharp
ECDPCPos.Serial serial = new Serial();
```
با ایجاد این شی Property‌های زیر به منظور برقراری ارتباط بین برنامه سمت PC و کارت‌خوان دماوند، قابل دسترس و تنظیم هستند:

| نام | نوع | توضیحات |
|-|-|-|
| ```DataBits``` | ```int``` |  |
| ```BaudRate``` | ```int``` |  |
| ```PortName``` | ```string``` |  |
| ```Parity``` | ```Parity``` |  |
| ```Handshake``` | ```Handshake``` |  |
| ```SerialOnReceive``` | ```event``` | تنظیم نام یک متد به عنوان CallBack دریافت نتیجه عملیات |


پس از تنظیم مقادیر بالا به منظور برقراری ارتباط از متد ‍‍```()InitComminication``` استفاده می‌گردد. خروجی این متد یک مقدار از نوع ```boolean``` است که در صورت موفقیت در برقراری ارتباط مقدار ```true``` و در غیر این صورت مقدار ```false``` برگردانده می‌شود:



```csharp
private void s_Onreveive()
{

}
```


```csharp
ECDPCPos.Serial serial = new Serial();

serial.BaudRate = 115200;
serial.DataBits = 8;
serial.Parity = System.IO.Ports.Parity.None;
serial.PortName = "COM8";
serial.SerialOnReceive += new Serial.ECDDataReceivedEventHandler(s_Onreveive);

if (serial.InitComminication())
{
    txt.Text += "Port " + serial.PortName + " Opened" + "\r\n";
}

```

در صورت موفق بودن ایجاد ارتباط می‌توان Property‌های مربوط به نوع تراکنش درخواستی را نیز تنظیم نمود:

| نام | نوع | توضیحات |
|-|-|-|
| ```Type``` | ```enum PaymentType``` | نوع تراکنش درخواستی که می‌تواند یکی از مقدارهای ```PaymentType.Sale``` (خرید)، ```PaymentType.SaleByID``` (خرید با شناسه) و ```PaymentType.Balance``` (مانده‌گیری) باشد |
| ```DeviceSerialNo``` | ```string``` | شماره سریال کارت‌خوان |
| ```MerchantNo``` | ```string``` | شماره پذیرنده |
| ```TerminalNo``` | ```string``` | شماره ترمینال (پایانه) |
| ```Amount``` | ```string``` | مبلغ خرید در نوع تراکنش‌های «خرید» و «خرید با شناسه»|
| ```SaleID``` | ```string``` | مقدار شناسه در نوع تراکنش «خرید با شناسه» |


پس از تنظیم مقادیر بالا و به منظور ارسال درخواست انجام تراکنش، می‌بایست متد ```()Payment``` فراخوانی گردد. پس از پایان عملیات در کارت‌خوان نتیجه از طریق متد تنظیم شده در ```SerialOnReceive``` دریافت می‌گردد.

```csharp
// Sale sample

if (serial.InitComminication())
{
    serial.DeviceSerialNo = txtSerial.Text.Trim();
    serial.MerchantNo = txtMerchant.Text.Trim();
    serial.TerminalNo = txtTerminal.Text.Trim();
    serial.Type = Serial.PaymentType.Sale;
    serial.Amount = txtAmount.Text.Trim();

    serial.Payment();
}

```

```csharp
// Balance sample

if (serial.InitComminication())
{
    serial.DeviceSerialNo = txtSerial.Text.Trim();
    serial.MerchantNo = txtMerchant.Text.Trim();
    serial.TerminalNo = txtTerminal.Text.Trim();
    serial.Type = Serial.PaymentType.Balance;

    serial.Payment();
}

```

## دریافت نتیجه

جدا از موفق یا ناموفق بودن انجام تراکنش توسط کارت‌خوان، نتیجه به صورت زیر دریافت می‌گردد.

### نسخه NET.
پس از پایان عملیات، Property‌های زیر از همان شی ایجاد شده از کلاس  ```Serial``` در مرحله شروع ارتباط و ارسال درخواست قابل دسترس هستند: 



```csharp
private void s_Onreveive()
{
    string result = "Status= " + serial.Status.ToString() + "\r\n";
    result += "Stan= " + serial.Stan + "\r\n";
    result += "RRN= " + serial.RRN + "\r\n";
    result += "DateTime= " + serial.Datetime + "\r\n";
    result += "Amount= " + serial.Amount + "\r\n";
    result += "Balance= " + serial.Balance + "\r\n";
    result += "CardNo= " + serial.CardNumber + "\r\n";

    MessageBox.Show(result);
}

```

| نام | نوع | توضیحات |
|-|-|-|
| ```Status``` | ```int``` | کد وضعیت انجام تراکنش |
| ```Error``` | ```string``` | توضیح وضعیت تراکنش |
| ```Stan``` | ```string``` | شماره پیگیری تراکنش |
| ```RRN``` | ```string``` | شماره ارجاع تراکنش |
| ```Datetime``` | ```string``` | زمان و تاریخ انجام تراکنش |
| ```Amount``` | ```string``` | مبلغ در تراکنش‌های «خرید» و «خرید با شناسه» |
| ```Balance``` | ```string``` | مقدار موجودی در تراکنش «مانده‌گیری» |
| ```CardNumber``` | ```string``` | شماره کارت مشتری به صورت Hash شده مطابق با الزامات شاپرک |

-  در صورت موفق بودن انجام تراکنش مقدار ```Status``` برابر ```0``` خواهد بود و در غیر این صورت ```Status``` برابر با کد خطای مربوطه می‌باشد. 


## قطع ارتباط

برای قطع ارتباط نیز می‌توان به صورت زیر عمل کرد.


### نسخه NET.
برای قطع ارتباط از متد ```()CloseComminication``` استفاده می‌گردد. خروجی این متد یک مقدار از نوع ```boolean``` است که در صورت موفقیت در قطع ارتباط مقدار ```true``` و در غیر این صورت مقدار ```false``` برگردانده می‌شود.

```csharp
serial.SerialOnReceive -= new Serial.ECDDataReceivedEventHandler(s_Onreveive);
if (serial.CloseComminication())
{

}
```


## دریافت نمونه پروژه


 دریافت [پروژه به زبان برنامه‌نویسی #C در گیت‌هاب](https://github.com/ecdco/PCPOSCsharpSample)





