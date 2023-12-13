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

Exactly the same PC application is also used with the Lindy 38152 matrix and a bit of fortunate googling uncovered the a document (appended) which gave command hex strings codes for that matrix's RS232 interface, and most importantly how to generate checksum codes.
I created an excel spreadsheet (appened) which calculated the complete commands and checked them against values observed using Wireshark(TM).

The command hex string codes are A short period of monitoring the PC app using Wireshark coobservation of the I tried a number of Using Wireshark it is possible to 


```

lounge_to_sky:        echo -e "\xa5\x5b\x02\x03\x01\x00\x01\x00\x00\x00\x00\x00\xf9" | nc 192.168.2.50 8000
lounge_to_firestick:  echo -e "\xa5\x5b\x02\x03\x02\x00\x01\x00\x00\x00\x00\x00\xf8" | nc 192.168.2.50 8000
lounge_to_dvd:        echo -e "\xa5\x5b\x02\x03\x03\x00\x01\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
lounge_to_cctv:       echo -e "\xa5\x5b\x02\x03\x04\x00\x01\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000

kitchen_to_sky:       echo -e "\xa5\x5b\x02\x03\x01\x00\x02\x00\x00\x00\x00\x00\xf8" | nc 192.168.2.50 8000
kitchen_to_firestick: echo -e "\xa5\x5b\x02\x03\x02\x00\x02\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
kitchen_to_dvd:       echo -e "\xa5\x5b\x02\x03\x03\x00\x02\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
kitchen_to_cctv:      echo -e "\xa5\x5b\x02\x03\x04\x00\x02\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000

spare_to_sky:         echo -e "\xa5\x5b\x02\x03\x01\x00\x03\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
spare_to_firestick:   echo -e "\xa5\x5b\x02\x03\x02\x00\x03\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
spare_to_dvd:         echo -e "\xa5\x5b\x02\x03\x03\x00\x03\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000
spare_to_cctv:        echo -e "\xa5\x5b\x02\x03\x04\x00\x03\x00\x00\x00\x00\x00\xf4" | nc 192.168.2.50 8000

bed1_to_sky:          echo -e "\xa5\x5b\x02\x03\x01\x00\x04\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
bed1_to_firestick:    echo -e "\xa5\x5b\x02\x03\x02\x00\x04\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000
bed1_to_dvd:          echo -e "\xa5\x5b\x02\x03\x03\x00\x04\x00\x00\x00\x00\x00\xf4" | nc 192.168.2.50 8000
bed1_to_cctv:         echo -e "\xa5\x5b\x02\x03\x04\x00\x04\x00\x00\x00\x00\x00\xf3" | nc 192.168.2.50 8000

- switch:
    name: "Lindy HDMI Matrix"
    unique_id: lindyhdmimatrix
#on switch on
    command_on: echo -e  "\xa5\x5b\x08\x0b\x0f\x00\x00\x00\x00\x00\x00\x00\xde" | nc 192.168.2.50 8000
#on switch off
    command_off: echo -e  "\xa5\x5b\x08\x0b\x0f\x00\x00\x00\x00\x00\x00\x00\xde" | nc 192.168.2.50 8000
```
