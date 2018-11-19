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

### نسخه JAVA

یک نمونه شی از کلاس ‍‍‍```Serial``` ایجاد می‌شود:
```java
Serial serial = new Serial();
```
با ایجاد این شی متدهای زیر به منظور برقراری ارتباط بین برنامه سمت PC و کارت‌خوان دماوند، قابل دسترس و تنظیم هستند:

| نام | نوع ورودی | توضیحات |
|-|-|-|
| ```setDataBits``` | ```int``` |  |
| ```setBaudRate``` | ```int``` |  |
| ```setPortName``` | ```String``` |  |
| ```setParity``` | ```int``` |  |
| ```setSerialAfterReceivedListener``` | ```Serial.SerialAfterReceivedListener``` | تنظیم کلاس دریافت کننده CallBack |

-  به منظور دریافت نتیجه انجام تراکنش، می‌بایست کلاس مورد نظر اینترفیس ```Serial.SerialAfterReceivedListener``` را implements نمایید. 


پس از تنظیم مقادیر بالا به منظور برقراری ارتباط از متد ‍‍```()initCommunication``` استفاده می‌گردد. خروجی این متد یک مقدار از نوع ```boolean``` است که در صورت موفقیت در برقراری ارتباط مقدار ```true``` و در غیر این صورت مقدار ```false``` برگردانده می‌شود:



```java
public class Main implements Serial.SerialAfterReceivedListener {

    public void start(){
        Serial serial = new Serial();

        serial.setPortName("COM3");
        serial.setSerialAfterReceivedListener(this);

        boolean initResult = serial.initCommunication();
        System.out.println("init: " + initResult);
        if (initResult) {
            

        }
    }
}
```

در صورت موفق بودن ایجاد ارتباط می‌توان متدهای مربوط به نوع تراکنش درخواستی را نیز تنظیم نمود:

| نام | نوع ورودی | توضیحات |
|-|-|-|
| ```setPaymentType``` | ```enum PaymentType``` | نوع تراکنش درخواستی که می‌تواند یکی از مقدارهای ```PaymentType.Sale``` (خرید)، ```PaymentType.SaleByID``` (خرید با شناسه) و ```PaymentType.Balance``` (مانده‌گیری) باشد |
| ```setSerialNumber``` | ```string``` | شماره سریال کارت‌خوان |
| ```setMerchantNumber``` | ```string``` | شماره پذیرنده |
| ```setTerminalNumber``` | ```string``` | شماره ترمینال (پایانه) |
| ```setAmount``` | ```string``` | مبلغ خرید در نوع تراکنش‌های «خرید» و «خرید با شناسه»|
| ```setSaleId``` | ```string``` | مقدار شناسه در نوع تراکنش «خرید با شناسه» |

پس از تنظیم مقادیر بالا و به منظور ارسال درخواست انجام تراکنش، می‌بایست متد ```()payment``` فراخوانی گردد. پس از پایان عملیات در کارت‌خوان نتیجه از طریق متد ```afterReceived``` که با implements اینترفیس ```Serial.SerialAfterReceivedListener``` دریافت می‌گردد.

```java
// Sale sample

boolean initResult = serial.initCommunication();
System.out.println("init: " + initResult);
if (initResult) {
     serial.setSerialNumber("000000000000");
     serial.setTerminalNumber("00000000");
     serial.setMerchantNumber("000000000000000");
     serial.setPaymentType(Serial.PaymentType.Sale);
     serial.setAmount("1000");      

     serial.payment(); 
}
```


```java
// Balance sample

boolean initResult = serial.initCommunication();
System.out.println("init: " + initResult);
if (initResult) {
     serial.setSerialNumber("000000000000");
     serial.setTerminalNumber("00000000");
     serial.setMerchantNumber("000000000000000");
     serial.setPaymentType(Serial.PaymentType.Balance);

     serial.payment(); 
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

### نسخه JAVA

پس از پایان عملیات، متدهای زیر از شی ```serial``` که از ورودی متد ```afterReceived``` دریافت می‌شود، قابل دسترس می‌باشد: 



```java
@Override
public void afterReceived(Serial serial) {
	System.out.println("PAYMENT_TYPE: " + serial.getPaymentType());
	System.out.println("SERIAL_NO   : " + serial.getSerialNumber());
	System.out.println("MERCHANT_NO : " + serial.getMerchantNumber());
	System.out.println("TERMINAL_NO : " + serial.getTerminalNumber());
	System.out.println("STAN        : " + serial.getSTAN());
	System.out.println("RRN         : " + serial.getRRN());
	System.out.println("RES_CODE    : " + serial.getResultCode());
	System.out.println("AMOUNT      : " + serial.getAmount());
	System.out.println("DATETIME    : " + serial.getDateTime());
	System.out.println("PAN         : " + serial.getPAN());
	System.out.println("BALANCE     : " + serial.getBalance());
	System.out.println("DESCRIPTION : " + serial.getDescription());
}
```

| نام | نوع خروجی | توضیحات |
|-|-|-|
| ```getResultCode``` | ```long``` | کد وضعیت انجام تراکنش |
| ```getPaymentType``` | ```enum PaymentType``` | نوع تراکنش انجام شده |
| ```getSerialNumber``` | ```String``` | شماره سریال کارت‌خوان |
| ```getMerchantNumber``` | ```String``` | شماره پذیرنده |
| ```getTerminalNumber``` | ```String``` | شماره ترمینال (پایانه) |
| ```getSTAN``` | ```String``` | شماره پیگیری تراکنش |
| ```getRRN``` | ```String``` | شماره ارجاع تراکنش |
| ```getAmount``` | ```String``` | مبلغ در تراکنش‌های «خرید» و «خرید با شناسه» |
| ```getBalance``` | ```String``` | مقدار موجودی در تراکنش «مانده‌گیری» |
| ```getDateTime``` | ```String``` | زمان و تاریخ انجام تراکنش |
| ```getPAN``` | ```String``` | شماره کارت مشتری به صورت Hash شده مطابق با الزامات شاپرک |
| ```getDescription``` | ```String``` | توضیح وضعیت تراکنش |

-  در صورت موفق بودن انجام تراکنش مقدار خروجی متد ```getResultCode``` برابر ```0``` خواهد بود و در غیر این صورت خروجی متد ```getResultCode``` برابر با کد خطای مربوطه می‌باشد. 



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

### نسخه JAVA

برای قطع ارتباط از متد ```()closeCommunication``` استفاده می‌گردد. خروجی این متد یک مقدار از نوع ```boolean``` است که در صورت موفقیت در قطع ارتباط مقدار ```true``` و در غیر این صورت مقدار ```false``` برگردانده می‌شود.

```java
@Override
public void afterReceived(Serial serial) {
    ...

    boolean r = serial.closeCommunication();
    System.out.println("close: " + r);
}
```


## دریافت نمونه پروژه


 دریافت [پروژه به زبان برنامه‌نویسی #C در گیت‌هاب](https://github.com/ecdco/PCPOSCsharpSample)

 دریافت [پروژه به زبان برنامه‌نویسی JAVA در گیت‌هاب](https://github.com/ecdco/PCPOSJavaSample)





