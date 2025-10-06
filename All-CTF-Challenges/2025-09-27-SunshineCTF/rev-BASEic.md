# BASEic

**CTF:** SunshineCTF | **Date:** September 2025 | **Category:** Reversing
___

A file with no extension was provided. Opening the file in a text editor showed that the very first bytes contained the string `ELF`, which identifies it as an ELF executable.

I used the Binary Ninja decompiler tool on [dogbolt.org](dogbolt.org) and found the following logic within the `main` function:

```C
    if (strlen(&var_48) != 0x16)
        puts("You don't get the flag that easily");
    else
    {
        char* rax_5 = sub_4012c6(&var_48, strlen(&var_48));
        
        if (strncmp(rax_5, "c3Vue2MwdjNyMW5nX3V", 0x13))
            puts("Closer");
        else if (strncmp(&rax_5[0x13], &var_56, strlen(&var_56)))
            puts("Soo Close");
        else
            puts("You got it, submit the flag!");
```

**Breaking down the if statements:**

* **1st if:** `strlen(&var_48) != 0x16` checks that the input length is 0x16 (hex) = 22 bytes. (The final flag is 22 characters.)
* **2nd if:** `(strncmp(rax_5, "c3Vue2MwdjNyMW5nX3V", 0x13))` compares the first 0x13 (hex) = 19 bytes of `rax_5`, or the input, to the **string** `"c3Vue2MwdjNyMW5nX3V"`.
* **3rd if:** The `strncmp` compares the remaining bytes to `var_56`, which is initiazlized as the **string** `"yX0I0NTM1fQ=="`.

The strings resemble Base64, so I concatenated and decoded them as shown in the solution below.

### Solution:

Concatenating the two Base64 strings gives `"c3Vue2MwdjNyMW5nX3VyX0I0NTM1fQ=="`. This can be decoded to UTF-8 with a simple python script:

```python
import base64
flag = "c3Vue2MwdjNyMW5nX3VyX0I0NTM1fQ=="
print(base64.b64decode(flag).decode('utf-8'))
```

Result (flag):

```
sun{c0v3r1ng_ur_B4535}
```

### Conclusion:

* The binary expects the input's first 19 bytes to match `"c3Vue2MwdjNyMW5nX3V"` and the remainder of the input to match `"yX0I0NTM1fQ=="`.
* These two strings are in Base64, so when concatenated and decoded, the flag is produced.

### Tools/References:

* [dogbolt.org](dogbolt.org): Decompiled the binary file with Binary Ninja.
* [Python Base64 library](https://docs.python.org/3/library/base64.html): Decoded the Base64 strings.
