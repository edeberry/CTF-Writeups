# Lunar Auth

**CTF:** SunshineCTF | **Date:** September 2025 | **Category:** Web Exploitation
___

A website url was provided ([comet.sunshinectf.games](https://comet.sunshinectf.games)), and we were told to "Infiltrate the LunarAuth admin panel" to find the flag. 

`robots.txt`is a file commonly found on websites to manage crawl traffic by telling crawlers which URLs they can access. I checked `robots.txt` ([comet.sunshinectf.games/robots.txt](https://comet.sunshinectf.games/robots.txt)) for hidden/disallowed paths, which led to this page:

```C
# tired of these annoying search engine bots scraping the admin panel page logins:

Disallow: /admin
```

I then navigated to `/admin` ([comet.sunshinectf.games/admin](https://comet.sunshinectf.games/admin)) on the website and reached a login prompt.

Inspecting the admin page showed the following code at the beginning of the JavaScript:

```js
/* 
To reduce load on our servers from the recent space DDOS-ers we have lowered login attempts by using Base64 encoded encryption ("encryption" 💀) on the client side.
    
TODO: implement proper encryption.
*/
    const real_username = atob("YWxpbXVoYW1tYWRzZWN1cmVk");
    const real_passwd   = atob("UzNjdXI0X1BAJCR3MFJEIQ==");
```

The username and password are stored directly in the JavaScript in Base64. I made a simple python script to decode the Base64 strings to UTF-8:

```python
import base64

def decodeBase64(input):
    output = base64.b64decode(input).decode("utf-8")
    return output

u = "YWxpbXVoYW1tYWRzZWN1cmVk"
p = "UzNjdXI0X1BAJCR3MFJEIQ=="

print("username: " + decodeBase64(u))
print("password: " + decodeBase64(p))
```

Result:

```
username: alimuhammadsecured
password: S3cur4_P@$$w0RD!
```

This can also be done automatically by an online Base64 decoder, such as using the Base64 tool on [cyberchef.org](https://cyberchef.org/). 

Once the username and password are decoded, submitting these credentials at the login page reveals the flag.

### Conclusion:

The website relied on client-side Base64 for "security," meaning that the credentials were stored in a place accessible to users (and not encrypted). By inspecting the page and decoding the strings in Base64, the username and password were revealed, allowing access to the flag.

### Tools/References:

* Browser devtools: used the JavaScript debugger to inspect the website's `admin` file.
* [Python Base64 library](https://docs.python.org/3/library/base64.html): Decoded the Base64 strings.