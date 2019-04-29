---
title: 'Import and short overviewe about EDF-files'
description: 'Chapter description goes here.'
free_preview: true
---

## Example coding exercise

```yaml
type: NormalExercise
key: 2bafef99a3
lang: r
xp: 100
skills: 1
```

European Data Format (EDF) is a format for exchange and storage medical time series. EDF was published in 1992 and stores multichannel data. It allows different sampling rates for each singnal. It includes a global header (header.global), containing general information such as patient id, start- and stop-time, date, number of signals etc. and signal headers (header.signal) with technical specialisations (filtering, sampling, rate, calibration) for each signal. EDF+ is a newer version of EDF, which allows coding discontinous recordings as well as annotations, stimuli and events.
EDF and EDF+ are popular formats to record polysomnography and actimetry data, the kind of data which is used in this tutorium. 
The data is real data from the sleep-laboratory of the charité, measured by an actrigraph (SOMNOwatch), an wirst worn device for monitoring human rest and activity.


Advices for R beginners

- list.files (or dir) is used to produce a list containing the names of files in the named directory, arguments of this function are:
  1. path: path name to the working directory
  2. pattern: only file names which match the expression will be returned
- For Loops let us repeat (loop) through the elements in a vector and run the same code on each element. The general structure of a for loop: for (i in x), where x needs to be a specified range (eg. x= 1:10, means 1 to 10)
- With the function assign(x, value, envir = as.environment(pos), inherits = FALSE, immediate = TRUE)) you can assign a value to a name
- Subsetting: The operator $ is used to select a column from a table. The column values are extracted as a vector. Furthermore you can use [], indicating a number or range (eg 1:100), specifing which subset of the extracted vector to select
- To show the values stored in your variables, go to the console, type the name of the variable and press enter

`@instructions`
1. 

Install needed R packages and import five EDF-Files. The data is in our working directory.
To import all five datasets in one you can use the functions list.files (or dir) and then an For Loop (see Advices for R beginners for more detailled discription).
2. Look at the data, how many different signals are stored in the EDF data?
3. Print and store the patient id´s, the date of the measurement and the start-and stoptime. Which physical dimension and sampling rate is used for the "Activity"-signal?
4. The activity-data is stored in the "Magnitude"-Channel. Create for each patient a variable for the activity-data.

`@hint`


`@pre_exercise_code`
```{r}
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/ae044b65ee0fa1e3aaac52a36e962fb6799f3266/SL001_SL001_(1)_reduced.edf", destfile = "edf/SL001.edf")
download.file(url = "https://assets.datacamp.com/production/repositories/4958/datasets/3e7ccc19575c43743feaa4c4298480c0a561223c/SL002_SL002_(1)_reduced.edf", destfile = "edf/SL002.edf")

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
