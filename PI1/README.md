# PI 1: Magic in the Air
> We are investigating an individual we believe is connected to a group smuggling drugs into the country and selling them on social media. You have been posted on a stake out in the apartment above theirs and with the help of space-age eavesdropping technology have managed to extract some data from their computer. What is the phone number of the suspect's criminal contact?
>flag format includes country code so it should be in the format: rgbCTF{+00000000000}

So we got a file simply named `data`, and when we run `file` command to see what file it is, it showed that it's a BTSnoop version 1.0. It turns out to be a Bluetooth sniffing dump file. It also fits the title of Magic in the Air. Interesting forensics challenge.

First thing I do was looking for tools to open it. A bit of exploring. Python library, Linux command, and ended up using Wireshark. Because this was captured using snooping tool, we should see the communication happened as a third party instead of bluetooth device vs host. When scrolling down a bit on Wireshark, I found a short string between the hexes several times:
```
'2B<öÁ	G613Ì
```
When we run strings we can also see the `G613` scattered around the file. It turned out to be a wireless Logitech keyboard we're snooping.

Scrolling down more, I found some repeating pattern with different tailing hex.

![Hex 1](https://github.com/spitfirerxf/rgbCTF2020/raw/master/PI1/Pic1.png)
![Hex 1](https://github.com/spitfirerxf/rgbCTF2020/raw/master/PI1/Pic2.png)

See the tailing `1c` and `12`? Apparently that's our key to the flag. Keyboard are using certain hexes to send keyboard signal to the host, but it's not in ASCII. So, I dumped all of the data to JSON file to make it easier to parse. See the `handle value.json` file for the export result.

I made the parser in Python, and it's pretty simple, just get the `btatt` values out of the JSON, cut it out to get the actual keypress, then remap it with keyboard hex keys. I found it on a Github Gist [here](https://gist.github.com/willwade/30895e766273f606f821568dadebcc1c#file-keyboardhook-py-L42). I need to reverse the key and value, though. See `bt.py` for the actual parser.

Ran the `bt.py`, I got a long list of all the pressed keys on the keyboard. After some rewriting, the keyboard pressed more or less like this:
```
yo man sorry for the delay trying to get this keyboard workin yea its new wireless man
been moving product speakin of you need to contact my boy right
ye
should be fine
just say ohnny
sent you
alright
lemme get you the number
hold up
im looking for its his burner
got it written down somewhere
yeah got it
0736727859
mind it is a swedish number
he got it on holiday there few months back
yeah you can buy burner super easily there
alright g
yeah its donny l
remember to tell him i sent you
peace
```
Swedish number.
Got the phone number. The flag is `rgbCTF{+46736727859}`
