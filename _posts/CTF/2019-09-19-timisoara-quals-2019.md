---
layout: post
title:  "Timisoara Quals 2019 Writeup"
date:   2019-09-19
excerpt: "Only solved one challenge but hey, it's honest work"
categories: [CTF]
permalink: /:categories/:title
image: "/images/CTF/timisoara-quals-2019/timisoara.png"
---

## Overview
Team Name: acsii  
Position: 12  
Score: 3726/5826

![](/images/CTF/timisoara-quals-2019/scoreboard.png")

## John
Well I only solved one challenge that required some form of brain, so here goes.

### Initial Analysis
We're given an ["image"](https://github.com/jloh02/jloh02.github.io/blob/master/files/CTF/timisoara-quals-2019/john.png). Running `file`, we realize it's not even a proper PNG due to the magic bytes/file signature. Since we're told it's a png, the bytes are probably encrypted. In that case, let's take a look in a hex editor.

![](/images/CTF/timisoara-quals-2019/HexEditJohn.png)

If we were to scroll all the way down, all PNGs are supposed to end with `\x00\x00\x00\x00IEND`, instead we see `\x7A\x7A\x7A\x7A\x33\x3f\x34\x3e`. 

### Decrypting the PNG
At first we thought it was a caesar cipher. But the differences between ASCII values of `\x33` and `I` was not the same as `\x3f` and `E`. However, for all 4 bytes `\x7A` to be `\x00`, we will turn to one of our basics of encryption - XOR. We used the script below to XOR all bytes in the image.

``` python
x = [chr(int(pngHexStream[i:i+2],16)^0x7a) for i in range(0, len(he), 2)]
f = open('newJohn.png','wb')
f.write(''.join(x).encode("latin_1"))
print (ord(x[0]),x[1])
```

After that we get an image that we can open ... sort of.

![](/images/CTF/timisoara-quals-2019/new.png)

### Retrieving the Flag
The image is partially corrupted. Let's scroll through the hex dump again.

![](/images/CTF/timisoara-quals-2019/HexEditNew.png)

We notice a very odd string `HATZ-fL4G` which was probably what the clue in the image was trying to tell us. But the text looks like absolute trash so let's go back to the goto cipher - Caesar Cipher. Using a simple script below we get the flag.

``` python
inp = "HATZ-fL4G: WLPFWI~Eudy3bm3kqxoh$"
output = ""
for i in inp:
	output += chr(ord(i)-3)
print (output)
```

**Flag:** `TIMCTF{Brav0_j0hnule!}`
