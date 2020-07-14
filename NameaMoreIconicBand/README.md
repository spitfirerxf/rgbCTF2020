# Name a More Iconic Band

>I'll wait.
The flag for this challenge is all the passwords in alphabetical order, each separated by a single white-space as an MD5 hash in lower case
md5(passwordA passwordB passwordC ...)
Example: if the passwords were "dog" and "cat", the flag would be
rgbCTF{md5("cat dog")}
rgbCTF{b89526a82f7ec08c202c2345fbd6aef3}

And a huge file of 1GB accompanying the challenge. First doing `file` command, I thought I was doing reversing challenge (because of the compressed file of ~200MB) with weird twist, but it turned out to be a memory forensics challenge. Know it from experience, that no challenge makers are able to reduce the file size of memory forensics to be under 1GB hahahaha

Anyway, to finish this challenge, we use `volatility`, you can find it [here](https://github.com/volatilityfoundation/volatility). So the thing we're looking is simple enough: find all the passwords.

First, I needed to do `imageinfo` to see the type of OS it's on.
```
python vol.py -f data imageinfo
```
Several minutes later, we got the OS, it's in Windows7 SP1 x64. We will use that profile to start digging the passwords. Volatility has an easy way to parse password hash from the file. It's a one line command:
```
python vol.py -f data --profile=Win7SP1x64 hashdump
```
It's a bit odd to think that Windows always kept password hashes in memory all the time. The result is out.
```
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:aad3b435b51404eeaad3b435b51404ee:756c599880f6a618881a49a9dc733627:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
pablo honey:1001:aad3b435b51404eeaad3b435b51404ee:7aaedbb86a165e8711cb46ee7ee2d475:::
the bends:1002:aad3b435b51404eeaad3b435b51404ee:dcbd05aa2cdd6d00b9afd4db537c4aa3:::
ok computer:1003:aad3b435b51404eeaad3b435b51404ee:74dea9ee338c5e0750e000d6e7df08e1:::
kid a:1004:aad3b435b51404eeaad3b435b51404ee:c9c55dd07a88b589774879255b982602:::
amnesiac:1005:aad3b435b51404eeaad3b435b51404ee:7b05eefac901e1c852baa61f86c4376f:::
hail to the thief:1006:aad3b435b51404eeaad3b435b51404ee:8eb2b699eeff088135befbde0880cfd4:::
in rainbows:1007:aad3b435b51404eeaad3b435b51404ee:419c1a5a5de0b529406fb04ad5e81d39:::
the king of limbs:1008:aad3b435b51404eeaad3b435b51404ee:a82da1c9a00f83a2ca303dc32daf1198:::
a moon shaped pool:1009:aad3b435b51404eeaad3b435b51404ee:4353a584830ede0e7d9e405ae0b11ea4:::
```
With a little bit of editing, I ran all the hashes through CrackStation.
![hashes result](https://github.com/spitfirerxf/rgbCTF2020/raw/master/NameaMoreIconicBand/Pic1.png)

Sort it all alphabetically into one line:
```
anyone can play guitar burn the witch idioteque karma police lotus flower my iron lung pyramid song supercollider there, there weird fishes/arpeggi
```
Ran it through MD5 on CyberChef, and we got the hashflag. The flag is `rgbCTF{cf271c074989f6073af976de00098fc4}`
