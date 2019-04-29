---
title: 'Introduction to EDF'
description: 'Chapter description goes here.'
free_preview: true
---

## TITLE is missing

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
3. [It saves storage!]

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

In R there are already several packages to load the binary EDF-files. Here we use the package [edf](https://github.com/bwrc/edf). 

```read.edf(FILENAME)``` reads the edf-file to a nested list. You can access header information of the edf-file with ```LOADEDFILE$header.global```. ```LOADEDFILE$header.signal``` gives access to the specifications of the stored signals. And finaly ```LOADEDFILE$signal$SIGNALNAME$data``` returns the signal values with a sampling rate of ```LOADEDFILE$header.signal$SIGNALNAME$samplingrate```.  


`@instructions`
1. Load the package ```edf``` which was already installed by ```install_github("bwrc/edf")```. You can load a package by ```library(PACKAGE)```.
2. Check out the files in the directory ```data```. Load and store them to ```edf_files``` by using ```list.files()```.

`@hint`


`@pre_exercise_code`
```{r}
# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/28c0d209e3b04cc805f1f94c86dbd4e3ed224ac7/SL011_SL011_(1)_reduced.edf", destfile = "SL011.edf")


```

`@sample_code`
```{r}
# Load needed packages
library(edf)

# Load edf-file 'SL001.edf'
PAT011 <- read.edf('SL011.edf')

# Read header information
PAT011$header.global

# Store the data of "Magnitude" to data_pat011
data_pat011 <- PAT011$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat011
sr_pat011 <- PAT011$header.signal$Magnitude$samplingrate
```

`@solution`
```{r}
# Load needed packages 
```

`@sct`
```{r}

```

---

## Cut data

```yaml
type: NormalExercise
key: ea3e82c2e9
xp: 100
```

In the following we want to use only data from sleep. Thats why a common way is to use only the time between light off and light on. 

We know that subject PAT011/SL011 had a light off time of 22:40 and a light on time of 6:07. But the dataset starts already at 21:59:10! 
The data ist still available under ```data_pat011``` and the sampling rate at ```sr_pat011``` (in Hz).

`@instructions`
Cut ```data_pat011``` to the ligth off/on times! And store it to ```data_pat011```.
1. Calculate the time difference from file-start to light_off in seconds.
2. Calculate the time difference from file-start to light_on in seconds.
3. Cut ```data_pat011``` to the light off/on time

`@hint`
- Did you use the sampling rate as well to calculate the indices?
- Calculation in indices must be in brackets!

`@pre_exercise_code`
```{r}
# Load needed packages
library(edf)

# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/28c0d209e3b04cc805f1f94c86dbd4e3ed224ac7/SL011_SL011_(1)_reduced.edf", destfile = "SL011.edf")

# Load edf-file 'SL001.edf'
PAT011 <- read.edf('SL011.edf')

# Store the data of "Magnitude" to data_pat011
data_pat011 <- PAT011$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat011
sr_pat011 <- PAT011$header.signal$Magnitude$samplingrate
```

`@sample_code`
```{r}
# Time difference between starttime and light off time in seconds
diff_light_off <- 2450

# Time difference between starttime and light on time in seconds
diff_light_on <- 29270

# Cut data_pat011
data_pat011 <- data_pat011[(diff_light_off*sr_pat011):(diff_light_on*sr_pat011)]
```

`@solution`
```{r}

```

`@sct`
```{r}

```

---

## LIDS

```yaml
type: NormalExercise
key: 79162182e1
xp: 100
```

Human sleep is definded not only by circadian rhythms (circa 24 hours), but also by ultradian sleep-cycles. These ultradian cycles, with durations of 90-110 min, are evident in EEG-Data (NONREM-REM-sleep), respiratory and cardiovascular physiology and so forth. 

Recent research exposed movement patterns in locomotor activity that directly reflect ultradian rhythms  as well as parameters measured in the sleep laboratory. 
In order to clarify the patterns and enhance the difference between movement and non-movement, the locomotor activity is transformed non-linearly into 
**"locomotor inactivity during sleep" (LIDS)**

This inversion from activity to inactivity results in values from 0 to 100, whereas LIDS=100 means complete inactivity. 
The function for calculating LIDS is ```LIDS=100/(counts+1)```

Before calculating LIDS, we need to calculate so called "activity counts". "Activity counts" are the measure of activity. There are many different algorithms to generate these counts. For our calculations, we want to use a method used in early generation activity monitors, utilizing a threshold crossing technique. Activity with acceleration signal (Magnitude) exceed the threshold of #10 ? is counted, any acceleration below is not counted. The number of samples exceeding the threshold is summed over an epoch of 30 seconds and indicates the count value (maximum counts value = 30 s*128 Hz = 3840).


`@instructions`
2. Once we have imported all the data, we can calculate the "activity counts" using the For Loops option in R. The number of iterations (x) is calculated from the difference between the stop time and the start time, multiplied by 60 to obtain minutes and then with 2 to obtain 30 second intervals. We use 10 mg for the threshold.

3. Now we can display the "activity counts" to see what the data looks like. Use the function plot (x=, y=). In order to plot the "activity counts", we need to create a time for the x-axis. To create the time vector, use the command seq (from =, to =, by =).

`@hint`


`@pre_exercise_code`
```{r}
# Load needed packages
library(edf)

# load edf files
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/28c0d209e3b04cc805f1f94c86dbd4e3ed224ac7/SL011_SL011_(1)_reduced.edf", destfile = "SL011.edf")

# Load edf-file 'SL001.edf'
PAT011 <- read.edf('SL011.edf')

# Store the data of "Magnitude" to data_pat011
data_pat011 <- PAT011$signal$Magnitude$data

# Read the sampling rate of "Magnitude" and store it to sr_pat011
sr_pat011 <- PAT011$header.signal$Magnitude$samplingrate

# Time difference between starttime and light off time in seconds
diff_light_off <- 2450

# Time difference between starttime and light on time in seconds
diff_light_on <- 29270

# Cut data_pat011
data_pat011 <- data_pat011[(diff_light_off*sr_pat011):(diff_light_on*sr_pat011)]
```

`@sample_code`
```{r}
# Calculate the numbers of 30 second intervals in data_pat011
numb_of_int <- length(data_pat011)/sr_pat011/30

# loop over all intervals
for (i in 1:numb_of_int){
  a <- data_pat011 [n:(3840+n)]
  b <- a > 10
  counts <- append (counts, sum(b))
  print(sum(b))
  break
}


```

`@solution`
```{r}

```

`@sct`
```{r}

```
