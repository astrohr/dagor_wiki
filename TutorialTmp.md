## Using DAGOR server application (TCS)

1. Use SSH to connect to DAGOR server (IP `10.1.4.120`). Get your own username & password beforehand at telescope@astro.hr. It is recommended to have 4 concurrent sessions. Recommended Windows client is MTPuTTY.
2. Switch to user `dagor` by typing `dagor`. If prompted for password, use `dagor` again.
3. The python script that controls the telescope is located at `~/tca`. One can run it using `python -m ~/tca <command>`. An alias is available to shorten this: `d <command>`
4. Typing just `d` returns a list of available commands. These commands are described in detail in the next section.

## DAGOR tca command list

### `api run` 

Startas an API process. API needs to be running so that ASCOM drivers for the focuser and lights can operate.

### `get (celest | local | altaz)`

Returns current position of the telescope in (RA, DE), (HA, DE) or (ALT, AZ) coordinates, respectively.

Azimuth is measured from north eastward.

### `get chirality`

Returns `cw` or `ce`.

`cw` stands for "west chirality" and means that the tube is on the west side of pillar (while observing the prime meridian due South). 
`ce` stands for "east chirality".

Careful: if we pass through NCP using DE, chirality is flipped!

### `goto home [ce]` 

Positions the telescope in maintenance position toward the south balcony.
Default chirality is `cw`.


### `goto home2 [cw]` 

Position the telescope in maintenance position toward the north balcony.
Default chirality is `ce`.

### `goto altaz <ALT> <AZ> [ce | cw | cc] [quick | track]`

Positions the telescope to point towards the specified "altaz" coordinates (altitude-azimuth), using specified chirality or closest path (`cc`, default). 

By default, the telescope does not track (altaz coordinates do not move). To initiate tracking after arriving at the coordinates, use "track" argument. 

"Quick" positioning has bigger tolerances on positioning error, so positioning is faster but less precise.

Status information is displayed while telescope is moving and tracking.


### `goto celest <RA> <DE> [ce | cw | cc] [quick | notrack]` 

Positions the telescope to point towards the specified equatorial coordinates, using specified chirality or closest path (`cc`, default). 

By default, the telescope does track (celestial coordinates move). Use `notrack` argument to prevent tracking once the coordinates have been reached.

> TODO: do we have notrack here?  
> TODO: do we have quick here? 

"Quick" positioning has bigger tolerances on positioning error, so positioning is faster but less precise.

Status information is displayed while telescope is moving and tracking.


### `goto this`

Telescope starts tracking at the current position.


### `goto stellarium` 

Accepts object information pasted from Stellarium, in order to avoid having to manually type the coordinates.

In Stellarium: 
 * click on an object and press `Ctrl-C`

In terminal: 
 * type `goto stellarium`
 * press `Enter`
 * right click anywhere in the terminal window to paste the copied text
 * press `Enter` twice

#### What to check in Stellarium

It is easy to get wrong data our of Stellarium. Check these points before using `goto stellarium` function:
 * "Current time" icon is enabled
 * "Real time" icon is also enabled
 * Observatory position is set to "Tičan" (usually is)
 * Desired object is selected
    * Information that will be copied is displayed in the top left part of the screen
    * Where Stellarium is "pointed" is not relevant, only what has been selected (i.e. clicked). Pressing `space` will point stellarium back to the selected object.


### `sync console`

Starts the "synchronization console" interface, which is used for manual correction of telescope position or manual movement for short distances.


### `set celest <RA> <DE>`

Corrects the current coordinates. This is primarily useful when an image is plate-solved - coordinates of the center of the image should be "set" using this command to get more accurate positioning around those coordinates.

Important: tracking needs to be restarted after this command!

 
### `focus get`

Shows current value of the focuser step counter.


### `focus set <steps>`

Sets step counter to specified value.


### `focus goto <steps>` 

Moves focuser to specified focuser position (number of steps).


### `dome (up | down)`

Moves the dome left or right, respectively, if viewed from the control room.

![220px-right-hand_grip_rule svg](https://user-images.githubusercontent.com/1173748/29496066-565f601e-85cb-11e7-9330-e89bf91a51d0.png)


### `dome (open | close)` 

Operates dome door. There is no indicator of actual door position, check with the webcam!


### `dome stop`

Stops dome rotation and doors.


### `lights (0 | 1 | 2 | 3)` 

Switches lights on and off.

 * `0` - lights off
 * `1` - light 1 on
 * `2` - light 2 on
 * `3` - both lights on

### `lights`

Displays the current status of the lights.


## Preliminary focuser adjustment

### Reset focuser counter

Focuser counter should be reset after turning it on, since it does not have an absolute encoder - the program only counts steps it sends to the stepper. Also MaxIm DL doesn't know how to work with negative focuser position number. 

To accomplish this, we will move the focuser completely "in" (for example, `d focus goto -5000`). The focuser will reach some position in the negative numbers before hitting the limit, e.g:
```
positioning.......
position: -3187
```

There we set focuser position to position zero (`d focus set 0`).

Lastly we can return the focuser to the starting position which is the negative of the final position, e.g: `d focus goto 3187`


If you get a trackback error at a first access to focuser, simply repeat the command. It should work. If the problem persists, recheck all connections.


# Starting tca api and preparing the software environment for observing

Have four concurrent sessions to TCS opened:
 * one for goto commands and viewing the telescope status. 
 * another for correcting position (`d set celest` commands)
 * third for API
 * fourth for miscellaneous commands, e.g: dome and lights, fans, etc.


## Start the API

Run the API using `d api run`.

API window will report all calls from MaxIm to the API. HTTP status around 200 is OK. If you get persistent statuses around 500, restart the API, check connections etc.

## Webcam

A webcam is mounted at the front of the telescope tube, viewing in the same direction as the telescope. It can be access it through Windows camera app (recommended) or MaxIm DL. The Windows camera app shows a rectangle that represents telescope FOV. Use the camera to adjust the dome position, check for lights, dome door, telescope position etc.

> TODO screenshot

## Time sync

Before observing, synchronize the computer clock with the NTP server!


# Using MaxIm DL

For observing, two windows are used: Camera control window (`Ctrl-W`) and Observatory control window (`Ctrl-T`).

## Camera control window

Setup tab, connect the camera. We are currently using SBIG STL-11000. If the camera is correctly connected, you should see sensor temperature. Start the cooler, setpoint should be either -15 or -20. If you have trouble connecting, recheck camera power and all data connections (USB extenders and hub, specifically). Check if camera is shown in Windows device manager.


## Camera control window 

Expose tab is used for imaging. 

~This camera uses a filter wheel and a monochrome CCD, so if one wants to have color images, he should do multiple exposures with different color filters and combine the images.~

For asteroid photometry and astrometry, only the clear filter is used. We ~will~ use Single and Autosave modes of exposure. ~If you take snapshots, use Single.~ Otherwise, use Autosave. 

CAREFUL: Before ANY imaging, set the save image path to D:\Pictures\[YYYY-MM-DD] and check that New buffer option is enabled. //autosave?! Imaging mode should be set to "No calibration".

Next to Autosave option, ~one can~ open an Autosave dialog. Use it for batch imaging. ~First set the filename and save image path to D:\Pictures\[YYYY-MM-DD].~ 

CAREFUL: Images recorded using autosave will be saved in D:\Pictures\[YYYY-MM-DD]\[YYYY-MM-DD]! Now enter as many exposure batches as you wish. 

CAREFUL: you  must enter something to suffix. Suffix should start with a letter. Usually you enter a letter corresponding to filter, and/or binning information.

Another VERY useful window is Screen stretch window. It plots a brightness histogram, and you can stretch a desired brightness interval to full range, in order to increase contrast. Use + and - magnifier buttons to zoom around the brightness intervals.

## Image calibration

> not every time!

In order to calibrate images, one should acquire dark and flat images.

Dark images should be done with same exposures, CCD temperature and binning as real images. You can record darks either before or after observing. For asteroids, you basically need exposure time of 30, 60 and 180 seconds, and binnings 1 and 2. While recording darks, make sure the dome is closed, windowblinds toward control room completely shut, door shut and no lights are on. Acquire a couple (about 5) of images using the Camera Control window, name them somehow consistently. Use "dark" preset, so that MaxIm writes to FITS header that this image is dark! Otherwise, you will experience slight problems with Calibration wizard.

Flat images should be done with same filter as real images. If you plan to do color images, you should have flat image for every filter. Acquire flat images with the telescope cover on, either using artificial light in the dome or using skylight during dusk and dawn, by pointing the telescope opposite from the sun (eastward in the dusk, westward in the dawn), to altitude around 45. Start with exposure time around 0.1-1s. Use "flat" preset in the camera control window, so that MaxIm writes to FITS header that this image is dark! Otherwise, you will experience slight problems with Calibration wizard. Look at the image histogram in the screen stretch window. There MUST NOT be stars in the image (right side of the histogram), and maximum brightness level MUST be around 20000-25000 (of 65535). Adjust exposure time if necessary, when exposure time is OK, acquire 5 images, name them somehow consistently.

Put all flats and darks in the same subfolder of the today's working folder (the YYYY-MM-DD one). Run the calibration wizard and point it to folder with flats and darks. CAREFUL: before you click Finish, check the box to show additional details. Check if all images are added to calibration groups. If not, manually add a new group for every flats/darks batch and add images to corresponding groups. Groups should have correct tags (dark, flat, exposure times etc.)

When you acquired a batch of images, you should calibrate them. CAREFUL: In the working folder, make a new one, preferably named "calibrated". Copy the images you want to calibrate to that folder, so you don't overwrite raw images. Close all images currently opened in MaxIm and then open only the ones you want to calibrate from the "calibrated" folder. Go to Process --> Calibrate all and then File --> Save all. Overwrite the originals. CAREFUL: Overwrite confirmation dialog will pop up for every file. Do not close it by hitting the Enter key, since that action will NOT overwrite the file. Either hit the "Y" key or click on Yes.


## Focusing

Focus heavily changes with temperature (tube thermal dilatation). Therefore, one should refocus every ~15min in the beginning of the night during the summer, while tube temperature is not yet stabilized. To minimize seeing effect on focus, use higher altitudes (for example 75°).

For focusing, use Observatory window --> Focuser tab. CAREFUL: You should do focuser preliminary adjustment before attempting to autofocus! CAREFUL: NEVER attempt to autofocus while not close to actual focus point! If you do, autofocus algorithm will either get stuck and you will have to force-close MaxIm or focus point will be wrongly estimated. You should use manual movement of focuser and Snapshot button to manually pick focus approximately. You can set exposure time for snapshot, 6 seconds is a good starting point. A good starting point for focuser position is around 3500-3800 steps. CAREFUL: Always wait until the focuser reached its destination, since image acquisition does not wait the focuser unit to finish moving!

Now you have a snapshot and you should select a star for focusing. The star should be in the center of the image and must not be saturated. How to determine if star is saturated? Use screen stretch, put the higher bound to maximum value (far right) and lower bound close to that (~90% maximum value). Stars that remain visible are certainly saturated. Click on the desired star. In the focuser window, you should see that a new star for focusing is selected, and its coordinates on the image. CAREFUL: If you acquired the image using camera control window and not focuser window --> snapshot, you cannot select a star for focusing! Read out its 1/2 flux diameter. Go to options and set target 1/2 flux diameter for 2 units higher than the current for your focusing star. CAREFUL: NEVER attempt to autofocus if you did not adjust 1/2 flux diameter correctly! If you do, autofocus algorithm will either get stuck and you will have to force-close MaxIm, since in this case Abort button does not work!

Start autofocus and observe the focusing curve. It should be shaped like a smooth parabola. If it is not, abort autofocusing and re-attempt to find focus manually. It is normal that the algorithm will go beyond the focus point. You can wait until it's stopped or abort it when measurement starts to drop again. In the status window, you will have optimal focus position, but the program will not go automatically to it. Try it with a snapshot and correct if necessary. In the same window, you have a history of all measurements, that's helpful. The lower the 1/2 flux diameter, the better. CAREFUL: focus is very sensitive, when doing manual corrections, make steps in range of 5-50.

During summer nights, the telescope will cool gradually. Therefore, in the beginning couple of hours in the night, you should gradually pull the focuser out (higher step values).

One can adjust the focuser during image download, but be quick, especially if you use binning.

## Plate solving

Telescope goto is not perfectly precise. Therefore, you should plate-solve an image and correct the telescope position whenever you move the telescope significantly (~ 1h RA, ~10° DE). Using the camera control window, take a single image with short exposure (6 seconds is enough, generally, and you may use binning 2 to reduce download time) at the coordinates you wish to acquire images. Start PinPoint Astrometry and do online search for coordinates. When online search is finished, PinPoint window will mysteriously escape. Click on PinPoint button until the window appears again. Now click on Process button. After some time, actual coordinates will be displayed. Copy them to clipboard (select with mouse, Ctrl-C).

Now you should adjust the telescope coordinates. CAREFUL: You should do this process rather quickly. Stop the tracking. Run command d set celest [actual coords from PinPoint]. CAREFUL: You should format the coordinates, so that DE coordinates are separated with colons and that there are no spaces in RA coordinates. CAREFUL: You MUST restart tracking IMMEDIATELY after setting the telescope coordinates!

Make a new image and redo PinPoint --> Process. Check coordinates. A couple of seconds/arcseconds mismatch is OK. If needed, repeat sync, or use sync console to nudge the telescope to right coordinates. CAREFUL: Do not rely on coordinates displayed in goto/tracking status, in dagor session! Rely only on coordinates measured by PinPoint!

## Dome control

> too many terms for the same thing (slew, position, point)

During ~slewing~, you may simultaneously move the dome. Turn the lights on and open the webcam app. Using dome commands in a separate dagor session (not the one you use for goto!!!), move the dome in the desired direction. Optimal dome position is when the FOV is near the LEFT edge of the opening, since telescope is moving towards right side during tracking. One should check/adjust dome position every 15-20min or every goto operation.

~CAREFUL~: The dome has great inertia, so it stops slowly (couple of seconds stopping time from full speed). It is equipped with a soft starter, so it accelerates even slower.

# Data organization, todo list and log

On the operator computer "Harmonia", images are stored in D:\Pictures\[YYYY-MM-DD]. Before observing, you should create a folder for tonight's observations. Have a text file called "todo-YYYY-MM-DD.txt", where should be a chronological list of planned objects in the following format:
[name]
[RA DE]
RA and DE should be formatted the following way (dashes represent numbers): --h--m--.--s --:--:--.--  That way, one can easily copy-paste coordinates to telescope control command.
Either comment whether an object was imaged, or write down names and coordinates of actually imaged objects into a separate file called "log-YYYY-MM-DD.txt".

# Observation preparation rough checklist
Couple of hours before dusk, turn on aircondition and the big fan in the dome if needed (esp. summer). Check weather forecast (clouds and precipitation radar) and try to predict weather for tonight.
Turn on all electrical equipment and the dagor server. Start dagor sessions, tca API and prepare software environment for observing. See also: section Data organization, todo list and log.
If no flats and darks for tonight's planned imaging are done, image them. (See: section "Image calibration"). CAREFUL: DO NOT move the telescope anywhere from home position before telescope cover is removed!
In the dusk, open the dome and point the opening so that it corresponds with the tube, so that cool air can easily enter the tube. Telescope cover should be removed.
Perform preliminary focus adjustment. By this point, the sky should be dark enough so that stars are visible.
Perform rough manual focusing and autofocus.

# Observation workflow
Slew to first undone object in your todo list. Check dome and move if necessary. TURN OFF THE LIGHTS!!!
Check focus and refocus if necessary.
Do plate solving if this is your first object tonight or you slewed significantly.
Configure batch imaging using Autosave option, perform imaging. If batch lasts longer than 15min, recheck dome position. Refocus if necessary.
Calibrate the images.
