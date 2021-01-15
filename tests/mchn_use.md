### Use Case for Michipicoten Harbor, Ontario, Canada

**Station Name:** 	mchn

**Location:** Michipicoten Harbor, Ontario, Canada

**Archive:**  SOPAC, NRCAN

**DOI:** N/A

**Ellipsoidal Coordinates:**

- Latitude: 47.961

- Longitude: -84.901

- Height: 152.019 m

[Station Page at Natural Resources Canada](https://webapp.geod.nrcan.gc.ca/geod/data-donnees/station/report-rapport.php?id=M093001)

[Station Page at Nevada Geodetic Laboratory](http://geodesy.unr.edu/NGLStationPages/stations/MCHN.sta)

<img src="mchn_monu-cors.png" width="500"/>


### Data Summary

Station mchn is operated by NRCAN. This site only tracks GPS.

Unfortunately only L1 data should be used at this site. Encourage the station operators to 
track L2C, L5 (and Galileo, Glonass, and Beidou!)


### Web App

You should use my web app to get a sense of what the site looks like. [Please note that the app 
will be analyzing data in real-time, so please wait for the answers to "pop" up in the 
left hand side of the page. It takes about 5 seconds](https://gnss-reflections.org/fancy6?example=mchn)
The webapp provides you with a photograph, coordinates (make a note of them), and 
a google Earth map. Save the periodogram so you can look at it more closely.

### Setting Azimuth and Elevation Mask

From the periodogram and google Earth map you should be able to come up with a pretty good 
azimuth mask.  Elevation angle might be a bit trickier, but in this case, go ahead and 
use what I did, which is in the title of the periodogram plot. For azimuth, I suggest that you 
use my web app. [Here is a pretty good start on an elevation and azimuth angle mask](https://gnss-reflections.org/rzones?station=mchn&msl=on&RH=7&eang=2&azim1=80&azim2=180). 

### Reproduce the Web App Results

**Make SNR File** 

I will use the defaults, which only translates the GPS signals. If you have Fortran installed:

*rinex2snr mchn 2019 205 -archive sopac*

If you don't have Fortran installed:

*rinex2snr mchn 2019 205 -archive sopac -fortran False*

**Quick Look at Data**

Let's look at the spectral characteristics of the SNR data for the default L1 settings:

*quickLook mchn 2019 205*

<img src="mchn-default.png" width="500">

The four subplots show you different regions around the antenna. The x-axis tells you reflector height (RH) and the y-axis gives you the spectral amplitude of the SNR data. The multiple colors are used to depict different satellites that rise or set over that section (quadrant) of the field at MCHN. Which colors go to which satellites is not super important. You also see some skinnier gray data - and those are failed periodograms. This means that the code doesn't believe the results are relevant. I did not originally plot failed periodograms, but people asked for them, and I do think it is useful to see that there is some quality control being used in this code.

Why does this not look like the results from my web app? Look closely. Make some changes at the command line for quickLook.

*quickLook mchn 2019 205 -h1 2 -h2 8*

<img src="mchn-better.png" width="500">

### Analyze the Data

Once you figure out what you need to do, go ahead and analyze the data from 2013.

*rinex2snr mchn 2013 1 -archive sopac -doy_end 365*

You need to use **make_json_input** to set up the analysis instructions.

*make_json_input -e1 5 -e2 25 mchn 47.961 -84.901 152.019*


[You will need to hand-edit it to only use L1 and to set the azimuth region.](mchn.json)
You will notice that I have a pretty restricted azimuth region.  Although you can get good reflections beyond 180 degrees, there is clearly something funny in the water there (from google Earth), and if you look at the photograph, it is pretty obvious that there is something sticking out of the water. Of course feel free to try something different. But if you choose a mask that reflects off water and something else, you aren't really measuring the height of the water.

Now that the analysis parameters are set, we can run **gnssir** to save the reflector height (RH) output.

*gnssir mchn 2013 1 -doy_end 365*

There are still outliers in your solutions - and in principle I encourage you to figure out better restrictions, i.e. increase the amplitude requirement or peak to noise restriction. If you don't take this into account, you can see what I mean when you compute the daily average:

*daily_avg mchn 2 10*

This command says the median filter allows any value within 2 meters of the median. The input of 10 means the 
number of tracks needed to compute an average.  

<img src="mchn_1.png" width="500">

You get something much more reasonable with a 0.25 meter median filter.

*daily_avg mchn 0.25 10*

<img src="mchn_2.png" width="500">

and for the average

<img src="mchn_3.png" width="500">

The number of tracks you will require is going to depend on the site. Here the azimuth is restricted because we are on a coastline of a lake. On an ice sheet we can often use every azimuth, which means more tracks. And some of those sites also tracked multiple frequencies. Here we can only reliably use L1.
Please note that these reflections are from ice in the winter and water during the summer. We will be implementing surface bias corrections (ice,snow) to our software. Until then, please take this into account when interpreting the results.


Note: there is a tide gauge at this site. Please [contact NRCAN](https://contact-contactez.nrcan-rncan.gc.ca/index.cfm?st=-1&cat=-1&scat=-1&lang=eng) for more information.
