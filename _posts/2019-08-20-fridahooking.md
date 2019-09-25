---
title: ! "[iOS] frida hooking"
date: 2019-08-20
tags:
  - chaem
  - iOS
  - frida
categories:
  - frida
---

# class 추출

frida -U -l classes.js PID

classes.js

```js
for (var className in ObjC.classes){
if (ObjC.classes.hasOwnProperty(className))
{console.log(className);} }
```

# method 추출

frida -U -l methodofclass.js PID

methodofclass.js
```js
console.log("[*] Started: Find All Methods of a Specific Class");
if (ObjC.available) {
try {
var className = "JailbreakDetectionVC";
var methods = eval('ObjC.classes.' + className + '.$methods');
for (var i = 0; i < methods.length; i++) {
try { console.log("[-] "+methods[i]); }
catch(err) { console.log("[!] Exception1: " + err.message); }
} }
catch(err) { console.log("[!] Exception2: " + err.message); } }
else { console.log("Objective-C Runtime is not available!"); }
console.log("[*] Completed: Find All Methods of a Specific Class");
```

#return 값 확인

returnvalue.js

```js
if (ObjC.available) { 
    try {
        var className = "ANSMetadata"; 
        var funcName = "- isJailbroken"; 
        var funcName2 = "- computeIsJailbroken"
        var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
        var hook2 = eval('ObjC.classes.' + className + '["' + funcName2 + '"]');
//        console.log("ho");
        Interceptor.attach(hook.implementation, { 
//            onEnter: function(args) {
//                console.log("starrrrr");
//            }
//        } );
            onLeave: function(retval) { console.log("[*] Class Name: " + className);
            console.log("[*] Method Name: " + funcName); 
            console.log("\t[-] Type of return value: " + typeof retval);
            console.log("\t[-] Return Value: " + retval); } }); 
        
        Interceptor.attach(hook2.implementation, { 
            //            onEnter: function(args) {
            //                console.log("starrrrr");
            //            }
            //        } );
                        onLeave: function(retval2) { console.log("[*] Class Name: " + className);
                        console.log("[*] Method Name: " + funcName2); 
                        console.log("\t[-] Type of return value: " + typeof retval2);
                        console.log("\t[-] Return Value: " + retval2); } });
    } 
    catch(err) { console.log("[!] Exception2: " + err.message); } 
}  
else { console.log("Objective-C Runtime is not available!"); }
```

# overwrite.js

```js
if (ObjC.available) { 

try { 
var className = "ANSMetadata"; 
var funcName = "- isJailbroken";
var funcName2 = "- computeIsJailbroken";  
var hook = eval('ObjC.classes.' + className + '["' + funcName + '"]');
var hook2 = eval('ObjC.classes.' + className + '["' + funcName2 + '"]');

Interceptor.attach(hook2.implementation, {
    onEnter: function(args2){
        //onEnter는 후킹함수 진입 시 실행되며, args[0]에는 self객체가
        //args[1]에는 selector객체가 들어있어 접근 가능하며
        //args[2]에는 해당 함수의 매개변수들이 들어있다.
        //매개변수를 변경하고 싶다면 이곳에서 변경한다.
        console.log(args2[0]);
        console.log(args2[1]);
        console.log(args2[2]);
        console.log(args2[3]);
    } ,
    onLeave: function(retval2) { 
    console.log("[*] Class Name: " + className); 
    console.log("[*] Method Name: " + funcName2); 
    console.log("\t[-] Type of return value: " + typeof retval2); 
    console.log("\t[-] Original Return Value: " + retval2); 
    var newretval2 = ptr("0") 
    retval2.replace(newretval2) 
    console.log("\t[-] New Return Value: " + newretval2) } });

Interceptor.attach(hook.implementation, { 
    onEnter: function(args1){
        console.log(args1[0]);
        console.log(args1[1]);
        console.log(args1[2]);
    } ,
    onLeave: function(v1) { 
        console.log("[*] Class Name: " + className);
        console.log("[*] Method Name: " + funcName);
        console.log("\t[-] Type of return value: " + typeof(v1));
        console.log("\t[-] Original Return Value: " + v1);
        var newretval = ptr("0");
        v1.replace(newretval);
        console.log("\t[-] New Return Value: " + newretval);
    } });
} 

catch(err) { console.log("[!] Exception2: " + err.message); } } 

else { console.log("Objective-C Runtime is not available!"); }
```

# js 후킹코드 실행 방법

λ frida -U -f 앱패키지명 -l C:\Users\chaem\__handlers__\__ANSMetadata_isJailbroken_.js

# frida-trace 사용

frida-trace -U -f 앱패키지명 -i *

## open하는 클래스 추출

frida-trace -U -f 앱패키지명 -i "open*"

## init으로 시작하는 클래스 추출

λ frida-trace -U -i "init*" PID

# handler 생성

λ frida-trace.exe -U -i "*" -p 5907

λ frida-trace -U net.cross-dev.sbikabu2sp-stg -m "*[ANSMetadata isJailbroken]" -p 5624
Waiting for USB device to appear...
Instrumenting functions...
-[ANSMetadata isJailbroken]: Loaded handler at "C:\Users\chaem\__handlers__\__ANSMetadata_isJailbroken_.js"
Started tracing 1 function. Press Ctrl+C to stop.
