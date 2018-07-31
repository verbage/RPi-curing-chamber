# RPi-curing-chamber
7/29/2018--THIS IS A WORK IN PROGRESS, AND WILL BE COMPLETED OVER THE NEXT COUPLE OF WEEKS

This is a basic Python script for monitoring temperature and humidity, and controlling humidity, in a beverage cooler used for dry curing meats and sausages (i.e. charcuterie).  Data is logged to Google Sheets, which also holds a Dashboard where certain control parameters can be set and adjusted as needed.

This was inspired by user hjbct44's PorkPi system (https://github.com/hjbct44/PorkPi).  More details about the PorkPi system can be seen in two of hjbct44's postings at SmokingMeatForums.com (https://www.smokingmeatforums.com/threads/porkpi-latest-batch.252528/ and https://www.smokingmeatforums.com/threads/curing-chamber-raspberry-pi-humidity-fluctuation.240436/).

PorkPi is much more comprehensive than this system--it controls both temperature and humidity, and also includes load cells to actually weigh/track weight loss in the products that are being cured.  It also has a lot of built in resilience to take care of errors, unexpected power outs and restarts, etc.  Of course, this also makes it much larger (main script ~1000 lines) and a bit harder to grok without dedicated effort since I didn't write it myself.  So I ended up just starting from scratch, and came up with this simpler script of ~100 lines.

Hardware-wise, this system consists of a:

1.  a Raspberry Pi 3 Model B

2.  a 4.4 cubic foot, glass-doored beverage cooler; this is basically just like a wine cooler with the cooling elements flush along the back, and many shelves instead of racks for wine bottles; I got this from Sam's Club, which puts it on sale ever once in a while for US$150 (https://www.samsclub.com/sams/126-can-dsv-in-club-886159/prod18910248.ip)

3.  a Bosch BME280 temperature/humidity sensor (https://www.bosch-sensortec.com/bst/products/all_products/bme280); this can be easily purchased for <US$3 from China, for example, https://www.aliexpress.com/wholesale?SearchText=bme280&SortType=price_asc&isFreeShip=y (caution, some vendors misleadingly offer the BMP280 sensor, but the BMP280 only does pressure whereas the BME280 does pressure, temperature, and humidity; in sum, make sure you are getting a BME280)

4.  a mini, USB-powered humidifier that is also available from China for <US$3, for example, https://www.aliexpress.com/wholesale?SearchText=mini+usb+donut+humidifier&SortType=price_asc&isFreeShip=y

5.  a custom-built, 4-outlet gang box with relay controls so each outlet can be turned on/off by the RPi; the parts for this cost about $10-12 at Home Depot

6.  two USB chargers--one to power the humidifier via the relay-controlled outlet mentioned above, and the other to power the RPi from an outlet that is wired to always on

Temperature control is provided by the beverage cooler itself.  Unlike a regular mini-fridge, which will almost always be too cool for dry curing even at the warmest setting, this particular beverage cooler can maintain warmer temperatures like up to 55° or 60°F, which let's me dial in an ideal dry curing temp of ~13°C (~55°F) easily.  If I were doing this with a regular fridge that could not reach higher temperatures, I would also control that directly with a relay to allow for higher temperatures.

Connectivity to Google Sheets is provided by burnash's gspread Google Spreadsheets Python API (https://github.com/burnash/gspread) using a service account with OAuth credentials.  After the service account first logs it, it is theoretically assigned an auth token that should last for one hour before another login is needed.  However, this time-limited auth token is frustratingly inconsistent in my own experience.  Sometimes it lasts for a full hour as expected, but sometimes it lasts for several hours, too.  And on the short end, sometimes it can expire after just 10-20 minutes despite the supposed one hour limit.  I get around this potential error by brute force--I simply log in frequently.
