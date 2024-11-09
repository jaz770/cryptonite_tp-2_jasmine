# Cryptography
## C3
The challenge is a cyclical cipher problem. A cyclical cipher involves shifting all the alphabets in a text by some number of positions. The position is the key/shift. So, for a shift of 2, A becomes C, B becomes D, and so on.  

Initially openining the ciphertext gave this:  
```
DLSeGAGDgBNJDQJDCFSFnRBIDjgHoDFCFtHDgJpiHtGDmMAQFnRBJKkBAsTMrsPSDDnEFCFtIbEDtDCIbFCFtHTJDKerFldbFObFCFtLBFkBAAAPFnRBJGEkerFlcPgKkImHnIlATJDKbTbFOkdNnsgbnJRMFnRBNAFkBAAAbrcbTKAkOgFpOgFpOpkBAAAAAAAiClFGIPFnRBaKliCgClFGtIBAAAAAAAOgGEkImHnI
```
Since convert.py seemed to be an encoding algoritm, I reversed the program to act as a decoder.
```
import sys
chars = ""
from fileinput import input
for line in input():
  chars += line

lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

out = ""

prev = 0
for char in chars:
  cur = lookup1.index(char)
  out += lookup2[(cur - prev) % 40]
  prev = cur

sys.stdout.write(out)
```
Giving ciphertext as input in the input() function gave:  
```
#asciiorder
#fortychars
#selfinput

chars = ""
from fileinput import input
for line in input():
    chars += line
b = 1 / 1

for i in range(len(chars)):
    if i == b * b * b:
        print chars[i] #prints
        b += 1 / 1
```

>[!NOTE]
>Encoding this using convert.py gives: DLSeGAGDgBNJDQJDCFSFnRBIDjgHoDFCFtHDgJpiHtGDmMAQFnRBJKkBAsTMrsPSDDnEFCFtIbEDtDCIbFCFtHTJDKerFldbFObFCFtLBFkBAAAPFnRBJGEkerFlcPgKkImHnIlATJDKbTbFOkdNnsgbnJRMFnRBNAFkBAAAbrcbTKAkOgFpOgFpOpkBAAAAAAAiClFGIPFnRBaKliCgClFGtIBAAAAAAAOgGEkImHnI
>back again.


The decoded file seems to print sections of the file passed to it as input based on the condition i==b^3.  
Since the comments mention selfinput, I passed the code saved as a text file(.txt) to the the program (And fixed the print chars[i] line to print (chars[i])).  

The input to input() was: `input(files=r'<absolute path to downloads>\picoctf\decoded.txt')`  
This gave:  
```
a
d
l
i
b
s
```
Initially I was unsure of what do after this; I thought of giving it as input to the above program again, which just gave b as the output.  
I tried submitting it as the flag, which turned out to be correct.  

flag: `picoCTF{adlibs}`


## Custom Encryption

The ciper is obtained through the the shared key and the semi cipher (which in turn is gotten through the text key) so, to decrypt the cipher, I'll need the shared key.
Since g and p are fixed and a and b are given in the enc_flag file, u and v can be generated, and through those, the shared key. Then, the cipher can be converted to a semi cipher.
Entering: 
```
p = 97
g = 31
a = 94
b = 21
print(f"a = {a}")
print(f"b = {b}")
u = generator(g, a, p)
v = generator(g, b, p)
key = generator(v, a, p)
b_key = generator(u, b, p)
shared_key = None
if key == b_key:
    shared_key = key
else:
    print("Invalid key")
print("u and v = ", u,", ", v)
print("Shared key = ", shared_key)
```
Gives the output:  
```
a = 94
b = 21
u and v =  43, 8
Shared key =  47
```
Using this shared key, I wrote a decrpyt() code and got the semi cipher.  
```
def decrypt(code, key):
    semi_cipher = []
    for i in code:
        semi_cipher.append(chr(int(((i)/key/311))))
    return semi_cipher
```
I thought I'd have to figure out a way to reverse the the dynamic xor enxryption but found that if the xor encrypted text and the text key are passed as inputs to the the function, it will return the plaintext since xor is reversible. (Simiarly, if the cipher and the plain text were passed as arguments, xoring them would give the text key.)  
Thus, the code was: 
```
def dynamic_xor_decrypt(semicipher, text_key):
    plain_text= ""
    key_length = len(text_key)
    for i, char in enumerate(semicipher[::]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        plain_text += encrypted_char
    return plain_text
```
I removed the [::-1] in enumerate since that would decrpyt the cipher, reversed, which would give the wrong answer text.  
After I'd gotten the decoded plain text, I revered it and printed it.  
```
rev_text = (dynamic_xor_decrypt(semiciph, "trudeau"))
print(rev_text[::-1])
```

flag: ```picoCTF{custom_d2cr0pt6d_8b41f976} ```

### Incorrect Tangents  
I thought I'd have to figure out a way to get the text key that I'd pass to xor_encrypt. I assumed that the text_key and plain_text length would be equal so I could just use the semi_cipher length to get the key_char through len(semi_cipher).  When I read up on xor, I understood that there's actually no such requirement and the function wraps around to the beginning of the text_key if the data has not been fully encrypted in one go.  
Then I tried using "trudeau" as the text key (since it was given as the key to "message"), which worked.
