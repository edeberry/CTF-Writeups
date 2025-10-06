# Missioncritical1

**CTF:** SunshineCTF | **Date:** September 2025 | **Category:** Reversing
___

A file with no extension was provided. I used the Binary Ninja decompiler tool on [dogbolt.org](dogbolt.org) and found the following logic within the `main` function:

```C
int32_t main(int32_t argc, char** argv, char** envp)
{
    void* fsbase;
    int64_t rax = *(fsbase + 0x28);
    char s[0x40];
    sprintf(&s, "sun{%s_%s_%s}\n", "e4sy", "s4t3ll1t3", "3131");
    time(nullptr);
    printf("Satellite Status: Battery=%d%%, Orbit=%d, Temp=%dC\n", 0x50, 0x20, 0xffffffe7);
    printf("Enter satellite command: ");
    char buf[0x38];
    fgets(&buf, 0x32, stdin);
    
    if (strcmp(&buf, &s))
        puts("Access Denied!");
    else
        puts("Access Granted!");
```

The sprintf() function stood out, as it contains the flag format (`sun{}`) in the following line:

```C
sprintf(&s, "sun{%s_%s_%s}\n", "e4sy", "s4t3ll1t3", "3131");
```

* The `sprintf` function formats a string like `printf`, but instead of printing the result, it stores the string in a character array.
* The `%s` is a format specifier that indicates that the argument will be a string. This means that if printed, the output would be the flag properly formatted.
* Following the format, the flag would be "sun{e4sy_s4t3ll1t3_3131}". This can be easily automated by adding the function to a C script that prints the result:

```C
# include <stdio.h>

int main(void) {
    char s[100];

    sprintf(&s, "sun{%s_%s_%s}\n", "e4sy", "s4t3ll1t3", "3131");
    printf("%s", s);

    return 0;
}
```

This code runs the `sprintf` function then prints the result:

```C
sun{e4sy_s4t3ll1t3_3131}
```

This verifies the formatting of the flag.

**Conclusion:**

The binary constructs the flag with `sprintf`, so manually reproducing or printing the formatted string reveals the flag.

### Tools/References:

* [dogbolt.org](dogbolt.org): Decompiled the binary file with Binary Ninja.
* [sprintf on zetcode.com](https://www.zetcode.com/clang/sprintf/): Reference for understanding sprintf formatting and usage.
