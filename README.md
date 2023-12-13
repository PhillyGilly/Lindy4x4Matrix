# Lindy4x4Matrix
This project is about IP control of a Lindy 38143 4*4 HDMI matrix.
![image](https://github.com/PhillyGilly/Lindy4x4Matrix/assets/56273663/707fd973-bb74-47e3-8e80-2244d8dc810b)

The matrix enables any HDMI input (numbered 1 to 4) to be connected simultanesouly to any HDMI-over-Cat6 output (identifioed as A to D).
If for example you want to watch the Amazon Firestick in the lounge you connect 2 to A, or if you want to watch Sky+ in the kitchen you connect 1 to B.
Users have to memorize the codes for various inputs and outputs which makes the matrix completely unfriendly and not usable by people that just want to watch TV.
Further the matrix consumes power by being switched on continuously.
So the requirement is to be able to manage the matrix using a table interface (i.e. Home Assistant) or by Voice Assistant (i.e. Alexa)
I had previously tried and failed to do this in 2019 but inspired by @CameronGray's excellent video https://youtu.be/BE6ZJmede5Q?si=LtarL4RUJyJyKgxZ, I decided to revisit this project in 2023.

Lindy provide a fairly basic PC application to remotely control the matrix.
![image](https://github.com/PhillyGilly/Lindy4x4Matrix/assets/56273663/2fc655cf-bc2b-44de-bef6-8c0bbdb5a8a5)

I tried a number of Using Wireshark it is possible to 
