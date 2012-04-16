# Revised Firmware for Sparkfun's USB Weather Board V3

The USB Weather Board from Sparkfun is so nice but, if you connect Weather Meters to it, you have some problems on it.  The most serious problem is that a rain gauge counts even if it is clear weather and there's no rain.
This modified firmware is for you who's facing such problems.

## Problems and stuff needed to improve

* Humidity shows as integer.  I want it to be float.
* A rain gauge value on CSV is integer.
* A rain gauge counts up if it's no rain.  It's so serious!
* USB Weather Board counts when a signal from a rain gauge changes HIGH to LOW and back to HIGH.  Sometimes it changes to LOW and keeps it.  This causes we lose one rain gauge count.

## Rain gauge miscount problem

### How rain gauge miscount

Long signal cables on Weather Meters catche much noise or radiation as you know.  USB Weather Board set this signal line HIGH and, if it falls to LOW, this fancy board counts as a rain gauge bucket tips.  Sometimes, maybe 10 through 20 times a day, noises around of it make this signal falling and it counts as it rains.  This is the reason that a rain gauge miscounts, I think.

When the bucket tips, the signal should move HIGH to LOW and LOW to HIGH.  Actually, however, it moves HIGH to LOW, HIGH to LOW, and LOW to HIGH.  At this time, this firmware doesn't count up and ignores second falling.  there's one more problem, more serious one, is that sometimes it moves HIGH to LOW and keeps LOW.  This falling keeps until next tipping and it means that one tipping that should be counted is ignored and the rainfall is less than actual.

### How to fix it

When the bucket tips, this signal falls to LOW and rises back to HIGH.  I measured this interval and it is 120 to 160 ms.  So I modified the interrupt when it falls and modified that it catches when when it changes each way, HIGH to LOW and LOW to HIGH.  With this change, we can measure the interval between changes.  If this interval is greater than 50000 micro seconds (= 0.05 sec).

Each measurements, this board has loop for interval.  This shiny new firmware checks wheather a rain gauge signal keeps LOW for a 3 secs or more and, if it is LOW, treats as one count.

## How to compile

You need [Arduino IDE](http://arduino.cc/hu/Main/Software) and the original v1.2 firmware zip archive from [Sparkfun's site](http://www.sparkfun.com/products/10586).  Copy libraries under Weather_Board_3_v12/libraries into Arduino IDE's libraries directory.  Ok, you are ready now.  Load the firmware source code and compile it.  To upload to the board, you need to set the appropriate serial port and the board type to "Arduino Pro or Pro Mini (3.3V, 8MHz) w/ ATmega328".