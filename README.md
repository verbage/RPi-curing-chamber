# RPi-curing-chamber
7/29/2018--THIS IS A WORK IN PROGRESS, AND WILL BE COMPLETED OVER THE NEXT COUPLE OF WEEKS

This is a basic Python script for monitoring temperature and humidity, and controlling humidity, in a beverage cooler used for dry curing meats and sausages (i.e. charcuterie).  Data is logged to Google Sheets, which also holds a Dashboard where certain control parameters can be set and adjusted as needed.

This was inspired by user hjbct44's PorkPi system (https://github.com/hjbct44/PorkPi).  More details about the PorkPi system can be seen in two of hjbct44's postings at SmokingMeatForums.com (https://www.smokingmeatforums.com/threads/porkpi-latest-batch.252528/ and https://www.smokingmeatforums.com/threads/curing-chamber-raspberry-pi-humidity-fluctuation.240436/).

PorkPi is much more comprehensive than this system--it controls both temperature and humidity, and also includes load cells to actually weigh/track weight loss in the products that are being cured (I still track weight loss by hand!).  It also has a lot of built in resilience to take care of errors, unexpected power outs and restarts, etc.  Of course, this also makes it much larger (main script ~1000 lines long with several helper scripts, too).  Thus, it is a bit harder to grok without a dedicated effort since I did not write it myself.  So in the end, I ended up just starting from scratch, and came up with this simpler script of just ~100 lines.

Hardware-wise, this system consists of a:

1.  a Raspberry Pi 3 Model B

2.  a 4.4 cubic foot, glass-doored beverage cooler; this is basically just like a wine cooler with the cooling elements flush along the back, and many shelves instead of racks for wine bottles; I got this from Sam's Club, which puts it on sale every once in a while for US$150 (https://www.samsclub.com/sams/126-can-dsv-in-club-886159/prod18910248.ip)

3.  a Bosch BME280 temperature/humidity sensor (https://www.bosch-sensortec.com/bst/products/all_products/bme280); this can be easily purchased for <US$3 from China, for example, https://www.aliexpress.com/wholesale?SearchText=bme280&SortType=price_asc&isFreeShip=y (caution, some vendors misleadingly offer the BMP280 sensor, but the BMP280 only does pressure whereas the BME280 does pressure, temperature, and humidity; in sum, make sure you are getting a BME280)

4.  a mini, USB-powered humidifier that is also available from China for <US$3, for example, https://www.aliexpress.com/wholesale?SearchText=mini+usb+donut+humidifier&SortType=price_asc&isFreeShip=y

5.  a custom-built, 4-outlet gang box with relay controls so each outlet can be turned on/off by the RPi; the parts for this cost about $10-12 at Home Depot plus a few dollars for a 5V relay board

6.  two USB chargers--one to power the humidifier via the relay-controlled outlet mentioned above, and the other to power the RPi from an outlet that is wired to always on

Temperature control is provided by the beverage cooler itself since it can be set to the ideal dry curing temperature of about ~13°C (~55°F).  Most fridges do not have set points this high/warm...  So if I were using a regular mini-fridge that could not be set to this higher temperature, I would control it with a relay to turn it on/off, and maintain the the necessary higher-than-normal fridge temperature.

To read data from the BME280 sensor, I use Matt Hawkins' BME280 Python script (https://www.raspberrypi-spy.co.uk/2016/07/using-bme280-i2c-temperature-pressure-sensor-in-python).

Connectivity to Google Sheets is provided by burnash's gspread Google Spreadsheets Python API (https://github.com/burnash/gspread) using a service account with OAuth credentials.  To deal with the frustratingly inconsistent expiration of temporary auth tokens, I use brute force and simply log in frequently.  Eventually, I will make this much more elegant by programatically checking for token expiration status instead of just brute forcing logins...

Using Google Sheets is slick because it can do essentially all of the heavy lifting regarding calculations, data manipulation, plotting graphs for the dashboard, and similar.  And even better, since most folks already have a free Google account, it does not require a monthly subscription fee for an IoT middleman service like ThingSpeak, Cayenne, Adafruit's IO platform, Thinger IO, etc.  Finally, since gspread cannot only push data to Google Sheets, but also pull it, too, this actually lets one control the RPi system from anywhere in the world in a secure manner.  Specifically, since this RPi system sits on my own personal WLAN, I would have to either set up a VPN or punch a hole through the firewall to talk to it if I am not at home.  But since gspread can also pull data from Google Sheets, which I can easily access from almost any device, I can simply set my system control parameters there.  The RPi system then grabs the control parameters from Google Sheets letting me easily control it from wherever I may be.
