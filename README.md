# Visual Discrimination Task

## Assumptions
### Devices
This task assumes the devices have been defined on the whisker server. The script expects the following digital lines to defined:

- IRBEAM - the feeding tray beam
- HOUSELIGHT
- TRAYLIGHT
- PELLET - the pellet dispenser

It also expects the following display(s) to be defined:

- CAGEDISPLAY - the display in the box
- (optional) HUMANDISPLAY - an external screen to display a running summary of the task.

The above can be defined in a device definition file. This will let you determine which lines and screens the above links to. 

Read more in the Whisker Manual:

- [Creating Device Definitions](http://egret.psychol.cam.ac.uk/whisker/help/WhiskerHelp/index.html?prog_serverdevicenames.htm) 
- [Setting Device Definition File](http://egret.psychol.cam.ac.uk/whisker/help/WhiskerHelp/index.html?console_setddf.htm)

Example:

    WhiskerServer v2.0 - DEVICE DEFINITION FILE - DO NOT ALTER THIS LINE
    ####################################################################
    # Visual Discrimination Frame Work 
    ###################################################################
     
     
    # Box 0 definition
    line    0      box0    IRBEAM
    line    1      box0    HOUSELIGHT
    line    2      box0    TRAYLIGHT
    line    3      box0    PELLET
    display 0      box0    CAGEDISPLAY
    display 1      human   HUMANDISPLAY

###Media
The task expects that any stimuli defined in the config file are present as Bitmap images in the Whisker Server Media directory. Read about [setting that directory](http://egret.psychol.cam.ac.uk/whisker/help/WhiskerHelp/console_setmultimediafolder.htm) in the Whisker manual. Warning! Not all BMP files are equal, and Whisker does not load 32bit bitmaps. Corel PaintShop Pro is recommended to create them.

## Installing the Task
To run this task you will need python installed. Check whether it is installed before going any further. 

Ensure you hve pip installed as well. 

 You can [Download Python Directly](https://www.python.org/downloads/)

## Config file

When you run the script it willl ask for the path to .yaml file. You can find out more about YAML [here](http://www.yaml.org/start.html)

Example:

    database_url: sqlite:///demo_task_results.db 
    server: 10.211.55.3
    subject: "Test"
    session: 1
    screen_resolution:
      width: 1024
      height: 768
    overview_screen_resolution:
      width: 1024
      height: 768
    stimuli_presentation_millis: 10000 # how long are the stimuli on the screen for
    iti_millis: 5000 # inter trial interval
    feedback_millis: 3000
    feedback_flash_millis: 250
    timeout_millis: 5000
    num_trials: 250
    max_task_time_millis: 3600000
    stimuli:
      - key: "star"
        file: "star.bmp"
      - key: "oval"
        file: "oval.bmp"
      - key: "speech"
        file: "Speech.bmp"
    pairings:
      - key: "AvB"
        stimuli:
          - key: "oval"
            reward_ratio: 1
            extinction_rate: 0.01
          - key: "star"
            reward_ratio: 0
        enabled: Yes
        frequency:
          appearance_ratio: 0.25
      - key: "BvC"
        stimuli:
        - key: "oval"
          reward_ratio: 1
        - key: "speech"
          reward_ratio: 1
        enabled: Yes
        frequency:
          fixed_rate: 4
          fixed_rate_start: 3
    conditions:
      - type: "trials"
        trials: 30
        changes:
          - key: "AvB"
            stimuli: 
              - key: "oval"
                reward_ratio: 0.5
     - type: "consecutive rewarded responses"
        responses: 5
        changes:
          - key: "BvC"
            enabled: No

### Fields
- database_url: database where the data will temporarily be stored. Use value from example unless you have reason to change it.
- server: the IP address for the Whisker server you wish to connect to
- subject: the value to write in the output files
- session: TODO 
- screen_resolution: the resolution Whisker will scale to for the touchscreen in the box
- overview_screen_resolution: as above but for an optional secondary screen which displays task information for humans
- stimuli_presentation_millis: how long the stimuli are presented for in milliseconds
- iti_millis: inter trial interval in milliseconds
- feedback_millis: how long the stimuli flash for in milliseconds
- feedback_flash_millis: determines the frequency at which the stimuli flash. This represents how long the feedback will be visible/invisible for. 
- timeout_millis: the milliseconds the task waits for input before the task progresses
- num_trials: the number of trials to generate
- max_task_time_millis: the total time is milliseconds the task can last
- stimuli: the stimuli used in this task. Each entry consists of a key which gives the name to refer to this stiumli as and a file which specifies the bitmap to use. The bitmaps must be placed in the Whisker Media folder (see above). Example:
      + key: "star"
        file: "star.bmp"
      + key: "oval"
        file: "oval.bmp"
      + key: "speech"
        file: "Speech.bmp"
- pairings: This lists the stimuli pairings or screens that the task will use. Stimuli are referred to using their keys specified in the stimuli list. Stimuli can be used in multiple pairings. However the task will become confused if a pairing consists on the the same stimuli. Each Pairing has a key, which is the name it will be refered to as, a list of stimuli and a frequency it occurs at. Optionally it can also be listed as enabled or disabled. Disabled stimuli are skipped over. 
    + frequeny: this controls how often the pairing occurs in the task. Either as a ratio of all pairing which is specified with the key appearance_ratio or with a fixed frequency. In the former case the pairing is distributed randomly across all the trials. The fixed_rate key specifies that this pairing will occur every n trials starting with the value specified by fixed_rate_start. Note the counting starts from 0, so in the last example the pairing would first appear on the 4 trial and then every 4 after that.
        - frequency:
            appearance_ratio: 0.25 
        - frequency:
            fixed_rate: 4
            fixed_rate_start: 3
    + stimuli: lists the stimuli defined in the stimuli list above, referred to by their key. Each stimuli has a reward_ratio which controls whether it pays out. 1 pays out 100% of the time and 0 pays out 0% of the time. 0.25 pays out a quarter of the time. extinction_rate controls the decrease in a stimulis reward_ratio and is subtracted after each time a pairing appears. Note:
        + A negative extinction_rate is going to cause the reward_ratio to increase
        + The same stimuli can have different reward_ratio values for different pairings. The extinction_rate only applies to specific pairing, not the simuli globally
- conditions: this list species modifications to the task that occur when certain conditions are met. These can eithr enable/disable pairings causing them to be skipped/included or stimuli reward_ratios for pairings to be changed. The supported conditions are:
    + trials: after _n_ trials
    + total responses: after _n_ responses
    + consecutive rewarded responses
    + consecutive unrewarded responses
    + total rewarded responses
    + total unrewarded responses

Examples:

      - type: "trials"
        trials: 30
        changes:
          - key: "AvB"
            stimuli: 
              - key: "oval"
                reward_ratio: 0.5
     - type: "consecutive rewarded responses"
        responses: 5
        changes:
          - key: "BvC"
            enabled: No

# Running the task
First set up the server and make sure the appropriate device definitions and stimui bitmaps are in place. Make a note of the [server's IP address](https://egret.psychol.cam.ac.uk/whisker/help/WhiskerHelp/index.html?console_serverstatusview.htm). This should be included in the config.yaml file, see above.

To start the client run:
  
  python visualdiscrimination/whisker_visualdiscrimination.py 

The task will then ask you for the config.yaml file to run from. 

To quit the task use Ctrl+C. It will write all the data it has to CSV files. 

# TODO
Test data with different session values
Allow config file to specify a device group

