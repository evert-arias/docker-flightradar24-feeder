# docker-flightradar24-feeder

Feed the ADS/B data from a $10 RTLSDR dongle to http://flightradar24.com for a premium subscription

To sign up for the first time and acquire a flightradar24 key:
```
 $ docker run -ti marcelmaatkamp/flightradar24-feeder --signup
```

After that start the feeder with the key: 
```
 $ docker run -d \
   --name flightradar_feeder \
   --restart=always \
   --privileged \
   -p 8080:8080 \
   -p 8754:8754 \
   -p 30001:30001 \
   -p 30002:30002 \
   -p 30003:30003 \
   -p 30334:30334 \
   marcelmaatkamp/flightradar24-feeder --fr24key=<flightradar24 signup key>
```
You may encounter this issue :

```
Found 1 device(s):
0: Realtek, RTL2838UHIDIR, SN: 00000001 (currently selected)
Kernel driver is active, or device is claimed by second instance of librtlsdr.
In the first case, please either detach or blacklist the kernel module
(dvb_usb_rtl28xxu), or enable automatic detaching at compile time.
usb_claim_interface error -6
Error opening the RTLSDR device: Device or resource busy
```

This is due to a conflict with the dvb_usb_rtl28xxu module.
Unload the module :

```bash
sudo modprobe -r dvb_usb_rtl28xxu
```

And blacklist it to avoid any future problem by editing the blacklist configuration file :

```bash
sudo vi /etc/modprobe.d/rtl-sdr-blacklist.conf
```

And add the following lines :

```bash
blacklist dvb_usb_rtl28xxu
```

Then try again :

```bash
./dump1090 --interactive --net
```

You should see some planes



See a plot of your locally received airplanes on:
 http://dockerhost:8080

See a statistics page about the data send to flightradar24:
 http://dockerhost:8754

And when everything is ok, your subscription will change to premium for 7 days:
  https://www.flightradar24.com/premium/feed_stats.php

![dump1090 on :8080](https://sites.google.com/site/marcelmaatkamp/_/rsrc/1427902302448/home/share-your-ads-b-data-for-a-premium-subscription-of-http-www-flightradar24-com/Schermafbeelding%202015-04-01%20om%2017.27.59.png)  

![flightradar feed](https://04a27ff1-a-62cb3a1a-s-sites.googlegroups.com/site/marcelmaatkamp/home/share-your-ads-b-data-for-a-premium-subscription-of-http-www-flightradar24-com/Schermafbeelding%202015-04-01%20om%2016.45.57.png)
