title: THJCC writeup
date: 2024-12-19 17:49:32
tags: CTF 
---

# THJCC writeup

### 這場我沒有什麼打但看到幾題蠻有趣的 就想說來寫個write up >w<
url: https://ctf.scint.org

## Discord 0x2

去discord /get_flag  就能拿到Flag了

![image](https://hackmd.io/_uploads/HJQPHmbB1e.png)

## Welcome 0x2
把 ```https://ctf.scint.org/``` 丟burp或curl或```view-source:https://ctf.scint.org/```
就可以去找Flag了

>Flag: ```THJCC{Sn4ke_G4me_Mast3r}```

## S-box

source:
```python!
from secret import FLAG
import base64

Sbox = [
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16
    ]


cipher = bytes(Sbox[ch] for ch in base64.b64encode(FLAG))


print(cipher.hex())
```

output

```txt
b16e45b3d1042f9ae36a0033edfc966e00202f7f6a04e3f5aa7fbec7fc23b17f6a04c75033d12727
```

exploit

```python
import base64

# Sbox provided in the problem
Sbox = [
    0x63, 0x7C, 0x77, 0x7B, 0xF2, 0x6B, 0x6F, 0xC5, 0x30, 0x01, 0x67, 0x2B, 0xFE, 0xD7, 0xAB, 0x76,
    0xCA, 0x82, 0xC9, 0x7D, 0xFA, 0x59, 0x47, 0xF0, 0xAD, 0xD4, 0xA2, 0xAF, 0x9C, 0xA4, 0x72, 0xC0,
    0xB7, 0xFD, 0x93, 0x26, 0x36, 0x3F, 0xF7, 0xCC, 0x34, 0xA5, 0xE5, 0xF1, 0x71, 0xD8, 0x31, 0x15,
    0x04, 0xC7, 0x23, 0xC3, 0x18, 0x96, 0x05, 0x9A, 0x07, 0x12, 0x80, 0xE2, 0xEB, 0x27, 0xB2, 0x75,
    0x09, 0x83, 0x2C, 0x1A, 0x1B, 0x6E, 0x5A, 0xA0, 0x52, 0x3B, 0xD6, 0xB3, 0x29, 0xE3, 0x2F, 0x84,
    0x53, 0xD1, 0x00, 0xED, 0x20, 0xFC, 0xB1, 0x5B, 0x6A, 0xCB, 0xBE, 0x39, 0x4A, 0x4C, 0x58, 0xCF,
    0xD0, 0xEF, 0xAA, 0xFB, 0x43, 0x4D, 0x33, 0x85, 0x45, 0xF9, 0x02, 0x7F, 0x50, 0x3C, 0x9F, 0xA8,
    0x51, 0xA3, 0x40, 0x8F, 0x92, 0x9D, 0x38, 0xF5, 0xBC, 0xB6, 0xDA, 0x21, 0x10, 0xFF, 0xF3, 0xD2,
    0xCD, 0x0C, 0x13, 0xEC, 0x5F, 0x97, 0x44, 0x17, 0xC4, 0xA7, 0x7E, 0x3D, 0x64, 0x5D, 0x19, 0x73,
    0x60, 0x81, 0x4F, 0xDC, 0x22, 0x2A, 0x90, 0x88, 0x46, 0xEE, 0xB8, 0x14, 0xDE, 0x5E, 0x0B, 0xDB,
    0xE0, 0x32, 0x3A, 0x0A, 0x49, 0x06, 0x24, 0x5C, 0xC2, 0xD3, 0xAC, 0x62, 0x91, 0x95, 0xE4, 0x79,
    0xE7, 0xC8, 0x37, 0x6D, 0x8D, 0xD5, 0x4E, 0xA9, 0x6C, 0x56, 0xF4, 0xEA, 0x65, 0x7A, 0xAE, 0x08,
    0xBA, 0x78, 0x25, 0x2E, 0x1C, 0xA6, 0xB4, 0xC6, 0xE8, 0xDD, 0x74, 0x1F, 0x4B, 0xBD, 0x8B, 0x8A,
    0x70, 0x3E, 0xB5, 0x66, 0x48, 0x03, 0xF6, 0x0E, 0x61, 0x35, 0x57, 0xB9, 0x86, 0xC1, 0x1D, 0x9E,
    0xE1, 0xF8, 0x98, 0x11, 0x69, 0xD9, 0x8E, 0x94, 0x9B, 0x1E, 0x87, 0xE9, 0xCE, 0x55, 0x28, 0xDF,
    0x8C, 0xA1, 0x89, 0x0D, 0xBF, 0xE6, 0x42, 0x68, 0x41, 0x99, 0x2D, 0x0F, 0xB0, 0x54, 0xBB, 0x16
]

# Build the inverse S-box
inverse_Sbox = [0] * 256
for i, value in enumerate(Sbox):
    inverse_Sbox[value] = i

# Hexadecimal cipher
cipher_hex = "b16e45b3d1042f9ae36a0033edfc966e00202f7f6a04e3f5aa7fbec7fc23b17f6a04c75033d12727"
cipher_bytes = bytes.fromhex(cipher_hex)

# Reverse S-Box substitution
decoded_bytes = bytes(inverse_Sbox[b] for b in cipher_bytes)

# Reverse Base64 decoding
FLAG = base64.b64decode(decoded_bytes).decode()

print("FLAG:", FLAG)
```

>Flag: ```THJCC{1t_INDE3d_C0nFuSed_Me}```

##  girlfriend? 
給了一串密文：
```
WkdsWmIwbFRSazFaUkVKTFdIcHdhazFFTVdaa01EUTk=
```
直覺Base64 
丟cyberchef
![image](https://hackmd.io/_uploads/B14nAi-SJl.png)
發現它有三層base64組成 最後直覺 ROT47 
得到
![image](https://hackmd.io/_uploads/Hym713-Bkg.png)
接著 丟caesar decrypte答案就出來了

>Flag: ```THJCC{1_l0v4_y0U}```

##  notepad+++

source:

```python
import time
import hashlib
from flask import Flask, g, request, render_template, make_response, redirect, url_for
app = Flask(__name__)

@app.before_request
def auth():
    if not request.cookies.get('session'):
        res = make_response(redirect(url_for('root')))
        g.session = hashlib.md5(str(time.time()).encode()).hexdigest()
        res.set_cookie('session', g.session)
        return res

    g.session = request.cookies.get('session')

def getctx():
    try:
        with open(f'./tmp/{g.session}', mode='a+', encoding='utf8') as f:
            f.seek(0)
            ctx = f.read()
    except:
        with open(f'./tmp/{g.session}', mode='r', encoding='utf8') as f:
            ctx = f.read()
            
    return 'Hello World' if not ctx else ctx

@app.route('/')
def root():
    return render_template('index.html',text = getctx())

@app.route('/profile')
def profile():
    return render_template('profile.html', text = getctx())

@app.route('/save', methods=['POST'])
def save():
    with open(f'./tmp/{g.session}', mode='w', encoding='utf8') as f:
        f.write(request.form['text'])
    return 'ok'

if __name__ == '__main__':
    app.run('0.0.0.0', 80)
```

發現可以透過session去拿Flag

payload
```
../flag.txt
```
![image](https://hackmd.io/_uploads/SJ3KQn-ryl.png)

>Flag: ```THJCC{tmp_1n_🐍_01b4c87cabcca82b}```

##  Sumire hime 
題目：
```
u know that?
Sumire hime is a sword!
Can you tell me what the type of the sword?

    Flag Format:THJCC{}
    (without ?si=<Something>)
    e.g. THJCC{https://youtu.be/lkvP4m9A828}

https://naupjjin.github.io/about/

Author: chilin.h & 堇姬Naup
```

這題知道的當下覺得有點幹

這題把Sumire hime翻譯得到堇姬
然後...丟google search然後
看到紅月的影片 url就是flag了

>Flag: ```THJCC{https://youtu.be/x27Tiu2jdlE}```



只能說這次難度比去年高蠻多的 但這次也是給我之後參加pre-exam跟MyFirst的練習
加油！！！
