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

The European Data Format (EDF) is a format for the exchange and storage of medical time series in a binary format. EDF was published in 1992 and stores multichannel data, allowing different sampling rates for each signal. It includes a global header, containing general information such as patient ID, start-time, date, number of signals etc., and signal headers with technical specifications (filtering, sampling rate, calibration) for each signal. EDF+ is a newer version of EDF, which also allows coding discontinuous recordings as well as annotations, stimuli and events.
EDF and EDF+ are popular formats for recording polysomnography and actimetry data, the kinds of data which are used in this workshop. You can find more information on this [website](https://www.edfplus.info/).

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
1. Load the package ```edf``` which has already been installed on the system by ```install_github("bwrc/edf")```. You can load a package by ```library(PACKAGENAME)```.
2. Load the edf data from the file ```pat.edf``` and store it to ```PAT```.
3. Read the header information of PAT.
4. Store the ```Magnitude``` data of PAT in ```data_pat```.
5. Store the sampling rate of ```Magnitude``` in ```sr_pat```.

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
library(___)

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
msg = "Did your carefully read the exercise information?"
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

In the following we want to use only data recorded while the subject is resting in bed. A common way to assure this is to use only the time between "light-off" and "light-on" markers, that are carefully written down in a sleep lab. 

We know that light-off time for subject PAT is 23:00 and light-on time is 7:00. But the dataset starts already at 22:14:10, followed by cable testing etc.

The data is still available as ```data_pat``` and the sampling rate (in Hz) as ```sr_pat```.

`@instructions`
Crop ```data_pat``` to the light off/on times and store the result to ```data_pat``` again:
1. Calculate the time difference from file start to light off in seconds.
2. Calculate the time difference from file start to light on in seconds.
3. Crop ```data_pat``` to the light off/on times.

`@hint`
- Mind the sampling rate ```sr_pat``` -- there is not just one measurement per second.
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
diff_light_off <- 45*60+50

# Time difference between starttime and light on time in seconds
diff_light_on <- diff_light_off + 8*3600

# Cut data_pat011
data_pat <- data_pat[(diff_light_off*sr_pat):(diff_light_on*sr_pat)]
```

`@sct`
```{r}
ex() %>% check_object("diff_light_off") %>% check_equal()
ex() %>% check_object("diff_light_on") %>% check_equal()
ex() %>% check_object("data_pat") %>% check_equal()
ex() %>% check_error()
success_msg("Now we have cropped our data to the right size and we can start the real time series analysis!")

```

---

## Calculation of Activity Counts

```yaml
type: NormalExercise
key: 79162182e1
xp: 100
```

Maybe you already wondered what kind of data we imported. The "Magnitude" is a pre-processed value from a three-dimensional acceleration sensor and returns the absolute of the 3D acceleration signal, detrended from gravity. The signal is stored in units of the gravitational force g, (g = 9.81 m/s$^2$). 

In most earlier actigraph models the unit "activity counts" is used as measure for activity, since earlier technology did not allow the measurement and storage of actual acceleration values at sampling rates of several Hz. Sadly, the exact algorithms for calculating "activity counts" in earlier models are still treated as proprietary material and not available to the scientific community ("black box calculations"). Nevertheless, we have to calculate some kind of "activity-counts" from our raw data to facilitate a comparison with earlier published results.

For our calculations, we want to use a method used in early generation activity monitors, utilizing a threshold crossing technique. We count how often the acceleration signal exceeds a threshold (10 mg -- milli g -- in our case), any acceleration below this threshold is not counted. The number of samples exceeding the threshold is summed over an epoch of 30 seconds and indicates the count value. Note that the maximum count value for each segment is 30 s x 128 Hz = 3840.

`@instructions`
```data_pat``` (in mg) and ```sr_pat``` are still available.
1. Calculate the numbers of 30-second intervals in ```data_pat``` and store the result to ```numb_of_intervals```. ```as.integer()``` converts the number to an integer.
2. Now we need to iterate over these 30 second intervals. Count, for each segment, the number of values that exceed 10 mg.  For a vector ```a```, the expression ```a>10``` is a vector of boolean (0 or 1) values, indicating when the condition is fulfilled.
3. Create a time series ```time``` for the time of each 30-seconds segment in hours.
4. Plot the created data.

`@hint`
1. You need length of data, sampling rate and 30 seconds.
2. In our solution, we create an integer vector with ```counts <- integer(numb_of_intervals)``` and iterate over ```1:numb_of_intervals```. In each iteration step we assign data from 30 seconds segments to ```a```. Then we use ```sum(a>10)``` to get the number of values bigger then 10 in a.
3. You remember ```seq(from=,to=,by=)```?

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
# Calculate the numbers of 30 second intervals in data_pat 
numb_of_intervals <- as.integer(___)

# loop over all intervals and store the counts in counts


# create a time vector for the 30 seconds segments in hours
time <- 

# Now we want to plot the counts as line-plot using plot()


```

`@solution`
```{r}
# Calculate the numbers of 30 second intervals in data_pat
numb_of_intervals <- as.integer(length(data_pat)/sr_pat/30)

# loop over all intervals (replace ___)
n <- 0
counts <- integer(numb_of_intervals)
for (i in 1:numb_of_intervals){
  a <- data_pat[(n+1):(n+3840)] # load 30-s-interval to a
  b <- a > 10 # creats boalean array with TRUE = (>10)
  counts[i]  <- sum(b) # TRUE
  n <- n+3840
}

# create a time vector for the 30 seconds segments in hours
time<- seq(1,length(counts))/2/60 # result

# Now we want to plot the counts as line-plot using plot()
plot(x=time, y=counts, xlab='time in hours', "l") # result

```

`@sct`
```{r}
ex() %>% check_object("numb_of_intervals")
ex() %>% check_object("counts")
ex() %>% check_object("time")
ex() %>% check_function("plot") %>% {
  check_arg(.,"x") %>% check_equal()
  check_arg(.,"y") %>% check_equal()
  check_arg(.,"type") %>% check_equal()
} %>% check_equal()

ex() %>% check_error
success_msg("Well done.")


```

---

## Calculation of LIDS

```yaml
type: NormalExercise
key: 892a0ab6cb
xp: 100
```

Human sleep is defined not only by circadian rhythms (circa 24 hours), but also by ultradian sleep-cycles. These ultradian cycles, with durations of 90-110 minutes, are evident in EEG-Data (non-REM / REM sleep), respiratory and cardiovascular physiology and so forth. 

Recent research exposed movement patterns in locomotor activity that directly reflect ultradian rhythms as well as parameters measured in the sleep laboratory as presented in the talk of Dr. Eva Winnebeck. In order to clarify the patterns and enhance the difference between movement and non-movement, the locomotor activity is transformed non-linearly into **"Locomotor Inactivity During Sleep" (LIDS)**.

This inversion from activity to inactivity results in values from 0 to 100, whereas LIDS=100 means complete inactivity.  The rule for calculating LIDS is ```LIDS=100/(counts+1)```. Now we calculate LIDS and plot again to visualize the sleep dynamic of the patient during the night. 

`@instructions`
1. First we need to calculate the LIDS, see given function. ```counts``` is still available.
2. Let's plot LIDS. ```time``` is still available.

`@hint`


`@pre_exercise_code`
```{r}
# load counts
counts <- scan('https://assets.datacamp.com/production/repositories/4958/datasets/1e17e1a5b67e26ad4c7e30794160f32bf2d0e671/counts.txt')

# create a time vector
time<- seq(1,length(counts))/2/60 # result

```

`@sample_code`
```{r}
# Calculate LIDS
LIDS <- 

# Plot LIDS as line plot

```

`@solution`
```{r}
# Calculate LIDS
LIDS <- (100/(counts+1))

# Plot LIDS as line plot
plot(time,LIDS,"l")
```

`@sct`
```{r}
ex() %>% check_object("LIDS")
ex() %>% check_function("plot") %>% {
  check_arg(.,"x") %>% check_equal()
  check_arg(.,"y") %>% check_equal()
}
```

---

## Moving average

```yaml
type: NormalExercise
key: e4068981d0
xp: 100
```

As we have seen in the previous task, there is strong noise in the data. To smooth it, we use a moving average filter. Moving averages are used to smooth data by calculating local means. As neighboring observations of a time series are likely to be similar in value, averaging eliminates some of the randomness in the data, leaving a smooth trend-cycle component. 

The elements of the centered simple moving average of the order $s$ are given by $\bar x_i = (1/s) \sum_{j=-k}^k x_{i+j}$, and $s=2k+1$, where $k$ is the one-side time-frame of a centered fixed subset of length $s$.
In our example we want to use a bin width of 30 minutes, that means that the one-side time-frame is 15 minutes, or rather 30, because we work with 30-second intervals, so that $k=30$.

https://www.rdocumentation.org/packages/forecast/versions/8.6/topics/ma
https://r4ds.had.co.nz/index.html

`@instructions`
```LIDS``` and ```time``` are still available.

Instead of programming a moving average yourself, you can use the function [```movavg(data=, window_length=,type=)```](https://www.rdocumentation.org/packages/pracma/versions/1.9.9/topics/movavg) from the package ```pracma```. 

1. Load the package ```pracma```
2. Calculate the moving average. As ```window_length``` use 30 minutes and use  ```type="s"```.
3. Plot the raw LIDS (in blue) and add the moving average (in red). You can add colors with the argument ```col=c("black")```. To add a graph to a plot, you can use ```lines(x,y,...)```.

`@hint`
- You can load a package by ```libraray(PACKAGENAME)```.

`@pre_exercise_code`
```{r}
# load counts
counts <- scan('https://assets.datacamp.com/production/repositories/4958/datasets/1e17e1a5b67e26ad4c7e30794160f32bf2d0e671/counts.txt')

# create a time vector
time<- seq(1,length(counts))/2/60 # result

# Calculate LIDS
LIDS <- (100/(counts+1))
```

`@sample_code`
```{r}
# Load the package "pracma"

# Calculate the moving average of LIDS, use a 30 minute window
LIDS_ma <- ___

# Plot LIDS and his moving average
plot(___)
lines(___)
```

`@solution`
```{r}
# Load the package "pracma"
library(pracma)

# Calculate the moving average of LIDS, use a 30.5-minute window (15 minutes into both directions)
LIDS_ma <- movavg(LIDS,61,"s")

# Plot LIDS and its moving average
plot(time,LIDS,col=c("blue"))
lines(time,LIDS_ma,col=c("red"))
```

`@sct`
```{r}
ex() %>% check_library("pracma")
ex() %>% check_function("movavg") %>% {
  check_arg(.,"x") %>% check_equal
  check_arg(.,"n") %>% check_equal
  check_arg(.,"type") %>% check_equal
} 

ex() %>% check_function("plot") %>% {
  check_arg(.,"x") %>% check_equal
  check_arg(.,"y") %>% check_equal
  check_arg(.,"col") %>% check_equal
}

ex() %>% check_function("lines") %>% {
  check_arg(.,"x") %>% check_equal
  check_arg(.,"y") %>% check_equal
  check_arg(.,"col") %>% check_equal
}

```

---

## LIDS and sleep stages

```yaml
type: NormalExercise
key: 6ca3da9d97
xp: 100
```

Sleep stages calculated across the night are often presented as a hypnograms, which describe the order and duration of each sleep stage. To see how LIDS and sleep stages correlate, we want to plot the LIDS and the hypnogram of the same patient and night in one graphics.

`@instructions`
1. Import the sleep stage data from "sleep.txt". It is stored in 30 second-epochs as well. The coding is this: 0="wake", 1="REM", 2="N1", 3="N2", 4="N3"

2. Now plot the moving average of LIDS and the sleep stages in one graph. As LIDS has a range of 0 to 100 and sleep stages from 0 to 4, we need to rescale one of them. Rescale the sleep stages to values from 10 to 90. Furthermore, complete the ```axis(side=,at=,labels=)```. Use ```side=4```. The parameter ```at``` defines tick-positions and ```labels``` defines the tick-labels. Both expected a vector, e.g. ```c(1,2,3)``` or ```c("A","B")```.

`@hint`
- Use ```scan(PATH)``` to load data.
- Use ```lines(x=,y=,...)``` to add graph.

`@pre_exercise_code`
```{r}
# Load the package "pracma"
library(pracma)

# load counts
counts <- scan('https://assets.datacamp.com/production/repositories/4958/datasets/1e17e1a5b67e26ad4c7e30794160f32bf2d0e671/counts.txt')

# load sleep stages
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/69d8d0dddd6064fc0293c50eb823688fe1244c3b/sleep_stage.txt", destfile = "sleep.txt")

# create a time vector with the unit "time epochs (30 sec)"
time<- seq(1,length(counts))/2/60 # result

# Calculate LIDS
LIDS <- (100/(counts+1))

# Calculate the moving average of LIDS, use a 30 minute window
LIDS_ma <- movavg(LIDS,60,"s")
```

`@sample_code`
```{r}
# Load sleep stages from 
sleep <- ___

# Plot sleep stages and LIDS

# Add second y-axis with labels for the sleep stages
axis(4, at=c(___), labels=c(___))
```

`@solution`
```{r}
# Load sleep stages from 
sleep <- scan("sleep.txt")

# Plot sleep stages and LIDS
plot(time,LIDS_ma,col=c("red"))
lines(time,sleep*20+10,col=c("darkgreen"))

# Add second y-axis (replace ___)
axis(4, at=c(10,30,50,70,90), labels=c("W","REM","N1","N2","N3"))
```

`@sct`
```{r}
ex() %>% check_function("scan") %>% check_arg("file") %>% check_equal
ex() %>% check_object("sleep") %>% check_equal()

ex() %>% check_function("plot") 
ex() %>% check_function("lines")
ex() %>% check_function("axis") %>% {
  check_arg(.,"at")
  check_arg(.,"labels")
}

success_msg('Great! You mastered the R-Tutorial of this workshop. We recommend to do some other R courses on datacamp, like "Data Visualization with ggplot2"')
```
