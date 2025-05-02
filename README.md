# Peter's LabView Community Edition Arduino Projects

I've started playing with the LabView CE, and combining with Arduino ([See "Getting Started"](https://learn.ni.com/learn/article/getting-started-with-arduino-and-labview-community-edition "Getting Started")).

I like using version control for any software, and since LabView CE requires non-commercial, it's fine if my repo is public.  So here you go.

I make no guarantees or warranties about any VI found in this repo.

## Gotchas with LabViewCE + Arduino

I had some gotchas when initially getting started with LabView CE and Arduino.  The following notes may help you with the same problems.

### "MakerHub > LINX" now called "Hobbyist" menu

With LabView 2025, NI they stopped putting "LINX" in the menus (though their examples still have the LINX name).  Instead of being **Tools > MakerHub > LINX > LINX Firmware Wizard**, it's the simplified **Tools > Hobbyist > Firmware Wizard**.

### Arduino SW Required

Also, the Getting Started didn't mention this, but you have to install the [Arduino software](https://arduino.cc) first, so that it installs the drivers and the compiler, which LabView/LINX still needs to access.

### Error 5003 when running example VI

If you always get error 5003 when trying to run a LabView VI which talks with Arduino (for example, doing the `LINX - Blink (Simple).vi`), and you know you have a setup that can talk with the Arduino (for example, the Arduino Blink is working just fine)

[This topic](https://forums.ni.com/t5/Hobbyist-Toolkit/Linx-for-Labview-2020-Error-5003/m-p/4075976 "LINX...Error 5003"), and associated replies, especially [this early reply by DDuff](https://forums.ni.com/t5/Hobbyist-Toolkit/Linx-for-Labview-2020-Error-5003/m-p/4075976 "DDuff 2020-08-18") [this post by comporder1](https://forums.ni.com/t5/Hobbyist-Toolkit/Linx-for-Labview-2020-Error-5003/m-p/4177138#M921 "comporder1 2021-09-08") explain that there's a problem with the Packet Number vi-block accessed from the Initialize Device VI.

To fix this problem, you can download the VI that comporder1 attached and put it where he showed (or your equivalent directory). To manually make the edit yourself, open the `LINX - Blink (Simple).vi`, and double click into the blocks in the order **Open Serial > Initialize > Initialize Device > Packet Number**. Once you have `Packet Number.vi` open, in the **switch** structure, select the `Initialize` case, and add a **msWait** block with a constant input of 2000.

Explanation: This fix adds a 2 second (2000ms) delay when it's trying to initialize the device connection. The value you need may vary: some reported needing 2 seconds; others only 430ms; it may depend on your computer and your Arduino. If you follow DDuff's original screenshot, and add the delay _outside_ the **switch** structure, then there are many calls to `Packet Number.vi`, which would all take 2s (which makes the example VI work, but run abominably slowly); if you follow comporder1's instructions (or my interpretation found here), it will only use the delay during the initialization, which should only be called once in your VI, so the extra delay would only be there at startup.
