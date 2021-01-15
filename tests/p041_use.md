### Use Case for Boulder, Colorado, USA

Station name: p041 

Location: Boulder, CO, USA

Archive: UNAVCO, SOPAC 

DOI:  doi.org/10.7283/T5R49NQQ

Ellipsoidal Coordinates:

- Latitude: 39.94949

- Longitude: -105.19427

- Height: 1728.842 m

[Station Page at UNAVCO](https://www.unavco.org/instrumentation/networks/status/nota/overview/P041)

[Station Page at Nevada Geodetic Laboratory](http://geodesy.unr.edu/NGLStationPages/stations/P041.sta)

<img src="https://gnss-reflections.org/static/images/P041.jpg" width="500">

## Data Summary

The p041 antenna is ~2 meters tall. The site is relatively planar and free of obstructions. Since October 2018 the site has operated a Septentrio receiver. It has multi-GNSS signals in the default RINEX file and is operated by UNAVCO through the GAGE Facility as part of the Network of the Americas.

## Web App

You should use my web app to get a sense of what the site looks like. [Please note that the app will be analyzing data in real-time, so please wait for the answers to "pop" up in the left hand side of the page. It takes about 5 seconds](https://gnss-reflections.org/fancy6?example=lorg). The webapp provides you with a photograph, coordinates (make a note of them), and a google Earth map. Save the periodogram so you can look at it more closely.


## Setting azimuth and elevation mask

To get a sense of whether an azimuth or elevation mask is appropriate, check the [Reflection Zone Mapping in the web app](https://gnss-reflections.org/rzones?station=p041&lat=39.9495&lon=-105.1943&height=1728.842&msl=on&RH=2&eang=2&azim1=0&azim2=360).  As noted, this site is relatively flat with no major obstacles to interfere with reflected signals.

## Reproduce the Web App 

**Make SNR File**

First you need to make a SNR file. I will use the defaults, which only translates the GPS signals. If you have Fortran installed:

*rinex2snr p041 2020 132*

If you don't have Fortran installed:

*rinex2snr p041 2020 132 -fortran False*

**Take a Quick Look at the Data**

Lets look at the spectral characteristics of the SNR data for the default L1 settings:

*quickLook p041 2020 132*

<img src="p041-l1.png" width="500">

The four subplots show you different regions around the antenna. The x-axis tells you reflector height (RH) and the y-axis gives you the spectral amplitude of the SNR data. The multiple colors are used to depict different satellites that rise or set over that section (quadrant) of the field at P041. Which colors go to which satelliets is not super important. The goal of this exercise is to notice that the peaks of those periodograms are lining up around an x value of 2 meters. You also see some skinnier gray data - and those are failed periodograms. This means that the code doesn't believe the results are relevant. I did not originally plot failed periodograms, but people asked for them, and I do think it is useful to see that there is some quality control being used in this code.

I will also point out that these are the data from an excellent receiver, a Septentrio. Not all receivers produce L1 data that are as nice as these. Now try L2C:

*quickLook p041 2020 132 -fr 20*

<img src="p041-l2c.png" width="500">

One thing you can notice here is that there are more colors in the L1 plots than in the L2C plots. That is simply the result of the fact that there are more L1 satellites than L2C satellites.

Now try L5. These are FABULOUS satellites, but unfortunately there are not a lot of them:

*quickLook p041 2020 132 -fr 5*

You can try different things to test the code. For example, you can change the height restrictions:

*quickLook p041 2020 132 -h1 0.5 -h2 10*

If you want to look at Glonass and Galileo signals, you need to create SNR files using the -orb gnss flag.

*rinex2snr p041 2020 132 -orb gnss*

I believe Beidou signals are tracked at this site, but the data are not available in the RINEX 2 file.

quickLook is meant to be a visual assessment of the spectral characteristics. However, it does print out the answers to a file called rh.txt. If you want to assess changes in the reflection environment around a GPS/GNSS sites, i.e. look at multiple days, please look at the other use cases I have compiled.


### Analyze the Data

Begin by creating a json file to set up analysis parameters

*make_json_input -e1 5 -e2 25 p041 39.94949 -105.19427 1728.842*

Because the site is uniformly flat, you can leave the parameters at default settings.

Then run **rnx2snr** to obtain the SNR values:

*rinex2snr p041 2020 1 -doy_end 365*

Once the SNR values are available, run **gnssir** for 2020 to save the reflector heights for each day.


*gnssir p041 1 -doy_end 365*

Now try taking the daily average.  In this example, I will set the median filter to allow values within 0.25 meters of the median, and require 12 tracks to calculate the averages:

*daily_avg p041 .25 10*

**daily_avg** will first plot all the reflector heights available on all the days in the 2020/results/p041 director. 

<img src="p041-daily1.png" width="500">

The code will next plot the daily average.  The spikes here correspond to decreased deflector height following winter storms.  Reflector height is reduced by a few centimeters in spring and early summer, possibly because grass at the site is thickest and greenest at this time of year.

<img src="p041-daily2.png" width="500">