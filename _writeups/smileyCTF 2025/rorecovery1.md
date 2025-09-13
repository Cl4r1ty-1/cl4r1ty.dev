---
layout: writeup
title: rorecovery1
tags: forensics
---

#### QUICK DISCLAIMER:
I did not solve this part of the challenge during the competition, but since I got very close and found this challenge interesting, I re-attempted it after the CTF ended. This write-up is based on that attempt.

#### Writeup:

We are given a single file with no extension, so my first instinct is to open it in a hex editor to see if there are any file header signatures present.

After opening the file in https://hexed.it, I see the file header is: `3C 72 62 6C 78 21 25 FF 0D 0A 15 0A 00 00 68 00`.

![file_header.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/file_header.png?raw=true)

Since I am not familiar with this header, I look on Wikipedia's "List of file signatures" (https://en.wikipedia.org/wiki/List_of_file_signatures) to see what type of file this could be.

![wikipedia.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/wikipedia.png?raw=true)

From this we can see it is a Roblox place file used by Roblox game developers to save their games locally. I then follow the <sup>71</sup> footnote to see if that leads me to anymore info. There I find a PDF with a lot of information about this type of file (https://www.classy-studios.com/Downloads/RobloxFileSpec.pdf). 

I decided to read more about the file header since the header in the challenge file did not match the header in the Wikipedia page. 

![fixed_header_pdf.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/fixed_header_pdf.png?raw=true)

After reading this I modified the file header of the challenge file to match the one outlined in the PDF.

![fixed_header_hexedit.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/fixed_header_hexedit.png?raw=true)

After that I decided to download the Roblox Studio software on a Windows machine to see if I could open the file. So after exporting it from hexedit and changing the file extension to `.rbxl`, I loaded up the Roblox software to be met with this error:

![rip_studio.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/rip_studio.png?raw=true)

I doubled checked the header about 10 times before realising that the footer might have to do with something. So I scroll all the way to the bottom of the challenge file in hexedit and looked for the footer section of the previously mentioned PDF.

And there it was, the footer also did not match the end data outlined in the PDF, so I modified that in the hex editor.

![fixed_footer_maybe.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/fixed_footer_maybe.png?raw=true)

After exporting the file again and trying to open it in Roblox Studio, I encountered a similar error to last time. This led to me wondering if the PDF was still accurate as I didn't know when it was written, so I decided to compare my challenge file to a valid `.rbxl` file. So I create a "Platformer" template inside Roblox Studio, save it to a file and import it into hexedit.

Comparing the headers, they seemed to be the same. However, looking at the footer, it was slightly off by 2 null bytes (`00 00`). So I added these null bytes where they needed to go and ended up with this footer:

![fixed_footer_yes.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/fixed_footer_yes.png?raw=true)

After this, I exported the file and attempted to open it in Roblox Studio again, and this time, I was successful!

![studio_init.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/studio_init.png?raw=true)

I have a bit of experience with Roblox Studio as I used to create some (not very good) games on here when I was younger. So my first instinct was to look at some of the scripts to see if there was any scripts that obfuscated data. That's when I came across the `validateMaster` script in `ServerScriptStorage\Utility\TypeValidation\`. On the surface it looked pretty normal, but the return statement appeared heavily modified. Looking a bit closer I could make out a few interesting words.

![weird_return.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/weird_return.png?raw=true)

After realising I just had to look through out-of-place readable characters after `hashId`, I managed to make out a pastebin link. `pastebin raw FncwDp3L` = https://pastebin.com/raw/FncwDp3L. This led me to a page with the following code:

```
local __LEGACY_TYPE_BLOB = {
	46,59,44,59,46,123,99,48,114,114,117,112,116,51,100,95,52,110,100,95,
	104,49,100,100,51,110,33,95,52,110,121,95,48,103,115,95,114,51,109,
	51,109,98,101,114,95,112,48,114,116,52,108,95,115,52,118,49,48,114,63,125
}
```

All these looked like ASCII values to me so I just wrote a quick Python script to convert them to text:

```
__LEGACY_TYPE_BLOB = [
	46,59,44,59,46,123,99,48,114,114,117,112,116,51,100,95,52,110,100,95,
	104,49,100,100,51,110,33,95,52,110,121,95,48,103,115,95,114,51,109,
	51,109,98,101,114,95,112,48,114,116,52,108,95,115,52,118,49,48,114,63,125
]


flag = "".join([chr(i) for i in __LEGACY_TYPE_BLOB])

print(flag)

```

Which gave me the flag: `.;,;.{c0rrupt3d_4nd_h1dd3n!_4ny_0gs_r3m3mber_p0rt4l_s4v10r?}`
