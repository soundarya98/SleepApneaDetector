# SleepApneaDetector

This repository contains codes that were used in the software of making a Portable Holter Monitor with Real Time Threat Detection used to detect sleep apnea. 
This can potentially be used by soldiers working in remote conditions to detect conditions like breathlessness and fatigue. 

This repository contains the software for the Holter Monitor.

Steps:
1. Data extraction and storage in a neat format-

The data is from physionet, and consist of 70 records, divided into a learning set of 35 records, and a test set of 35 records (x01 through x35). Recordings vary in length from slightly less than 7 hours to nearly 10 hours each. Each recording includes a continuous digitized ECG signal, a set of apnea annotations (derived by human experts), and a set of machine-generated QRS annotations. The files with names of the form rnn.dat contain the digitized ECGs. The .hea files are (text) header files that specify the names and formats of the associated signal files. The .apn files are (binary) annotation files, containing an annotation for each minute of each recording indicating the presence or absence of apnea at that time. An example is shown below:

![Dataset](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/Dataset.png)


2. Data Segmentation-

We segment the data into reasonable units of 3 minutes each so as to detect apnea in each segment, according to the format followed by the test data.

3. Data Cleaning-

Using a custom generated sine wave, we filtered the ECG signal to extract the peaks (the part of the signal that we are concerned with).
![Sine Filter](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/Sine_Filter.png)

4. Signal Filtration-

Filtering using high pass filter to allow any frequency above 0.5 should remove respiratory artifacts (0.12Hz to 0.50HZ) ie 8-30 breaths per minute. However, we found that 20Hz cut off gave better suppression of non-R peak aspects of the ECG.
![Butter Pass Filter](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/Butter_pass_filter.png)


6. RR interval calculation-

The parts of a signal are shown below. From our research, we found that the R-R intervals in signals measured while sleeping were more correlated with apneatic data. So, our next step was to extract these R-R intervals as part of feature extraction.
![PQRST of signal](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/PQRST-In-the-ECG-waveform.png)

We used the criteria that peaks are at least 2 X std deviation above the mean of the signal.

The final result of filtering is shown below:
![After Pre-processing](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/AfterPreprocessing.png)

7. Removal of outliers-

A possible problem that could arise is if our criteria for idetifying peaks fails for a peak, and thus, we obtain an unnaturally long R-R interval. To avoid this, we removed the abnormal R-R intervals by replacing them with the mean R-R interval.

8. Calculation of nn50- (variant 1 and 2)

Calc NN50 measure (variant 1), is defined as the number of pairs of adjacent RR- intervals where the first RR- interval exceeds the second RR- interval by more than 50 ms.
(Note that my calculation is not strictly NN50 because the resolution doesn't show if it's 51ms, it only shows if it's 60ms greater)

The NN50 measure (variant 2) is defined as the number of pairs of adjacent RR-intervals where the second RR- interval exceeds the first RR interval by more than 50 ms.

10. Apnea detection-

We then used various ML algorithms such as SVM, RF, KNN, DecisionTreeClassifier, XGBoost etc for a comparitive study of the performance of the various algorithms.
We achieved an accuracy of 90% using Random forests. 
The confusion matrix is given below:

![Confusion Matrix](https://github.com/soundarya98/SleepApneaDetector/blob/master/Images/RF_ConfusionMatrix.png)




