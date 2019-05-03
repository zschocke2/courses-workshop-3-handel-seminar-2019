---
title: TITLE
description: 'Chapter description goes here.'
free_preview: true
---

## Introduction to EDF

```yaml
type: PureMultipleChoiceExercise
key: 6946f47d86
xp: 50
```

European Data Format (EDF) is a format for exchange and storage medical time series in a binary format. EDF was published in 1992 and stores multichannel data. It allows different sampling rates for each signal. It includes a global header, containing general information such as patient ID, start-time, date, number of signals etc. and signal headers with technical specifications (filtering, sampling, rate, calibration) for each signal. EDF+ is a newer version of EDF, which allows coding discontinous recordings as well as annotations, stimuli and events.
EDF and EDF+ are popular formats to record polysomnography and actimetry data, the kind of data which is used in this workshop. You find more information on this [website](https://www.edfplus.info/) 

The data we use is real data from the sleep-laboratory of the Charité Berlin, measured by an actrigraph (SOMNOwatch), a wrist worn device for monitoring activity, ECG and respiration in humans.

What is the advantage of using binary data sets?

`@hint`


`@possible_answers`
1. Everybody can read it!
2. There is no advantage!
3. [It saves storage and reduces access time!]

`@feedback`
1. Wrong, actually you need to know, how the data is stored to load it.
2. No. Try again!
3. Well done.

---

## Load EDF

```yaml
type: NormalExercise
key: 2bafef99a3
lang: r
xp: 100
skills: 1
```

In R there are already several packages to load the binary EDF files. Here we use the package [edf](https://github.com/bwrc/edf). 

 ```read.edf(FILENAME)``` reads the EDF file to a nested list. You can access header information of the EDF file with ```LOADEDFILENAME$header.global```. 

```LOADEDFILENAME$header.signal``` gives access to the specifications of the stored signals. 

And finally ```LOADEDFILENAME$signal$SIGNALNAME$data``` returns the signal values with a sampling rate of ```LOADEDFILENAME$header.signal$SIGNALNAME$samplingrate```. 

`@instructions`
1. Load the package ```edf``` which was already installed by ```install_github("bwrc/edf")```. You can load a package by ```library(PACKAGENAME)```.
2. Load the edf data from the file ```pat.edf``` and store it to ```PAT```.
3. Read the header information of PAT.
4. Store the ```Magnitude``` data of PAT in ```data_pat```
5. Store the sampling rate of ```Magnitude``` in ```sr_pat```

`@hint`
- Functions and variables without assignment will be assigned to the console and printed out.

`@pre_exercise_code`
```{r}
# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/3c221559fc1636bb231047193d1063e866f6856b/SL010_SL010_(1)_reduced.edf", destfile = "pat.edf")


```

`@sample_code`
```{r}
# Load needed packages
library(edf)

# Load edf-file 'pat.edf'
PAT <- 

# Read header information


# Store the data of "Magnitude" to data_pat
data_pat <- 

# Read the sampling rate of "Magnitude" and store it to sr_pat
sr_pat <- 
```

`@solution`
```{r}
# Load needed packages
library(edf)

# Load edf-file 'pat.edf'
PAT <- read.edf('pat.edf')

# Read header information
PAT$header.global

# Store the data of "Magnitude" to data_pat011
data_pat <- PAT$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat011
sr_pat <- PAT$header.signal$Magnitude$samplingrate
```

`@sct`
```{r}
msg = "Did your carfully read the exercise information?"
ex() %>% check_library("edf")

ex() %>% check_function("read.edf") %>% {
  check_arg(.,"filename") %>% check_equal()
} %>% check_equal()

ex() %>% check_object("PAT") %>% check_equal(incorrect_msg=msg)

ex() %>% check_object("data_pat") %>% check_equal(incorrect_msg=msg)

ex() %>% check_object("sr_pat") %>% check_equal(incorrect_msg=msg)

ex() %>% check_error()

success_msg("You managed to load your first EDF file. Great work!")
```

---

## Cut data

```yaml
type: NormalExercise
key: ea3e82c2e9
xp: 100
```

In the following we want to use only data from sleep. Thats why a common way is to use only the time between light off and light on. 

We know that subject PAT a light off time of 23:00 and a light on time of 7:00. But the dataset starts already at 22:14:10! 
The data ist still available under ```data_pat``` and the sampling rate at ```sr_pat``` (in Hz).

`@instructions`
Cut ```data_pat``` to the ligth off/on times! And store it to ```data_pat```.
1. Calculate the time difference from file start to light off in seconds.
2. Calculate the time difference from file start to light on in seconds.
3. Cut ```data_pat``` to the light off/on time.

`@hint`
- Did you use the sampling rate as well to calculate the indices?
- Calculation in indices have to be in brackets!

`@pre_exercise_code`
```{r}
# Load needed packages
library(edf)

# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/3c221559fc1636bb231047193d1063e866f6856b/SL010_SL010_(1)_reduced.edf", destfile = "pat.edf")

# Load edf-file 'pat.edf'
PAT <- read.edf('pat.edf')

# Store the data of "Magnitude" to data_pat
data_pat <- PAT$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat
sr_pat <- PAT$header.signal$Magnitude$samplingrate
```

`@sample_code`
```{r}
# Time difference between starttime and light off time in seconds
diff_light_off <- 

# Time difference between starttime and light on time in seconds
diff_light_on <- 

# Cut data_pat
data_pat <- 
```

`@solution`
```{r}
# Time difference between starttime and light off time in seconds
diff_light_off <- 2450

# Time difference between starttime and light on time in seconds
diff_light_on <- 29270

# Cut data_pat011
data_pat <- data_pat[(diff_light_off*sr_pat):(diff_light_on*sr_pat)]
```

`@sct`
```{r}
ex() %>% check_object("diff_light_off") %>% check_equal()
ex() %>% check_object("diff_light_on") %>% check_equal()
ex() %>% check_object("data_pat") %>% check_equal()
ex() %>% check_error()
success_msg("Now we have cutted our data to the right size and we can start the real time series analysis!")

```

---

## Calculation of Activity Counts

```yaml
type: NormalExercise
key: 79162182e1
xp: 100
```

Maybe you already wondered what kind of data we imported. The "Magnitude" is a pre-processed value from a 3 dimensional acceleration sensor and returns the absolute of the 3D accerlation signal, detrended from gravity. The signal is stored in units of the gravitational force g, (g = 9,81 m / s ^ 2). 

In most of the actigraphs the unit "activity-counts" is used as measure for activity, but mostly calculated in a black box. As we use raw data we need to calculate "activity-counts" first. 

For our calculations, we want to use a method used in early generation activity monitors, utilizing a threshold crossing technique. 
Activity with acceleration signal exceed the threshold (10 milli g) is counted, any acceleration below is not counted. 
The number of samples exceeding the threshold is summed over an epoch of 30 seconds and indicates the count value (maximum counts value = 30 s*128 Hz = 3840).

`@instructions`
2. Once we have imported all the data, we can calculate the "activity counts" using the For Loops option in R. The number of iterations (x) is calculated from the difference between the stop time and the start time, multiplied by 60 to obtain minutes and then with 2 to obtain 30 second intervals. We use 10 mg for the threshold.

3. Now we can display the "activity counts" to see what the data looks like. Use the function plot (x=, y=). In order to plot the "activity counts", we need to create a time for the x-axis. To create the time vector, use the command seq (from =, to =, by =).

`@hint`


`@pre_exercise_code`
```{r}
# Load needed packages
library(edf)

# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/3c221559fc1636bb231047193d1063e866f6856b/SL010_SL010_(1)_reduced.edf", destfile = "pat.edf")

# Load edf-file 'pat.edf'
PAT <- read.edf('pat.edf')

# Store the data of "Magnitude" to data_pat
data_pat <- PAT$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat
sr_pat <- PAT$header.signal$Magnitude$samplingrate

# Time difference between starttime and light off time in seconds
diff_light_off <- 2750

# Time difference between starttime and light on time in seconds
diff_light_on <- (28800 + diff_light_off)

# Cut data_pat011
data_pat <- data_pat[(diff_light_off*sr_pat):(diff_light_on*sr_pat)]
```

`@sample_code`
```{r}
# Calculate the numbers of 30 second intervals in data_pat011
numb_of_int <- as.integer(length(data_pat)/sr_pat/30)
length(data_pat)
numb_of_int

# loop over all intervals
n <- 0
counts <- integer(numb_of_int)
for (i in 1:numb_of_int){
  a <- data_pat[(n+1):(n+3840)]
  b <- a > 10
  counts[i]  <- sum(b)
  n <- n+3840
}
length(counts)
# create a time vector with the unit "time epochs (30 sec)"
time<- seq(1,length(counts))/2/60 # result

# Now we want to plot the counts as line-plot using plot()
plot(x=time, y=counts, xlab='time in hours', "l") # result

```

`@solution`
```{r}

```

`@sct`
```{r}

```

---

## Calculation of LIDS

```yaml
type: NormalExercise
key: 892a0ab6cb
xp: 100
```

Human sleep is definded not only by circadian rhythms (circa 24 hours), but also by ultradian sleep-cycles. These ultradian cycles, with durations of 90-110 min, are evident in EEG-Data (NONREM-REM-sleep), respiratory and cardiovascular physiology and so forth. 

Recent research exposed movement patterns in locomotor activity that directly reflect ultradian rhythms  as well as parameters measured in the sleep laboratory. 
In order to clarify the patterns and enhance the difference between movement and non-movement, the locomotor activity is transformed non-linearly into 
**"locomotor inactivity during sleep" (LIDS)**

This inversion from activity to inactivity results in values from 0 to 100, whereas LIDS=100 means complete inactivity. 
The function for calculating LIDS is ```LIDS=100/(counts+1)```
Now we calculate LIDS ("Locomotor Inactivity During Sleep") and plot again to visualize the sleep dynamic of the patient during the night. LIDS=100/(counts+1).

`@instructions`
1. First we need to calculate the LIDS, see given function. 

2. Than we want to plot the LIDS over time, but now using ggplot2, a system for declaratively creating graphics. In most cases you start the ggplot2 function with ggplot(data, aes(x,y, other astehtics)), supply a dataset and aesthetic mapping with aes(). Than you can add on layers (like geom_point(aes()), geom_line(aes()) or geom_histogram(aes())). The aesthetics may vary from one layer to another, and can be definded by aes(). 
To use ggplot2 the data needs to be in data.frame format. To bring the data in this format use the function data.fame(vector1, vector2)

https://ggplot2.tidyverse.org/reference/ggplot.html

`@hint`


`@pre_exercise_code`
```{r}
# load counts
counts <- scan('https://assets.datacamp.com/production/repositories/4958/datasets/1e17e1a5b67e26ad4c7e30794160f32bf2d0e671/counts.txt')

# create a time vector with the unit "time epochs (30 sec)"
time<- seq(1,length(counts))/2/60 # result

```

`@sample_code`
```{r}
# Calculate LIDS
LIDS <- (100/(counts+1))

plot(time,LIDS,"l")

```

`@solution`
```{r}

```

`@sct`
```{r}

```

---

## Insert exercise title here

```yaml
type: NormalExercise
key: e4068981d0
xp: 100
```



`@instructions`


`@hint`


`@pre_exercise_code`
```{r}

```

`@sample_code`
```{r}

```

`@solution`
```{r}

```

`@sct`
```{r}

```
