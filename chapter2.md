---
title: 'Advanded Task'
description: ""
---

## Insert exercise title here

```yaml
type: NormalExercise
key: 672518f89f
xp: 100
```

In this advanced task, we want to determine the **distributions of activity durations and rest durations**. An activity episode is characterized by an acceleration < 10 mg (inactivity) immediately prior to the beginning of the episode, accelerations >=10 mg (activity) during the episode, and an <10 mg immediately after the episode. Rest episodes are just the complement, i.e., episodes characterized by <10 mg throughout. The duration of the episode is the time from beginning of the episode to its end. 

A distribution of durations can most easily be studied by a histogram of the durations. However, it may turn out to be helpful to use a logarithmic width of the histogram bins, which is most easily been done by analyzing the logarithm of the durations instead of the durations themselves. Compare both versions to identify advantages and disadvantages. Alternatively, you might want to try to investigate the **transition probability** of activity (or rest) episodes as introduced in the presentation of Frank Pillmann.

The acceleration data is available as ```data_pat``` and the sampling rate (in Hz) as ```sr_pat```.

`@instructions`


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
