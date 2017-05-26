# cordova-plugin-unionpay

Cordova plugin for Unionpay, supports **iOS** and **Android**.

最下面有一部分**good practice**

## Installation

```
# Official environment, Cordova
cordova plugin add https://github.com/Santino-Wu/cordova-plugin-unionpay.git --variable UNIONPAYMODE=00

# Develop environment, Cordova
cordova plugin add https://github.com/Santino-Wu/cordova-plugin-unionpay.git --variable UNIONPAYMODE=01

# Official environment, Ionic
ionic plugin add https://github.com/Santino-Wu/cordova-plugin-unionpay.git --variable UNIONPAYMODE=00

# Develop environment, Ionic
ionic plugin add https://github.com/Santino-Wu/cordova-plugin-unionpay.git --variable UNIONPAYMODE=01
```

> The above parameter `--variable UNIONPAYMODE=00` means invoking payment in official environment.
> If you don't have the permission for invoking payment in official environment, set variable `UNIONPAYMODE` as `01`(which is **develop** mode) to invoke payment.

## Usage

``` javascript
/**
 * @param {String} tn - The transaction number which fetched from Unionpay server
 * @param {Function} successFn - The callback function when payment was successful
 * @param {Function} failureFn - The callback function when payment was failed
 * @description
 * Requests payment with transaction number
 */
unionpay.pay(tn, successFn, failureFn);
```

## Example

``` javascript
// Fetches transaction number from your server
var tn = '...';

window.unionpay.pay(tn, function (msg) {
    // Payment was successful, do something
    // ...
}, function (error) {
    // Payment was failed, do something
    // ...
});
```

## 缺少的一个步骤：偷偷地从别人的一个服务器获得一个测试tn
修改index.html文件，允许发起http request
``` html
<meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: https://ssl.gstatic.com 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline'; media-src *; connect-src http://101.231.204.84:*">
```

修改JS代码，从一个url来动态获取一个tn
``` javascript
var app = {
    // Application Constructor
    initialize: function() {
        document.addEventListener('deviceready', this.onDeviceReady.bind(this), false);
    },

    // deviceready Event Handler
    //
    // Bind any cordova events here. Common events are:
    // 'pause', 'resume', etc.
    onDeviceReady: function() {
        
        alert('hahaha');
        var txt = document.getElementById("txt1");
        document.getElementById("myBtn").addEventListener("click",function(){
            MyCordovaPlugin.getDate(function(res){
               txt.value = res;
            });
        },false);

        document.getElementById("myBtnPay").addEventListener("click",function(){
            var url = "http://101.231.204.84:8091/sim/getacptn";
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function() { 
                if (xmlHttp.readyState == 4 && xmlHttp.status == 200){
                    var tn = xmlHttp.responseText;
                    txt.value = tn;
                    unionpay.pay(tn, function (msg) {
                        // Payment was successful, do something
                        // ...
                        alert('successful');
                    }, function (error) {
                        // Payment was failed, do something
                        // ...
                        alert('failed');
                    });
                }
            }
            xmlHttp.open("GET", url, true); // true for asynchronous 
            xmlHttp.send(null);
            
        },false);
        
        this.receivedEvent('deviceready');
    },

    // Update DOM on a Received Event
    receivedEvent: function(id) {
        var parentElement = document.getElementById(id);
        var listeningElement = parentElement.querySelector('.listening');
        var receivedElement = parentElement.querySelector('.received');

        listeningElement.setAttribute('style', 'display:none;');
        receivedElement.setAttribute('style', 'display:block;');

        console.log('Received Event: ' + id);
    }
};

app.initialize();
```


## 测试银行账号
```
测试账号	测试信息
姓名	张三
手机号码	18100000000
证件类型	01身份证
CVN2	248
有效期	1219
证件号	510265790128303
华夏银行贷记卡	6226388000000095
密码	111101
验证码	123456（先点获取验证码之后再输入）
```
