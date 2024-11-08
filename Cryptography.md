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
Giving ciphertext as input in the input() function gavw:  
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
Initially I was unsure of what do it after this; I thought of giving it was input to the above program again, which just gave b as the output.  
I tried submitting it as the flag, which turned out to be correct.  

flag: `picoCTF{adlibs}`
