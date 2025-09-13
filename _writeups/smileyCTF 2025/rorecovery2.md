---
layout: writeup
title: rorecovery2
tags: forensics
---

#### QUICK DISCLAIMER:
I did use the techniques explained in my rorecovery1 write up to recover and open the file in Roblox Studio to solve this challenge. Even though I solved this challenge and not the first one during the CTF, I thought it would make more sense to include that process in my writeup for the first challenge, as this was the intended order of completion.

#### Writeup:
As explained in the above disclaimer, please refer to the `rorecovery1` writeup for how to recover this file to be opened in Roblox Studio.

Once I opened the patched file in Roblox Studio, I started looking around some of the `Workspace` objects in the Explorer. Since the purpose of this challenge was to find the "hidden asset", I was looking for anything that seemed off or out-of-place.

![init_explorer.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/init_explorer.png?raw=true)

In the `Gameplay` folder, all of the objects looked the same, with nothing looking like it didn't belong.

![zoom_to.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/zoom_to.png?raw=true)

While looking through the `LevelArt` folder, I used the `Zoom to` tool on any objects that caught my eye to see if it was hidden from the player on the map. I did this until I came across this interesting looking asset in the `Layout` folder

![base.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/base.png?raw=true)

This caught my eye as it was the only object with a child item in this folder. Zooming to the `base` led me to an object in the game, but moving away from that and trying to zoom to the `LegitBase` did absolutely nothing. This led me to believe that this was the hidden asset I was looking for.

![legit_base.png](https://github.com/Cl4r1ty-1/CTF/blob/main/smileyCTF%202025/images/legit_base.png?raw=true)

Opening this asset in the `Properties` window gives us the asset ID that we need to complete our flag. Since the flag format is `.;,;.{assetname_id}` we just need to put these values into the flag and submit.

Flag: `.;,;.{LegitBase_137276802718496}`
