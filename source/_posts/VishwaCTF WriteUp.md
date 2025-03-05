title: VishwaCTF'25 Write Up
date: 2025-3-5 11:39:00
tags: CTF
cover:
---

### This is VishwaCTF'25 writeup eng version

## QuadRant
It give us four pices of QRcode so I think that maybe build them together and the flag we come through and it actually is

![image](https://hackmd.io/_uploads/By5gASNjJl.png)

>Flag: VishwaCTF{aG9lMTIzNDU2c3Bhc3NhZ2U=}

## Chaos
It give us a code and a output file
source:

```py=
import random
import base64
import os

messages = []
with open('https://x.com/Abhinav04139720.txt', 'rb') as f:
    for line in f.readlines():
        messages.append(line.strip())

with open('flag.txt', 'rb') as f:
    flag = f.read().strip()
messages.append(flag)

random.shuffle(messages)

def xor_encrypt(msg):
    transformed = bytearray(msg)
    for i in range(len(transformed)):
        transformed[i] ^= (i % 256)  
    return base64.b85encode(transformed).decode() 

with open('output.txt', 'w') as f:
    for msg in messages:
        encrypted_msg = xor_encrypt(msg)
        f.write(f'{encrypted_msg}\n\n')

```

as we can see that it is using xor and base85 so just write a script to solve it 

exploit:

```py=
import base64

def xor_decrypt(encoded_msg):
    transformed = bytearray(base64.b85decode(encoded_msg))
    for i in range(len(transformed)):
        transformed[i] ^= (i % 256)
    return transformed.decode(errors='ignore')

with open('output.txt', 'r') as f:
    encrypted_messages = f.read().strip().split('\n\n')

decrypted_messages = [xor_decrypt(msg) for msg in encrypted_messages]

with open('decrypted_output.txt', 'w') as f:
    for msg in decrypted_messages:
        f.write(f'{msg}\n\n')![image](https://hackmd.io/_uploads/S1pdwtSikl.png)


print("Decryption complete. Check 'decrypted_output.txt'.")
```

>Flag: VishwaCTF{CrYpt0_cRyPT0_1g_It_1s_sOm3_7hiNg_t0_D0}

## Flames

when I start up the instance it show a page
![image](https://hackmd.io/_uploads/S1pdwtSikl.png)

ok it look like a form to let us input things so maybe we can decide to try sqli (? 

payload:
```
'UNION SELECT a; -- -
```

![image](https://hackmd.io/_uploads/rkZldKroJx.png)

and we can get this page and there is a link to another page when we click it we can get the flag >w<

> Flag: VishwaCTF{SQL_1nj3ct10n_C4n_Qu3ry_Your_He4rt}
