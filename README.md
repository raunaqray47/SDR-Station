# SDR-Station
A RaspberryPi based SDR Station to capture publicly available signals

The setup is designed to receive and decode raw ADS-B messages transmitted at 1090MHz. 

Hardware:     
    
    Raspberry Pi 3B+ running a basic copy of the Raspbian Operating System
    RTL-SDR USB Receiver
        Operating Range: 24 to 1766 MHz
        Max Sampling Rate: 2.8 MSPS
        Only capable of listening in the frequency ranges 
    1090MHz dipole antenna

Software: 
    
    dump1090 for ADS-B decoding
    pyModeS for message parsing

![image](https://github.com/user-attachments/assets/93f40b18-bc96-433a-93d4-2ac3522d4ae2)


Mode S uses L band signals that follow the line-of-sight propagation, any obstructions between the transmitter and receiver can cause a significant number of signals to be blocked. If no obstacle exists between the aircraft and the receiver, and the transmitter has enough power, the maximum range of the receiver is determined by the curvature of the earth.
The maximum distance (d) of a Mode S receiver can be obtained by knowing the altitude of the receiver antenna: 
  
    d=(α_r+α_t )R
    d=(arccos⁡〖R/(R+h_r )〗+arccos⁡〖R/(R+h_t )〗 )  R

where R is the radius of the earth, while h_t and h_r are the height of the aircraft and receiver above the sea level. However, in real-life applications, Mode S signal follows the Friis transmission model, which states that the maximum distance also depends on the power of the transmitter, as well as the directivities of the transmission and receiving antennas. 

![image](https://github.com/user-attachments/assets/b094f1d8-0b9c-4775-83e8-7e0fa602b55a)

In principle, any antenna designed for the radio frequency around 1 GHz can be used for receiving Mode S signals. The carrier frequency of Mode S is 1090 MHz, which corresponds to the wavelength of 27.5 centimetres. A monopole OR A DIPOLE antenna can  be used for this purpose. The monopole antenna and the dipole antenna both are half-wavelength (λ/2) antennas with a total conductor length of 13.75 cm.

![image](https://github.com/user-attachments/assets/9d737b88-f780-43bb-84fc-cb1278a8034e)

The Raspberry Pi runs a basic copy of the Raspbian Operating System.

dump1090 is one of the most frequently used open-source Mode S decoder. It also offers embedded HTTP server that displays the currently detected aircrafts on Google Map, along with TCP server streaming and receiving raw data to/from connected clients (using --net). 

1.	The directory was cloned (downloaded) to a local source as shown:

        $ git clone https://github.com/antirez/dump1090.git
2.	Installing the dependencies: 

        sudo apt-get install build-essential debhelper librtlsdr-dev pkg-config dh-systemd libncurses5-dev libbladerf-dev
3.	Compile the directory

        $ cd dump1090
        $ make

The successful compilation marks the complete installation of dump1090. Once it is compiled and the RTL-SDR receiver is connected, the following command starts receiving and decoding the signals:

        $ ./dump1090



With the default option, Mode S messages and decoded information are displayed in the terminal. 
 
     1. *8d451dbd9905b5018004005979c5;
     2. CRC: 000000
     3. RSSI: -20.6 dBFS
     4. Score: 1800
     5. Time: 9214131.08us
     6. DF:17 AA:451DBD CA:5 ME:9905B501800400
     7. Extended Squitter Airborne velocity over ground, subsonic (19/1)
     8. ICAO Address:	451DBD (Mode~S / ADS-B)
    10. Air/Ground:	airborne
    12. Ground track	271.4
    14. Groundspeed:	436.1 kt
    16. Geom rate: 	0 ft/min
    18. NACv: 	0

To view Raw Mode S messages, the –raw option can be used
    
    $ ./dump1090 –raw

The terminal output will only have the raw ADS-B messages. 

    *5d4074358ad00c;
    *8d407435990dbd01900484f66c3c;
    *8d40743558af828cd326fe0c2fe9;
    *a80011b1e0da112fe0140060939f;
    *a00015b8c2680030a80000318667;
    *5d4074358ad030;
    *5d4074358ad030;

dump1090 can also provide a live view of all aircraft seen by the receiver using the –interactive option:

    $ ./dump1090 –interactive

![image](https://github.com/user-attachments/assets/5b317643-73ef-4203-b2be-4635aa2f076f)

![image](https://github.com/user-attachments/assets/1b54257b-ea9d-49dd-93a7-369c5909964c)

PyModeS is an open-source Python library developed by Junzi Sun and contributors from TU Delft's Aerospace Engineering Faculty for decoding Mode-S and ADS-B signals.

The stable version of pyModeS can be installed as: 

    pip install --upgrade pyModeS

For this project, the RTL-SDR was used to fetch traffic data from dump1090. The dump1090 data  source was then supplied through a TCP output stream using these commands – 

    $ dump1090 --net --quiet
    $ modeslive --source net --connect localhost 30002 raw

