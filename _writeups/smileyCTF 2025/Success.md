---
layout: writeup
title: Success
tags: rev
---

We are given a Haskell file that takes a string (`flag`) as an argument, checks if it is 39 characters long, converts each character to its decimal ASCII value and places those into an array (`chars`).

It then checks certain values in the array against many arithmetic, logical and bitwise operations.

Our goal is to solve all these "equations" to find the correct values of the flag in the array, and then convert those back to a string.

Through some research I came across a tool called "z3" which is a SMT solver. This tool allows us to add constraints on certain values and solve for them. You can use this tool in a python script which is what I did. After learning the basics of z3, this was the script I used to solve this challenge

{% highlight python %}
from z3 import *

flag = [BitVec(f'c{i}', 32) for i in range(39)] # init array of 39 BitVec values
s = Solver()

for c in flag:
        s.add(c >= 32, c <= 126) # ensure all values are printable ascii

# add each constraint from hs script
s.add(flag[37] * flag[15] == 3366)
s.add(flag[8] + flag[21] == 197)
s.add(flag[8] * flag[13] == 9215)
s.add(flag[0] * flag[3] == 2714)
s.add(flag[3] + flag[21] == 159)
s.add(flag[1] * flag[20] == 5723)
s.add(flag[6] | flag[37] == 105)
s.add(flag[11] * flag[7] == 11990)
s.add(flag[29] & flag[25] == 100)
s.add(flag[16] | flag[29] == 127)
s.add(flag[20] - flag[6] == -8)
s.add(flag[21] + flag[20] == 197)
s.add(flag[2] + flag[36] == 77)
s.add(flag[35] * flag[11] == 3630)
s.add(flag[4] * flag[3] == 2714)
s.add(flag[35] ^ flag[6] == 72)
s.add(flag[25] + flag[24] == 221)
s.add(flag[14] * flag[36] == 3465)
s.add((flag[15] - flag[11]) - 148 == -156)
s.add(flag[37] + flag[17] == 138)
s.add(flag[1] ^ flag[38] == 70)
s.add(flag[9] + flag[29] == 212)
s.add(flag[30] - flag[10] == 7)
s.add(flag[10] + flag[33] == 206)
s.add(flag[7] * flag[15] == 11118)
s.add(flag[28] * flag[14] * 55 == 641025)
s.add((flag[7] | flag[4]) | 216 == 255)
s.add(flag[24] + flag[4] == 151)
s.add(flag[2] * flag[30] == 4928)
s.add(flag[5] + flag[22] == 224)
s.add(flag[18] | flag[36] == 127)
s.add(flag[13] + flag[34] == 195)
s.add(flag[9] | flag[17] == 111)
s.add(flag[12] * flag[9] == 10403)
s.add(flag[25] ^ flag[27] == 23)
s.add(flag[13] ^ flag[34] == 59)
s.add(flag[18] + flag[31] == 200)
s.add(flag[17] + flag[32] == 213)
s.add(flag[2] * flag[12] == 4444)
s.add(flag[24] * flag[31] == 11025)
s.add(flag[5] * flag[0] == 5658)
s.add(flag[10] + flag[32] + 228 == 441)
s.add(flag[35] * flag[0] == 1518)
s.add(flag[30] | flag[8] == 113)
s.add(flag[28] - flag[34] == 11)
s.add(flag[26] * flag[14] == 9975)
s.add(flag[31] * flag[22] == 10605)
s.add(flag[26] * flag[32] * 239 == 2452140)
s.add(flag[28] * flag[38] == 13875)
s.add(flag[18] + flag[16] == 190)
s.add(flag[27] + flag[26] + 96 == 290)
s.add(flag[22] - flag[38] == -24)
s.add(flag[33] + flag[5] == 224)
s.add(flag[19] * flag[16] == 10355)
s.add(flag[27] + flag[1] == 158)
s.add(flag[33] + flag[12] == 202)
s.add(flag[19] * flag[23] == 10355)

if s.check() == sat:
        model = s.model()
        result = ''.join([chr(model[c].as_long()) for c in flag]) # convert all decimal values to acsii and join string together
        print(f'Flag: {result}')
else:
        print("womp womp")

{% endhighlight %}

In this script, I import all modules from the z3 library and create an initial array of 39 BitVector values. This is essential because if they are integers you cannot add bitwise constraints as easily. 

I then initialise a z3 `Solver()`. The `for` loop add the constraint on all values in the array to be printable ascii characters (between 32 and 126 inclusive). After that I go ahead and add every constraint from the Haskell script to their relevant positions in the array. 

The end is just z3 jargon which I assume first checks if all values have been solved and if so gets the result from the model. I then convert all of the solved decimal values to ASCII,  join the array into a string and print the flag.

```
┌──(kali㉿kali)-[~/Downloads/success]
└─$ python solve.py  
Flag: .;,;.{imagine_if_i_made_it_compiled!!!}
```

Flag: `.;,;.{imagine_if_i_made_it_compiled!!!}`
