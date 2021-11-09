# Current process:

should be all good except for

1. adding current and mean BPM in Text format

2. displaying alarms graphically

3. clear all the data when reconnect, and click the clear button

__have tested with both csv and live data__



# Host System Design

The test is divided into 3 parts: 
- ## Host Alarms
    - validate the input message, reject the wrong messages
    - raise alarm when BPM is too high or too low

- ## Data processing
    - translate the bytes back to the numbers and store the message into arrays
    - calculate the mean bpm in the last 15 seconds

- ## GUI
    - plot the pulse / BPM data
    - display any change 
    - display alarms
    - response to the users' instruction 
        - change thresholds
        - clear data
        - exit the GUI
        - change the data type to display

<br /> 
The functions are defined in the following files:

- alarmFunction: 

<br /> 
----

# Host Alarm Test

## Related functions and requirements:

Requirements| Function name | under file |
--|--|--
ALRM-1 (input data validation) | commAlarms() | alarmFunction 
ALRM-2, ALRM-3 (raise alarm when BPM is low or high) | bpmAlarm() | alarmFunction
ALRM-4 (alarms are indicated graphically)|**Haven't done**|alarmFunction
ALRM-5, ALRM-7, ALRM-11 (alarms with timestamps) |**Haven't done**|alarmFunction
ALRM-6, ALRM-8, ALRM-9, ALRM-10|**setThresholds()**|alarmFunction
---

Note:
- Inside of the commAlarms, there are seqNumCheck(), checksum() functions to meet the ALRM-1-ii, ALRM-1-iii
- Besides the required conditions, there should be functions to reject the undesired message with 'unwanted length' and 'lack flags'


<br /> 

## Test strategy: 
1. Create csv file with dummy data, test with the dummy data and print the alarm strings and timestamp in the terminal window. The dummy data should contains some manually made errors
1. Test the alarm functions with live data (data keep updated from arduino)
1. Integrate the host alarms with the GUI functions, test with dummy data, see if the alarms will display in GUI (This will be test in the Host visualisation part)


<br /> 
----
# Host Visualisation Test
## Related functions and requirements:

Requirements| Function name | under file |
--|--|--
HDIS-1, HDIS-7, HDIS-8 | draw_gui_window() | guiFunctions
HDIS-2 (latency of less than 2 seconds) |???|--
HDIS-3 (update less than 1 second) |mainloop|--
HDIS-4 |plotFigure()|guiFunction
HDIS-5, HDIS-6 |**Haven't done**|guiFunction
set High and Low threshold| setThreshold() inside of guiAction()| alarmFunction
log all the actions|guiAction()|guiFunction
clear data| guiAction()|guiFunction
exit GUI| guiAction()| guiFunction


---

## Note:
- For HDIS-2, we don't have specific functions to control it, because it happens naturally if all the functions runs less than 2 seconds. But we can write function to test it
- Make 2 sliders for user to set the BPM thresholds
- For data displaying, we draw both graphs at the same time, but in different canvas, which can be selected through the tabs above the graph
- To support the function plotFigure(), we have other functions such as get_this_array(), update_array(), addFigure()



<br /> 

## Test strategy: 
1. Test the layout of the GUI, see if we have all the element we need (graph, logtext window, buttons, etc.)
1. Test the GUI actions, for example alarms, button, log text, see if all the actions can be logged
1. Create a seperate window to display the animate plot with dummy data, then try with live data
1. If the data displaying window (canvas) works well, integrate it into the entire layout

<br /> 

# Software Description
## 1. seqnumCheck()
cato| comment
--|--
Description| check if the sequence number of this message is correct
Input| raw_message, oldSeqNum
Output| True/False 
Author| Jacinta and Yunyao Duan

<br /> 

## 2. checksum()
cato| comment
--|--
Description| check if the received checksum and calculated
Input| raw_message
Output| True/False
Author| Yunyao Duan

<br /> 

## 3. commAlarm()
cato| comment
--|--
Description| combines all the comm alarm requirements, update alarm_string if there's comm error
Input| raw_message, seqnumCheck(), checksum()
Output| True/False, alarm_string
Author| Yunyao Duan

<br /> 

## 4. fourBytesToNum()
cato| comment
--|--
Description| convert the bytes in the raw message to binary number, and store the data to class for later use
Input| raw_message
Output| this_message, data.pulse, data.bpm
Author| Yunyao Duan


<br /> 

## 5. meanBPM()
cato| comment
--|--
Description| calculate the mean BPM over the last 15 seconds
Input| bpmCnt (count for 15 loops)
Output| data.mean_bpm
Author| Yunyao Duan


<br /> 

## 6. get_data_to_draw()
cato| comment
--|--
Description| get the data to display in this frame
Input| dt_w, DATALEN_w, dt_b, DATALEN_b
Output| t_w_this, pulse_this, t_b_this, bpm_this
Author| Yunyao Duan
