# Write-up: Cipher's Secret Message

*Link to the TryHackMe challenge: https://tryhackme.com/room/hfb1cipherssecretmessage*

This is a short write-up on how I decrypted Cipher's message.  
We received a secret message from Void, asking us to decrypt the following text:

``` a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm ```

*Along with it, we were given the code used to encrypt the message:*
```
from secret import FLAG

def enc(plaintext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(plaintext)
    )
with open("message.txt", "w") as f:
    f.write(enc(FLAG))
```
**** 

Before we can modify the code and decipher the message, we need to analyze it line by line.

```
from secret import FLAG
```
The variable FLAG is being imported from secret, this variable most likely contains the unencrypted string we are looking for.

**** 

```
def enc(plaintext):
```
The function enc is defined and takes plaintext as an argument. This is where FLAG is placed to encrypt it.

**** 

```
return "".join(
```

The str.join(iterable) function is used to combine an iterable into a single string. 
For example:
 

```
x = "!".join(["A", "B", "C"])
print(x)
# Output: A!B!C

x = "".join(["A", "B", "C"])
print(x)
# Output: ABC
```

****
```
chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base)
```
chr() converts an int into its corresponding ASCII character (e.g., chr(65) -> A; chr(97) -> a).
ord() does the opposite: it converts a character into its int representation.
So, c is a character, and two lines below, we see:
```
for i, c in enumerate(plaintext)
```
The enumerate() function allows us to loop over an iterable while keeping track of the index. 
Example:
```
x = 'aBcdf=g'
for i, c in enumerate(x):
    print(i, c)
# Output: 0 a, 1 B, 2 c, ...
```
**** 


Here, i is the index, and c is the character at that position.

The transformation formula is:
```
(ord(c) - base + i) % 26 + base
```

The base variable is set to 65 ('A') if c is uppercase or 97 ('a') if it's lowercase.
For example, with c = 'C' and i = 2:
```
chr((67 - 65 + 2) % 26 + 65)
chr(69) → 'E'
```
The % 26 ensures that the result wraps around the alphabet (range 0–25).

**** 

```
if c.isalpha() else c
```

This checks if c is an alphabetic character; if not, it returns c unchanged.


****
Finally, the code writes the encrypted output to message.txt, but this part isn't essential for understanding the encryption.
```
with open("message.txt", "w") as f:
    f.write(enc(FLAG))
```
**** 

# Summary of findings

- For each character c and index i
- If c is not alphabetic, it stays the same
- Otherwise:
- Calculate (ord(c) - base + i) % 26 + base
- Convert back to a character with chr()
- Repeat for every character in the string


# Decoding

To decrypt, we only need to change the formula so that instead of adding i, we subtract i:
```
chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) - i) % 26 + base)
```

 

Then, place the encrypted message into the function and print the result:
```
def dec(plaintext): 
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) - i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(plaintext)
    )

print(dec('a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm'))
```


Then just run the script in the terminal and that's it!

Other ways to solve this: You can also use a Progressive Caesar Cipher online decryption tool, such as https://www.dcode.fr/progressive-caesar-cipher, but that would kill the fun of the challenge!

*P.S.: Don't forget to format your final answer as THM{flagstring} before submitting it.*

*P.S.S: I know that this isn't really a challenge where a write-up is needed, due to its simplicity. I just wrote one because I wanted to hone my writing skills ;d*

# happy analyzing!!!!
