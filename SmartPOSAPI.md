---
id: SmartPOSAPI
title: راهنمای فنی اتصال به APP API کارت‌خوان‌های هوشمند دماوند
sidebar_label: API کارت‌خوان هوشمند
custom_edit_url: https://github.com/ecdco/docs/blob/master/SmartPOSAPI.md
---

نسخه 1٫0

## مقدمه
این مستند به شیوه تعامل اپلیکیشن‌های شخص ثالث با اپلیکیشن پرداخت شرکت الکترونیک کارت دماوند موجود بر روی دستگاه‌های کارت‌خوان هوشمند می‌پردازد، به گونه‌ای که این اپلیکیشن‌ها بتوانند بدون کوچکترین درگیری با امور مربوط به پرداخت در شبکه شاپرک، تراکنش‌های مالی خود را به انجام و نتیجه حاصل را بدون کاستی دریافت نمایند.

برای ایجاد چنین اپلیکیشن‌هایی یک کتابخانه (jar.)  اندرویدی در اختیار متقاضیان از سوی شرکت الکترونیک کارت دماوند قرار داده می‌شود که تمامی امور انجام تراکنش، دریافت نتایج تراکنش و گزارش‌گیری را بر عهده خواهد داشت. قابل ذکر است که تمامی این اپلیکیشن‌ها پس از توسعه برای عملیاتی شدن نیازمند تست و تایید شرکت الکترونیک کارت دماوند خواهند بود.


## ساختار کلی

به صورت کلی توابع زیر توسط این نسخه از API قابل پیاده‌سازی است.
-   دریافت پیکربندی
-   انجام تراکنش
-   درخواست دریافت اطلاعات مربوط به یک تراکنش انجام شده
-   درخواست چاپ رسید یک تراکنش انجام شده
-   درخواست دریافت گزارش از تراکنش‌های انجام شده در یک روز خاص یا یک بازه بر اساس تاریخ انجام تراکنش
-   فراخوانی اپلیکیشن پرداخت
    
که این اعمال به کمک دو دسته از کلاس‌های «ارسال کننده درخواست» و «دریافت نتایج» به انجام خواهند رسید. این کلاس‌ها به شرح زیر است.

### کلاس‌های ارسال کننده درخواست

-   ```ConfigRequest``` : ارسال درخواست «دریافت پیکربندی» به اپلیکیشن پرداخت
-   ```PaymentRequest``` : ارسال درخواست «انجام تراکنش» یا «دریافت یک تراکنش انجام شده» به اپلیکیشن پرداخت
-   ```PrintRequest``` : ارسال درخواست «چاپ یک تراکنش انجام شده» به اپلیکیشن پرداخت
-   ```ReportRequest``` : ارسال درخواست «دریافت گزارش» به اپلیکیشن پرداخت
-   ```LunchAppRequest``` : ارسال درخواست «فراخوانی اپلیکیشن پرداخت» 

### کلاس‌های دریافت نتایج
-   ```ConfigResult``` : دریافت نتیجه درخواست «دریافت پیکربندی» از اپلیکیشن پرداخت
-   ```PaymentResult``` : دریافت نتیجه درخواست «انجام تراکنش» یا «دریافت یک تراکنش انجام شده» از اپلیکیشن پرداخت
-   ```PrintResult``` : دریافت نتیجه درخواست «چاپ یک تراکنش انجام شده» از اپلیکیشن پرداخت
-   ```ReportResult``` : دریافت نتیجه درخواست «دریافت گزارش» از اپلیکیشن پرداخت

###  شروع فراخوانی

شروع انجام هر عملی با پیاده‌سازی کلاس‌های «ارسال کننده درخواست» خواهد بود. این کلاس‌ها متد مهمی با نام ```setRequestMethod``` دارند که چگونگی کار هر کلاس را مشخص می‌کند. این متد متناسب با هر کلاس ارسال کننده درخواست می‌تواند تنها یکی از مقادیر ثابت زیر را بپذیرد.

قابل ذکر است که تمامی این مقادیر ثابت از طریق کلاس  ```RequestMethodConst``` قابل دسترس هستند.

### مقادیر کلاس  ```RequestMethodConst```

*  مقادیر خاص کلاس ```ConfigRequest```
    *   ```Get_Config```: دریافت پیکربندی

*  مقادیر خاص کلاس ```PaymentRequest```
    *   ```Do_Transaction```: انجام تراکنش
    *   ```Get_Transaction_By_PaymentId```: دریافت اطلاعات یک تراکنش بر اساس شناسه خاص آن
    *   ```Get_Transaction_By_TrackingNumber```: دریافت اطلاعات یک تراکنش بر اساس شماره پیگیری آن
    *   ```Get_Transaction_By_ReferenceNumber```: دریافت اطلاعات یک تراکنش بر اساس شماره مرجع آن


*  مقادیر خاص کلاس ```PrintRequest```
    *   ```Print_Transaction_By_PaymentId```: چاپ رسید یک تراکنش بر اساس شناسه خاص آن
    *   ```Print_Transaction_By_TrackingNumber```: چاپ رسید یک تراکنش بر اساس شماره پیگیری آن
    *   ```Print_Transaction_By_ReferenceNumber```: چاپ رسید یک تراکنش بر اساس شماره مرجع آن

*  مقادیر خاص کلاس ```ReportRequest```
    *   ```Get_Report_Specific_Day```: دریافت گزارش‌ تراکنش‌های انجام شده در یک روز خاص
    *   ```Get_Report_Date_Range```: دریافت گزارش‌ تراکنش‌های انجام شده بین دو تاریخ مشخص
    
*  مقادیر خاص کلاس ```ReportRequest```
    *   ```Lunch_App```: فراخوانی اپلیکیشن پرداخت

    
*توجه - Payment Id : شناسه‌ایی یکتاست است که به دلخواه کاربر به هر تراکنش انتساب داه می‌شود. تنظیم این مقدار اجباری نمیباشد. باید توجه داشت که تضمین یکتایی این مقدار بر عهده کاربر خواهد بود.*


### نمونه ارسال درخواست

```java
PaymentRequest paymentRequest = new PaymentRequest(this);
paymentRequest.setRequestMethod(RequestMethodConst.Do_Transaction);
...
```

*```this``` در اینجا به اکتیویتی جاری اشاره دارد.*

### نمونه دریافت نتیجه
نتیجه تمامی این توابع از طریق متد ```onActivityResult``` دریافت می‌گردد. بنابرین در اکتیویتی مورد نظر خود می‌بایست این متد را ```Override``` نمایید.
```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {
        if (resultCode == Activity.RESULT_OK) {
            String result = data.getStringExtra("RequestResult");
            PaymentResult paymentResult = new PaymentResult(result);
            ...
        }
    }
}
```
*مقدار  ```requestCode``` از طریق متد ```setRequestCode``` کلاس‌های «ارسال کننده درخواست» قابل تنظیم است. مقدار پیش‌فرض این پارامتر عدد ```1``` می‌باشد.*

<br/>

نتایج همواره در قالب یک مقدار ```String``` بازگشت داده می‌شوند که همانطور که در نمونه کد بالا نیز مشخص است از طریق کلید ```"RequestResult"``` قابل دریافت است.

<br/>

در ادامه به شرح هر یک از کلاس‌های ارسال و دریافت نتایج خواهیم پرداخت.


## انجام تراکنش

انجام تراکنش به صورت پنج گام زیر به انجام می‌رسد.


**گام یک:** ایجاد یک نمونه شی از کلاس ```PaymentRequest```

```java
PaymentRequest paymentRequest = new PaymentRequest(this);
```


**گام دو:** تنظیم متد درخواست بر روی مقدار انجام تراکنش

```java
paymentRequest.setRequestMethod(RequestMethodConst.Do_Transaction);
```

**گام سه:** تعیین نوع تراکنش، در حال حاضر سه نوع تراکنش زیر با استفاده از این نسخه API قابل انجام است.
-  خرید
-  خرید با شناسه
-  دریافت موجودی حساب کارت‌های بانکی

تعین نوع تراکنش به کمک متد ```setTransactionType``` انجام می‌پذیرد. این متد متناسب با نوع تراکنش مورد نظر تنها می‌تواند یکی از مقادیر ثابت زیر را بپذیرد. قابل ذکر است که تمامی این مقادیر از طریق کلاس  ```TransactionTypeConst``` قابل دسترس هستند.


-  ```TransactionTypeConst.Sale```: تراکنش خرید
-  ```TransactionTypeConst.SaleById```: تراکنش خرید با شناسه
-  ```TransactionTypeConst.Balance```: تراکنش دریافت موجودی حساب کارت بانکی


```java
paymentRequest.setTransactionType(TransactionTypeConst.Sale);
```


**گام چهار:‌** تنظیم مقادیر مورد نیاز انجام هر تراکنش، بدیهی است انجام هر تراکنش نیازمند داده‌های مشخصی است که در این بخش برای انجام **تراکنش خرید** می‌بایست مقدار مبلغ خرید و برای انجام **تراکنش خرید با شناسه** علاوه بر مبلغ، مقدار شناسه خرید نیز  تنظیم گردد.


```java
paymentRequest.setAmount("1000");
// paymentRequest.setSaleId("123456789101112131415");
```


**گام پنجم:** ارسال درخواست، این عمل به کمک متد ```send``` انجام می‌شود و نتیجه عملیات ارسال به صورت یک مقدار ```True``` یا ```False``` برگردانده خواهد شد.

```java
boolean result = paymentRequest.send();
```

**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**


### دریافت نتیجه انجام تراکنش

برای دریافت نتیجه تراکنش می‌بایست کلاس ```PaymentResult``` با مقدار دریافت شده از کلید ```"RequestResult"``` پیاده‌سازی گردد.


```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {

        if(resultCode == Activity.RESULT_OK){

            String result = data.getStringExtra("RequestResult");
            
            PaymentResult paymentResult = new PaymentResult(result);
            
            if (paymentResult.isOK()) {
            
                Log.d("ECD", "CardNumber: " + paymentResult.getCardNumber());
                
                ...
            }
	   }
	}
}
```

فهرست متد‌های قابل دسترس از کلاس  ```PaymentResult``` به شرح زیر است.


-  **```isOK```**: چنانچه درخواست از سوی اپلیکیشن پرداخت مورد پذیرش قرار گرفته و در پردازش آن به مشکلی برخورد نکرده باشد، خروجی این متد برابر مقدار ```True``` می‌باشد.
-  **```getRequestStatus```**: اگر خروجی تابع ```isOK``` برابر ```False``` باشد، علت خطا را می‌توانید از خروجی این تابع  جویا شوید. این خروجی برابر با یکی ار فیلد‌های ثابت کلاس ```RequestStatusConst``` خواهد بود.
-  **```getTransactionType```**: دریافت نوع تراکنش که مقدار خروجی برابر با یکی از فیلد‌های ثابت کلاس ```TransactionTypeConst``` خواهد بود.
-  **```getTransactionStatus```**: خروجی این تابع مشخص می‌کند مراحل انجام تراکنش تا چه مرحله‌ایی پیش رفته است. این خروجی برابر با یکی از فیلد‌های ثابت کلاس ```TransactionStatusConst``` خواهد بود.
-  **```getErrorCode```**: کد خطا تراکنش در شبکه پرداخت شاپرک
-  **```transactionIsSuccessful```**: چنانچه کد خطای تراکنش برابر با مقدار ```"00"``` باشد (به معنی تراکنش موفق) خروجی این متد نیز برابر مقدار  ```True```  خواهد شد.
-  **```transactionHasUserError```**: چنانچه کد خطای تراکنش برابر با یکی از خطاهای سمت دارنده کارت باشد (مانند ورود رمز اشتباه کارت) خروجی برابر مقدار  ```True```  خواهد بود.
-  **```getPaymentId```**: مقدار دلخواه کاربر که در هنگام ارسال درخواست انجام تراکنش تعیین می‌شود.
-  **```getCardNumber```**: شماره کارت کشیده شده بر روی دستگاه، این مقدار بنابر الزامات شاپرک به صورت ستاره‌دار خواهد بود.
-  **```getBankName```**: نام بانک صادر کننده کارت مشتری
-  **```getAmount```**: مقدار مبلغ خرید (در تراکنش‌های خرید و خرید با شناسه) یا مقدار موجودی کارت (در تراکنش دریافت موجودی)
-  **```getReferenceNumber```**: شماره مرجع تراکنش
-  **```getTrackingNumber```**: شماره پیگیری تراکنش
-  **```getDate```**: تاریخ انجام تراکنش در قالب شاپرک
-  **```getTime```**: زمان انجام تراکنش در قالب شاپرک
-  **```getDateFormatted```**: تاریخ انجام تراکنش در قالب YYYY-MM-DD (میلادی)
-  **```getTimeFormatted```**: زمان انجام تراکنش در قالب HH:MM:SS
-  **```getCreatedTime```**: زمان و تاریخ انجام تراکنش به صورت Timestamp 
-  **```getMerchantNumber```**: شماره پذیرنده
-  **```getTerminalNumber```**: شماره پایانه
-  **```getSerialNumber```**: شماره سریال کارت‌خوان
-  **```isSaleTransaction```**: اگر تراکنش از نوع خرید باشد خروجی تابع برابر مقدار  ```True``` خواهد بود
-  **```isBalanceTransaction```**: اگر تراکنش از نوع در یافت موجودی باشد خروجی تابع برابر مقدار  ```True``` خواهد بود
-  **```isSaleByIdTransaction```**: اگر تراکنش از نوع خرید با شناسه باشد خروجی تابع برابر مقدار  ```True``` خواهد بود
-  **```getSaleId```**: مقدار شناسه خرید، چنانچه تراکنش از نوع خرید با شناسه باشد
-  **```getDescription```**: توضیحات انجام تراکنش


###  متدهای اختیاری کلاس ```PaymentRequest```

این کلاس شامل برخی متد‌های دیگر است که به دلخواه کاربر می‌تواند آن‌ها را نیز فراخوانی نماید. فهرست این متدها به شرح زیر است. 

-  **```setTitle```**: تنظیم عنوان اکتیویتی انجام تراکنش
-  **```setPaymentId```**: تنظیم یک مقدار دلخواه بر روی تراکنش. این مقدار می‌بایست در میان تمام تراکنش‌ها کاملا یکتا باشد.
-  **```setCustomerMessage```**: تنظیم پیام مشتری، این پیام به هنگام کشیدن کارت به مشتری نمایش داده خواهد شد.
-  **```setTopPadding```**: بسته به طراحی اپلیکیشن این مقدار می‌تواند به مقدار ```False``` یا ```True``` تنظیم گردد.
-  **```setRequestCode```**: تنظیم مقدار دلخواه برای پارامتر  ```requestCode```


##  دریافت پیکربندی

منظور از پیکربندی، تنظیماتی است که اپلیکیشن پرداخت پیش از انجام هر تراکنشی می‌بایست دریافت کند. علاوه بر اینکه پیکربندی از داخل اپلیکیشن پرداخت نیز قابل دریافت است، ولی با استفاده از کلاس ```ConfigRequest``` نیز می‌توان از طریق API اقدام به دریافت تنظیمات کرد.

**البته توجه داشته باشید که در طول استفاده از کارت‌خوان تنها یک بار نیاز به دریافت پیکربندی می‌باشد.**

ارسال درخواست با استفاده از کلاس ```ConfigRequest``` و دریافت پاسخ نیز توسط کلاس ```ConfigResult``` انجام می‌شود و در هنگام ارسال درخواست، نیازی به تنظیم متد ```setRequestMethod``` نیست و این متد به صورت پیش‌فرض با مقدار ```RequestMethodConst.Get_Config``` تنظیم شده است.



```java
ConfigRequest configRequest = new ConfigRequest(this);
configRequest.setPassword("123456");
boolean result = configRequest.send();
```

**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**



کلاس ```ConfigRequest``` تنها یک متد مهم با نام ```setPassword``` دارد که می‌بایست با کلمه عبور پذیرندگی تعریف شده بر روی کارتخوان تنظیم گردد.


برای دریافت نتیجه پیکربندی نیز می‌بایست کلاس ```ConfigResult``` با مقدار دریافت شده از کلید ```"RequestResult"``` پیاده‌سازی گردد.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {

    if (requestCode == 1) {

        if (resultCode == Activity.RESULT_OK) {

            String result = data.getStringExtra("RequestResult");
            
            ConfigResult configResult = new ConfigResult(result);
            
            if (configResult.isOK()) {
                
                Log.d("ECD", "isOK: " + configResult.isOK());
                Log.d("ECD", "RequestStatus: " + configResult.getRequestStatus());
                Log.d("ECD", "MerchantNumber: " + configResult.getMerchantNumber());
                Log.d("ECD", "MerchantTitle: " + configResult.getMerchantTitle());
                Log.d("ECD", "MerchantPhoneNumber: " + configResult.getMerchantPhoneNumber());
                Log.d("ECD", "MerchantPostalCode: " + configResult.getMerchantPostalCode());
                Log.d("ECD", "TerminalNumber: " + configResult.getTerminalNumber());
  
            }
        }
    }
}
```


##  دریافت اطلاعات یک تراکنش انجام 

همانند درخواست انجام تراکنش، ارسال این درخواست نیز با استفاده از کلاس ```PaymentRequest``` و دریافت پاسخ نیز توسط کلاس ```PaymentResult``` انجام می‌شود. با این تفاوت که متد ```setRequestMethod``` می‌بایست یکی از مقادیر ثابت کلاس  ```RequestMethodConst``` باشد:


-  ```Get_Transaction_By_PaymentId```: جستجوی یک تراکنش بر اساس Payment Id
-  ```Get_Transaction_By_TrackingNumber```: جستجوی یک تراکنش بر اساس Tracking Number
-  ```Get_Transaction_By_ReferenceNumber```: جستجوی یک تراکنش بر اساس Reference Number

بدیهی است که متناسب با تنظیم هریک از این مقادیر می‌بایست مقدار مربوط به هریک را نیز تنظیم نمایید:

```java
...
// RequestMethodConst.Get_Transaction_By_PaymentId
paymentRequest.setPaymentId("1000001");
...
```

```java
...
// RequestMethodConst.Get_Transaction_By_TrackingNumber
paymentRequest.setTrackingNumber("001234");
...
```

```java
...
// RequestMethodConst.Get_Transaction_By_ReferenceNumber
paymentRequest.setReferenceNumber("1234567891245");
...
```

**نمونه ارسال درخواست:**

```java
PaymentRequest paymentRequest = new PaymentRequest(this);
paymentRequest.setRequestMethod(RequestMethodConst.Get_Transaction_By_TrackingNumber);
paymentRequest.setTrackingNumber("000123");
boolean result = paymentRequest.send();
```
**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**


**نمونه دریافت نتیجه:**


```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {
        if(resultCode == Activity.RESULT_OK){
        
            String result = data.getStringExtra("RequestResult");
            
            PaymentResult paymentResult = new PaymentResult(result);
            
            if (paymentResult.isOK()) {
            
                Log.d("ECD", "CardNumber: " + paymentResult.getCardNumber());
                ...
            }
	   }
	}
}
```

*در صورتی که تراکنش مورد نظر پیدا نشود، مقدار خروجی تابع  ```isOK``` برابر  ```False``` و مقدار خروجی تابع ```getRequestStatus``` نیز برابر مقدار ```RequestStatusConst.Transaction_Not_Found``` خواهد بود.*


## چاپ اطلاعات یک تراکنش انجام شده

ارسال این درخواست با استفاده از کلاس ```PrintRequest``` و دریافت پاسخ نیز توسط کلاس ```PrintResult``` انجام می‌شود. مقدار متد ```setRequestMethod``` نیز می‌بایست یکی از مقادیر ثابت کلاس  ```RequestMethodConst``` باشد:

-  ```Print_Transaction_By_PaymentId```: جستجو و چاپ یک تراکنش بر اساس Payment Id
-  ```Print_Transaction_By_TrackingNumber```: جستجو و چاپ یک تراکنش بر اساس Tracking Number
-  ```Print_Transaction_By_ReferenceNumber```: جستجو و چاپ یک تراکنش بر اساس Reference Number

بدیهی است که متناسب با تنظیم هریک از این مقادیر می‌بایست مقدار مربط را نیز تنظیم نمایید:

```java
...
// RequestMethodConst.Print_Transaction_By_PaymentId
printRequest.setPaymentId("1000001");
...
```

```java
...
// RequestMethodConst.Print_Transaction_By_TrackingNumber
printRequest.setTrackingNumber("001234");
...
```

```java
...
// RequestMethodConst.Print_Transaction_By_ReferenceNumber
printRequest.setReferenceNumber("1234567891245");
...
```

**نمونه ارسال درخواست:**
```java
PrintRequest printRequest = new PrintRequest(this);
printRequest.setRequestMethod(RequestMethodConst.Print_Transaction_By_TrackingNumber);
printRequest.setTrackingNumber("000123");
boolean result = printRequest.send();
```
**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**


**نمونه دریافت نتیجه:**

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {
        if(resultCode == Activity.RESULT_OK){

            String result = data.getStringExtra("RequestResult");

            PrintResult printResult = new PrintResult(result);

            if (printResult.isOK()) {
                        
                        
            } 
	   }
	}
}
```

چنانچه عمل جستجو و چاپ تراکنش با موفقیت انجام شده باشد مقدار خروجی تابع  ```isOK``` برابر  ```True```  خواهد بود.

در صورتی که تراکنش مورد نظر پیدا نشود، مقدار خروجی تابع  ```isOK``` برابر  ```False``` و مقدار خروجی تابع ```getRequestStatus``` نیز برابر ```RequestStatusConst.Transaction_Not_Found``` خواهد بود.

شکست در انجام عمل چاپ می‌تواند علل دیگری همچون نبود کاغذ و… باشد که می‌توانید به کمک خروجی متد  ```getRequestStatus```، از آن باخبر گردید. همان‌طور که پیش‌تر نیز بیان شد خروجی این متد یکی از مقادیر ثابت موجود در کلاس ```RequestStatusConst``` است.



## گزارش‌گیری

ارسال این درخواست با استفاده از کلاس ```ReportRequest``` و دریافت پاسخ نیز توسط کلاس ```ReportResult``` انجام می‌شود. مقدار متد ```setRequestMethod``` نیز می‌بایست یکی از مقادیر ثابت کلاس  ```RequestMethodConst``` باشد:

-  ```Get_Report_Specific_Day```: دریافت گزارش‌ تراکنش‌های انجام شده در یک روز خاص
-  ```Get_Report_Date_Range```: دریافت گزارش‌ تراکنش‌های انجام شده بین دو تاریخ مشخص


متناسب با تنظیم هر یک از این دو مقدار، می‌بایست داده‌های مورد نیاز دیگری از کلاس ```ReportRequest``` نیز تنظیم گردند که در ادامه شرح داده شده است:

```java
...
// RequestMethodConst.Get_Report_Specific_Day
reportRequest.setDay("2018-02-02");
...
```

```java
...
// RequestMethodConst.Get_Report_Date_Range
reportRequest.setFrom("2018-02-02");
reportRequest.setTo("2018-02-04");
...
```

توجه داشته باشید ورودی هر سه متد می‌بایست یک مقدار ```String```  (تاریخ میلادی) با قالب ```YYYY-MM-DD``` باشد.



**نمونه ارسال درخواست:**
```java
ReportRequest reportRequest = new ReportRequest(thisActivity);
reportRequest.setRequestMethod(RequestMethodConst.Get_Report_Specific_Day);
reportRequest.setDay("2018-06-24");
boolean result = reportRequest.send();
```
**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**


**نمونه دریافت نتیجه:**

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == 1) {
        if(resultCode == Activity.RESULT_OK){

            String result = data.getStringExtra("RequestResult");
            
            ReportResult reportResult = new ReportResult(result);

            if (reportResult.isOK()) {
            
                Log.d("ECD", "Total: " + reportResult.getTotal());
                
                ...
            }
	   }
	}
}
```

متدهای زیر از کلاس ```ReportResult```  را می‌توانید در صورت تایید خروجی تابع  ```isOK``` دریافت نمایید:

-  **```getTotal```**: تعداد کل تراکنش‌های انجام شده
-  **```getTotalSuccess```**: تعداد تراکنش‌های موفق انجام شده
-  **```getTotalAmount```**: جمع مبالغ تراکنش‌های «خرید» و «خرید با شناسه» موفق انجام شده


علت شکست در انجام گزارش‌گیری را می‌توانید به کمک خروجی متد getRequestStatus، بررسی نمایید. همان‌طور که پیش‌تر نیز بیان شد خروجی این متد یکی از مقادیر ثابت موجود در کلاس RequestStatusConst است.



## فراخوانی اپلیکیشن پرداخت

در مواقعی که نیاز به بالا آمدن کامل اپلیکیشن پرداخت دارید، می‌توانید از این متد استفاده نمایید.


**نمونه ارسال درخواست:**
```java
LunchAppRequest configRequest = new LunchAppRequest(this);
boolean result = configRequest.send();
```
**چنانچه مقدار ```result``` در نمونه کد بالا برابر مقدار  ```True```  باشد، ارسال درخواست با موفقیت انجام شده است.**

توجه داشته باشید که ارسال این درخواست پاسخی در پی نخواهد داشت.


## نمونه پروژه اندروید

برای دریافت نمونه پروژه می‌توانید به صفحه [گیت‌هاب (github.com/ecdco/SmartPOSAPISample)](https://github.com/ecdco/SmartPOSAPISample) اختصاص یافته به این موضوع مراجعه نمایید.






