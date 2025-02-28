title: NHNC offical writeup
date: 2024-11-24 17:49:32
tags: CTF
cover:
---

# NHNC LemonTea writeup

## Secret Rot13
Open the two file

encrypte code
```python
def encrypt(text, key):
    encrypted_text = ""
    for i, char in enumerate(text):
        offset = ((i + 1 + key) * (i + 1)) % 26 
        if 'A' <= char <= 'Z':
            new_char = chr((ord(char) - ord('A') + offset) % 26 + ord('A'))
        elif 'a' <= char <= 'z':
            new_char = chr((ord(char) - ord('a') + offset) % 26 + ord('a'))
        else:
            new_char = char 
        encrypted_text += new_char
    return encrypted_text

# 測試範例
key = 7
plaintext = "NHNC{TEST}"
ciphertext = encrypt(plaintext, key)
print("加密後的密文:", ciphertext)

```
This code is let the word do ROT
so find read the code and write a code to decrypte

code for decrypte

```py
def decrypt(encrypted_text, key):
    decrypted_text = ""
    for i, char in enumerate(encrypted_text):
        offset = ((i + 1 + key) * (i + 1)) % 26 
        if 'A' <= char <= 'Z':
            new_char = chr((ord(char) - ord('A') - offset + 26) % 26 + ord('A'))
        elif 'a' <= char <= 'z':
            new_char = chr((ord(char) - ord('a') - offset + 26) % 26 + ord('a'))
        else:
            new_char = char 
        decrypted_text += new_char
    return decrypted_text

# 加密的文字
encrypted_text = "VZRU{Y0k_yd0w_Z0o_ti_rsslyxli}"

# 嘗試所有可能的密鑰並輸出解密結果
for key in range(26):  # 假設密鑰範圍是 0 到 25
    decrypted_text = decrypt(encrypted_text, key)
    print(f"密鑰 {key}: {decrypted_text}")
```

## AES？
open output.txt
```txt
Enter IV: 1234567890987654
Secret Key: 1234567890987654
output: TkdU8sqjliuakA+nj2aEmbDf+AaJwASfPuooaKadCqg=
```
Is AES

Put it into an online decrypter

The flag:

>Flag: NHNC{Y0u_kn0w_AES}
