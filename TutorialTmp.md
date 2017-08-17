Using DAGOR server application
1)	Use SSH to connect to DAGOR server (10.1.4.120). User/pass shall be provided to you. It is recommended to have 4 concurrent sessions. Recommended Windows client is MTputty.
2)	Switch user to user dagor, using password dagor. It may be done automatically.
3)	The python script that controls the telescope is located at ~/tca. One can run it using python -m ~/tca [command]. To shorten the command, there is alias: d [command]
4)	Typing only d returns a list of commands. Commands are described in detail in the next section.

DAGOR tca command list
api run --> API needs to be started in order to work with the ASCOM driver for the focuser and lights.
get [celest | local | altaz] --> returns coordinates in RA,DE | HA,DE | ALT,AZ, respectively. Azimuth is measured from north toward east.
get chirality --> returns cw if the tube is west from RA axis or ce if tube is east from RA axis. Careful, if we pass through NCP using DE, chirality is flipped!
goto home [ce] --> goes to maintenance position toward the right balcony (viewed from control room), by default with cw chirality
goto home2 [cw] --> goes to maintenance position toward the left balcony (viewed from control room), by default with ce chirality
goto altaz [coords] [ce | cw | cc] [quick | track] --> goes to specified altaz coordinates, using specified chirality or closest path (cc, default). By default, the telescope does not track! For tracking, use "track" argument. Quick positioning has bigger tolerances on positioning error, but positioning is faster. While slewing or tracking, one can view telescope status.
goto celest [coords] [ce | cw | cc] [quick] --> goes to specified altaz coordinates, using specified chirality or closest path (cc, default). By default, the telescope does track. Quick positioning has bigger tolerances on positioning error, but positioning is faster. While slewing or tracking, one can view telescope status.
goto this --> effectively, start tracking
goto stellarium --> one can paste stellarium data, in order to avoid typing the coordinates
sync console --> runs sync console, which is used for manual correction of telescope position or manual movement
focus set --> get focuser step counter value
focus set [steps] --> set step counter to specified value
focus goto [steps] --> go to specified focuser position
dome [up | down | stop] --> moves the dome left or right, respectively, if viewed from the control room
dome [open | close] --> operates dome door. There is no indicator of actual door position, check with the webcam!
lights [0 | 1 | 2 | 3] --> switches lights on and off. 0 - lights off. 1 - light 1 on. 2 - light 2 on. 3 - both lights on.
set celest [coords] -->

Using goto stellarium function
1) Make sure that Stellarium observer's location and time corresponds to actual telescope location and time. CAREFUL: In Stellarium, check that you use actual time and observatory coordinates and that tracking is enabled.
2) Click on the desired object. In the left corner, a big information text should appear. Copy it, using Ctrl-C.
3) Run the "goto stellarium" function without any extra arguments. Paste the text data into the session window. Putty and derivatives use right click for paste command. Press enter button TWICE.

Preliminary focuser adjustment
Before observing, one should calibrate the focuser, since it does not have an encoder - the program only counts steps it sends to the stepper. MaxIm DL doesn't know how to work with negative focuser position number. So, we will move the focuser completely inside (for example, d focus set -5000, the stepper will jump some steps but we are sure that the focuser is completely inside), and set focuser position to zero (d focus set 0). Now we can return the focuser closer to the actual focus point, around 3500 steps.
If you get a trackback error at a first access to focuser, simply repeat the command. It should work. If the problem persists, recheck all connections.

Starting tca api and preparing the software environment for observing
Have four concurrent sessions to dagor opened. Use one for goto commands and viewing the telescope status. Use another for sync console. Use third for API. Use fourth for miscellaneous, for example, dome and lights.
Run the API using d api run. API window will report all calls from MaxIm to the API. HTTP status around 200 is OK. If you get persistent statuses around 500, restart the API, check connections etc.
A webcam is mounted at the front of the telescope tube, viewing in the same direction as the telescope. One can access it through Windows camera app (recommended) or MaxIm. The Windows camera app shows a rectangle that represents telescope FOV. Use the camera to adjust the dome position, check for lights, dome door, telescope position etc.
Before observing, synchronize the computer clock with the NTP server!
Start MaxIm DL.

Using MaxIm DL
For observing, two windows are used: Camera control window (Ctrl-W) and Observatory control window (Ctrl-T).

Using the Camera control window --> Setup tab, connect the camera. We are currently using SBIG STL-11000. If the camera is correctly connected, you shoud see sensor temperature. Start the cooler, setpoint should be around -15, -20. If you have trouble connecting, recheck camera power and all data connections (USB extenders and hub, specifically). Check if camera is shown in Windows device manager.

Camera control window --> Expose tab is used for imaging. This camera uses a filter wheel and a monochrome CCD, so if one wants to have color images, he should do multiple exposures with different color filters and combine the images. For asteroid photometry and astrometry, only the clear filter is used. We will use Single and Autosave modes of exposure. If you take snapshots, use Single. Otherwise, use Autosave. CAREFUL: Before ANY imaging, set the save image path to D:\Pictures\[YYYY-MM-DD] and check that New buffer option is enabled. //autosave?! Imaging mode should be set to "No calibration".
Next to Autosave option, one can open an Autosave dialog. Use it for batch imaging. First set the filename and save image path to D:\Pictures\[YYYY-MM-DD]. CAREFUL: Images recorded using autosave will be saved in D:\Pictures\[YYYY-MM-DD]\[YYYY-MM-DD]! Now enter as many exposure batches as you wish. CAREFUL: you  must enter something to suffix. Suffix should start with a letter. Usually you enter a letter corresponding to filter, and/or binning information.

Another VERY useful window is Screen stretch window. It plots a brightness histogram, and you can stretch a desired brightness interval to full range, in order to increase contrast. Use + and - magnifier buttons to zoom around the brightness intervals.

Image calibration
In order to calibrate images, one should acquire dark and flat images.

Dark images should be done with same exposures, CCD temperature and binning as real images. You can record darks either before or after observing. For asteroids, you basically need exposure time of 30, 60 and 180 seconds, and binnings 1 and 2. While recording darks, make sure the dome is closed, windowblinds toward control room completely shut, door shut and no lights are on. Acquire a couple (about 5) of images using the Camera Control window, name them somehow consistently. Use "dark" preset, so that MaxIm writes to FITS header that this image is dark! Otherwise, you will experience slight problems with Calibration wizard.

Flat images should be done with same filter as real images. If you plan to do color images, you should have flat image for every filter. Acquire flat images with the telescope cover on, either using artificial light in the dome or using skylight during dusk and dawn, by pointing the telescope opposite from the sun (eastward in the dusk, westward in the dawn), to altitude around 45. Start with exposure time around 0.1-1s. Use "flat" preset in the camera control window, so that MaxIm writes to FITS header that this image is dark! Otherwise, you will experience slight problems with Calibration wizard. Look at the image histogram in the screen stretch window. There MUST NOT be stars in the image (right side of the histogram), and maximum brightness level MUST be around 20000-25000 (of 65535). Adjust exposure time if necessary, when exposure time is OK, acquire 5 images, name them somehow consistently.

Put all flats and darks in the same subfolder of the today's working folder (the YYYY-MM-DD one). Run the calibration wizard and point it to folder with flats and darks. CAREFUL: before you click Finish, check the box to show additional details. Check if all images are added to calibration groups. If not, manually add a new group for every flats/darks batch and add images to corresponding groups. Groups should have correct tags (dark, flat, exposure times etc.)

When you acquired a batch of images, you should calibrate them. CAREFUL: In the working folder, make a new one, preferably named "calibrated". Copy the images you want to calibrate to that folder, so you don't overwrite raw images. Close all images currently opened in MaxIm and then open only the ones you want to calibrate from the "calibrated" folder. Go to Process --> Calibrate all and then File --> Save all. Overwrite the originals. CAREFUL: Overwrite confirmation dialog will pop up for every file. Do not close it by hitting the Enter key, since that action will NOT overwrite the file. Either hit the "Y" key or click on Yes.

Focusing
Focus heavily changes with temperature (tube thermal dilatation). Therefore, one should refocus every ~15min in the beginning of the night during the summer, while tube temperature is not yet stabilized. To minimize seeing effect on focus, use higher altitudes (for example 75°).

For focusing, use Observatory window --> Focuser tab. CAREFUL: You should do focuser preliminary adjustment before attempting to autofocus! CAREFUL: NEVER attempt to autofocus while not close to actual focus point! If you do, autofocus algorithm will either get stuck and you will have to force-close MaxIm or focus point will be wrongly estimated. You should use manual movement of focuser and Snapshot button to manually pick focus approximately. You can set exposure time for snapshot, 6 seconds is a good starting point. A good starting point for focuser position is around 3500-3800 steps. CAREFUL: Always wait until the focuser reached its destination, since image acquisition does not wait the focuser unit to finish moving!

Now you have a snapshot and you should select a star for focusing. The star should be in the center of the image and must not be saturated. How to determine if star is saturated? Use screen stretch, put the higher bound to maximum value (far right) and lower bound close to that (~90% maximum value). Stars that remain visible are certainly saturated. Click on the desired star. In the focuser window, you should see that a new star for focusing is selected, and its coordinates on the image. CAREFUL: If you acquired the image using camera control window and not focuser window --> snapshot, you cannot select a star for focusing! Read out its 1/2 flux diameter. Go to options and set target 1/2 flux diameter for 2 units higher than the current for your focusing star. CAREFUL: NEVER attempt to autofocus if you did not adjust 1/2 flux diameter correctly! If you do, autofocus algorithm will either get stuck and you will have to force-close MaxIm, since in this case Abort button does not work!

Start autofocus and observe the focusing curve. It should be shaped like a smooth parabola. If it is not, abort autofocusing and re-attempt to find focus manually. It is normal that the algorithm will go beyond the focus point. You can wait until it's stopped or abort it when measurement starts to drop again. In the status window, you will have optimal focus position, but the program will not go automatically to it. Try it with a snapshot and correct if necessary. In the same window, you have a history of all measurements, that's helpful. The lower the 1/2 flux diameter, the better. CAREFUL: focus is very sensitive, when doing manual corrections, make steps in range of 5-50.

During summer nights, the telescope will cool gradually. Therefore, in the beginning couple of hours in the night, you should gradually pull the focuser out (higher step values).

One can adjust the focuser during image download, but be quick, especially if you use binning.

Plate solving
Telescope goto is not perfectly precise. Therefore, you should plate-solve an image and correct the telescope position whenever you move the telescope significantly (~ 1h RA, ~10° DE). Using the camera control window, take a single image with short exposure (6 seconds is enough, generally, and you may use binning 2 to reduce download time) at the coordinates you wish to acquire images. Start PinPoint Astrometry and do online search for coordinates. When online search is finished, PinPoint window will mysteriously escape. Click on PinPoint button until the window appears again. Now click on Process button. After some time, actual coordinates will be displayed. Copy them to clipboard (select with mouse, Ctrl-C).

Now you should adjust the telescope coordinates. CAREFUL: You should do this process rather quickly. Stop the tracking. Run command d set celest [actual coords from PinPoint]. CAREFUL: You should format the coordinates, so that DE coordinates are separated with colons and that there are no spaces in RA coordinates. CAREFUL: You MUST restart tracking IMMEDIATELY after setting the telescope coordinates!

Make a new image and redo PinPoint --> Process. Check coordinates. A couple of seconds/arcseconds mismatch is OK. If needed, repeat sync, or use sync console to nudge the telescope to right coordinates. CAREFUL: Do not rely on coordinates displayed in goto/tracking status, in dagor session! Rely only on coordinates measured by PinPoint!

Dome control
During slewing, you may simultaneously move the dome. Turn the lights on and open the webcam app. Using dome commands in a separate dagor session (not the one you use for goto!!!), move the dome in the desired direction. Optimal dome position is when the FOV is near the LEFT edge of the opening, since telescope is moving towards right side during tracking. One should check/adjust dome position every 15-20min or every goto operation.

CAREFUL: The dome has great inertia, so it stops slowly (couple of seconds stopping time from full speed). It is equipped with a soft starter, so it accelerates even slower.

Data organization, todo list and log
On the operator computer "harmonia", images are stored in D:\Pictures\[YYYY-MM-DD]. Before observing, you should create a folder for tonight's observations. Have a text file called "todo-YYYY-MM-DD.txt", where should be a chronological list of planned objects in the following format:
[name]
[RA DE]
RA and DE should be formatted the following way (dashes represent numbers): --h--m--.--s --:--:--.--  That way, one can easily copy-paste coordinates to telescope control command.
Either comment whether an object was imaged, or write down names and coordinates of actually imaged objects into a separate file called "log-YYYY-MM-DD.txt".

Observation preparation rough checklist
Couple of hours before dusk, turn on aircondition and the big fan in the dome if needed (esp. summer). Check weather forecast (clouds and precipitation radar) and try to predict weather for tonight.
Turn on all electrical equipment and the dagor server. Start dagor sessions, tca API and prepare software environment for observing. See also: section Data organization, todo list and log.
If no flats and darks for tonight's planned imaging are done, image them. (See: section "Image calibration"). CAREFUL: DO NOT move the telescope anywhere from home position before telescope cover is removed!
In the dusk, open the dome and point the opening so that it corresponds with the tube, so that cool air can easily enter the tube. Telescope cover should be removed.
Perform preliminary focus adjustment. By this point, the sky should be dark enough so that stars are visible.
Perform rough manual focusing and autofocus.

Observation workflow
Slew to first undone object in your todo list. Check dome and move if necessary. TURN OFF THE LIGHTS!!!
Check focus and refocus if necessary.
Do plate solving if this is your first object tonight or you slewed significantly.
Configure batch imaging using Autosave option, perform imaging. If batch lasts longer than 15min, recheck dome position. Refocus if necessary.
Calibrate the images.
