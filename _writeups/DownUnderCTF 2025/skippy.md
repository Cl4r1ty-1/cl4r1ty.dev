---
layout: writeup
title: skippy
tags: rev
---

We are given an exe file to reverse engineer.

Having not brought a windows machine with me for this CTF, I decided to open in Ghidra straight away.

{% highlight C %}
int __cdecl main(int _Argc,char **_Argv,char **_Env)

{
  undefined8 in_R9;
  undefined8 local_48;
  undefined8 local_40;
  undefined local_38;
  undefined8 local_28;
  undefined8 local_20;
  undefined local_18;
  
  __main();
  local_28 = 0xe8bef2e0e0d2d6e6;
  local_20 = 0xbed0e6eac4becad0;
  local_18 = 0x40;
  sandwich((char *)&local_28,0xbed0e6eac4becad0,_Env,in_R9);
  local_48 = 0xdedee4c2cedcc2d6;
  local_40 = 0xdededededededede;
  local_38 = 0x40;
  sandwich((char *)&local_48,0xdededededededede,_Env,in_R9);
  decrypt_bytestring((longlong)&local_28,&local_48,_Env,in_R9);
  return 0;
}
{% endhighlight %}

The decompiled main function looks something like this, we can see it defines a few variables and sends them to the `sandwich()` function, so we have a look at this function next.

{% highlight C %}
void sandwich(char *param_1,undefined8 param_2,undefined8 param_3,undefined8 param_4)

{
  stone(param_1,param_2,param_3,param_4);
  decryptor((longlong)param_1);
  stone(param_1,param_2,param_3,param_4);
  return;
}
{% endhighlight %}

The `sandwich` function just calls a couple of other functions with similar parameters. We decide to have a look at the `stone()` function next.

{% highlight C %}
void stone(char *param_1,undefined8 param_2,undefined8 param_3,undefined8 param_4)

{
  FILE *pFVar1;
  
  pFVar1 = (FILE *)(*(code *)__imp___acrt_iob_func)(2);
  __mingw_fprintf(pFVar1,"%s\n",(undefined1 (*) [10])s_Oh_no!_Skippy_is_about_to_trip!_14000b038,
                  param_4);
  pFVar1 = (FILE *)(*(code *)__imp___acrt_iob_func)(2);
  fflush(pFVar1);
  s_Oh_no!_Skippy_is_about_to_trip!_14000b038[0] = *param_1;
  return;
}
{% endhighlight %}

This function is what will cause the program to crash as it attempts to write to read only memory (segfault), this means that we need to avoid `stone()` functions.

The next function we look at is `decryptor()`

{% highlight C %}
void decryptor(longlong param_1)

{
  FILE *_File;
  FILE *_File_00;
  undefined8 uVar1;
  ulonglong local_10;
  
  _File = (FILE *)(*(code *)__imp___acrt_iob_func)(2);
  uVar1 = 0x2f;
  fwrite("Uh oh... Skippy sees a null zone in the way...\n",1,0x2f,_File);
  _File_00 = (FILE *)(*(code *)__imp___acrt_iob_func)(2);
  fflush(_File_00);
  __mingw_printf("%d\n",(undefined1 (*) [10])(ulonglong)uRam0000000000000000,uVar1,_File);
  for (local_10 = 0; local_10 < 0x10; local_10 = local_10 + 1) {
    *(byte *)(local_10 + param_1) = *(byte *)(local_10 + param_1) >> 1;
  }
  return;
}
{% endhighlight %}

The first part of this function will crash the program similar to one of the ways the `stone()` function does. The main part of the function we want to focus on is the for loop at the bottom. This will bit shift 16 bytes of the parameter to the right.

Lets go back to the `main()` function and then take a look at the `decrypt_bytestring()` function. 

{% highlight C %}
void decrypt_bytestring(longlong param_1,undefined8 *param_2,undefined8 param_3,undefined8 param_4)

{
  ulonglong uVar1;
  undefined8 local_168 [9];
  undefined8 uStack_120;
  undefined local_f8 [200];
  undefined8 *local_30;
  undefined8 local_28;
  ulonglong local_20;
  
  local_20 = 0x60;
  local_28 = 0x60;
  uStack_120 = 0x140001601;
  local_30 = local_168;
  memcpy(local_30,&DAT_14000a000,0x60);
  AES_init_ctx_iv((longlong)local_f8,param_1,param_2);
  uVar1 = local_20;
  AES_CBC_decrypt_buffer((longlong)local_f8,local_30,local_20);
  *(undefined *)(local_20 + (longlong)local_30) = 0;
  stone((char *)local_30,local_30,uVar1,param_4);
  puts((char *)local_30);
  return;
}
{% endhighlight %}

This appears to decrypt 96 bytes of data starting at the memory address `0x14000a000` using AES and the 2 parameters as the key and IV respectively. Lets take a look at the data at this address.

```
       14000a000 ae              ??         AEh
       14000a001 27              ??         27h    '
       14000a002 24              ??         24h    $
       14000a003 1b              ??         1Bh
       14000a004 7f              ??         7Fh    
       14000a005 fd              ??         FDh
       14000a006 2c              ??         2Ch    ,
       14000a007 8b              ??         8Bh
       14000a008 32              ??         32h    2
       14000a009 65              ??         65h    e
       14000a00a f2              ??         F2h
       14000a00b 2a              ??         2Ah    *
       14000a00c d1              ??         D1h
       14000a00d b0              ??         B0h
       14000a00e 63              ??         63h    c
       14000a00f f0              ??         F0h
       14000a010 91              ??         91h
       14000a011 5b              ??         5Bh    [
       14000a012 6b              ??         6Bh    k
       14000a013 95              ??         95h
       14000a014 dc              ??         DCh
       14000a015 c0              ??         C0h
       14000a016 ee              ??         EEh
       14000a017 c1              ??         C1h
       14000a018 4d              ??         4Dh    M
       14000a019 e2              ??         E2h
       14000a01a c5              ??         C5h
       14000a01b 63              ??         63h    c
       14000a01c f7              ??         F7h
       14000a01d 71              ??         71h    q
       14000a01e 55              ??         55h    U
       14000a01f 94              ??         94h
       14000a020 00              ??         00h
       14000a021 7d              ??         7Dh    }
       14000a022 2b              ??         2Bh    +
       14000a023 c7              ??         C7h
       14000a024 5e              ??         5Eh    ^
       14000a025 5d              ??         5Dh    ]
       14000a026 61              ??         61h    a
       14000a027 4e              ??         4Eh    N
       14000a028 5e              ??         5Eh    ^
       14000a029 51              ??         51h    Q
       14000a02a 19              ??         19h
       14000a02b 0f              ??         0Fh
       14000a02c 4a              ??         4Ah    J
       14000a02d d1              ??         D1h
       14000a02e fd              ??         FDh
       14000a02f 21              ??         21h    !
       14000a030 c5              ??         C5h
       14000a031 c4              ??         C4h
       14000a032 b1              ??         B1h
       14000a033 ab              ??         ABh
       14000a034 89              ??         89h
       14000a035 a4              ??         A4h
       14000a036 a7              ??         A7h
       14000a037 25              ??         25h    %
       14000a038 c5              ??         C5h
       14000a039 b8              ??         B8h
       14000a03a ed              ??         EDh
       14000a03b 3c              ??         3Ch    <
       14000a03c b3              ??         B3h
       14000a03d 76              ??         76h    v
       14000a03e 30              ??         30h    0
       14000a03f 72              ??         72h    r
       14000a040 7b              ??         7Bh    {
       14000a041 2d              ??         2Dh    -
       14000a042 2a              ??         2Ah    *
       14000a043 b7              ??         B7h
       14000a044 22              ??         22h    "
       14000a045 dc              ??         DCh
       14000a046 93              ??         93h
       14000a047 33              ??         33h    3
       14000a048 26              ??         26h    &
       14000a049 47              ??         47h    G
       14000a04a 25              ??         25h    %
       14000a04b c6              ??         C6h
       14000a04c b5              ??         B5h
       14000a04d dd              ??         DDh
       14000a04e b0              ??         B0h
       14000a04f 0d              ??         0Dh
       14000a050 d3              ??         D3h
       14000a051 c3              ??         C3h
       14000a052 da              ??         DAh
       14000a053 63              ??         63h    c
       14000a054 13              ??         13h
       14000a055 f1              ??         F1h
       14000a056 e2              ??         E2h
       14000a057 f4              ??         F4h
       14000a058 df              ??         DFh
       14000a059 51              ??         51h    Q
       14000a05a 80              ??         80h
       14000a05b d5              ??         D5h
       14000a05c f3              ??         F3h
       14000a05d 83              ??         83h
       14000a05e 18              ??         18h
       14000a05f 43              ??         43h    C

```

This can be easily written in hex as `ae27241b7ffd2c8b3265f22ad1b063f0915b6b95dcc0eec14de2c563f7715594007d2bc75e5d614e5e51190f4ad1fd21c5c4b1ab89a4a725c5b8ed3cb37630727b2d2ab722dc9333264725c6b5ddb00dd3c3da6313f1e2f4df5180d5f3831843`.

It is now clear that we need to decrypt this string of hex using AES, looking back at our main function we have the variables 
`local_28 = 0xe8bef2e0e0d2d6e6;` 
and 
`local_20 = 0xbed0e6eac4becad0;`. 

Because the decryptor function takes in 16 bytes from the address of `local_28`, we can treat them as a single variable: 

`some_bytes = 0xe8bef2e0e0d2d6e6bed0e6eac4becad0`

Looking back at the decryptor function, it bit shifts everything to the right, so I decide to do this in CyberChef. We also have to remember that in this particular executable it will be stored in little endian format. Swapping the endianness and shifting the bits reveals some readable text! 

`skippy_the_bush_`

Since the same operation the variables
`local_48 = 0xdedee4c2cedcc2d6;`
and
`local_40 = 0xdededededededede;`

(or simply just `some_bytes = 0xdedee4c2cedcc2d6dededededededede`)

We can do the same to it in CyberChef and this reveals more readable text:

`kangaroooooooooo`

So now we have a strings of AES encrypted bytes and two strings of readable characters. Looking back at how the `decrypt_bytestring()` function works, `skippy_the_bush_` seems to be passed in as the key, and `kangaroooooooooo` is passed in as the IV. With this information, we can attempt to decrypt the encrypted strings.

Putting all this into CyberChef, we successfully decrypted the string and got the flag!

![cyberchef_aes.png](https://github.com/Cl4r1ty-1/CTF/blob/main/DownUnderCTF%202025/images/cyberchef_aes.png?raw=true)

Flag: `DUCTF{There_echoes_a_chorus_enending_and_wild_Laughter_and_gossip_unruly_and_piled}`
