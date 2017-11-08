# Linn-Photobooth
I'm currently in the process of uploading the code and setting the links correctly, I'll do a Show HN when it's ready, please refrain from sharing before this thank you. 

LINN is your personal photobooth robot artist. 

A photobooth is a setup where guests take pictures of themselves.

LINN is a 2-axis gimbal controlled by a bluetooth ps3-controller which then stylize your photos with some deep neural style transter and offer single button instant print experience.


This project was realized in two weeks for the wedding of Thi-Kim & Pierre where it was a great success.

Without them this project would never have existed. You can contribute to their wedding gift here : Donate Paypal or Stripe or BTC

You can donate to Gist Noesis on our main site. You can also use the following affiliate link and buy our recommended products to help us improve the photobooth and develop our company. 

You can also help us grow by Starring our repository, sharing it on social media, or even better by building your own Linn.

It's free for non-commercial use. 

It exists in different variants depending on the budget you want to spend, your technical abilities, and the internet connectivity of the event location.


Here is the full setup which went live :

Picture of the full setup

The user experience is intended streamlined : the users put themselves with accessories in the frame of the camera, they see themselves on the TV so they can see what the photo looks like,
 then press X on the controller and it takes a photo instantly displayed. Then he either press Select two times to print the original pictures and 30 secs later he has the printed photo.
Alternatively he can use L2/R2 to change the art style, wait less than 6 secs then press Start two times to print the art picture. 
He can also press "Square" to have the photo taken in 5 secs (enough to hide the ps3 controller).
He can also use L1/R1 to see previous/next pictures.
They can also use the analogic joystick to move the camera.
If you enable manual auto exposure using v4l2-ctl -d /dev/video0 -c auto_exposure=1 then the up and down arrows will adjust exposure time. (Remember you can also adjust Gain usin v4l2-ctl to play on ISO sensitivity)



# Hardware : 
- PS3 controller
- Bluetooth dongle (IT WORKS BT150 is ubuntu16.04 compatible )
- Logitech C922
- Canon Selphy CP800
- TV Hd-ready (everything was developped for full HD so I had to adapt the ratio quickly when I discovered my grandparents tv was only Hd-ready )
- Sedatech PC Gamer Ultimate Intel i7-7700K 4x 4.20Ghz (max 4.5Ghz), Geforce GTX1080Ti 11Go, 32Gb RAM DDR4 3000Mhz, 3To HDD, 500Go SSD
- Second Geforce GTX1080Ti 11Go
- 2 axis DIY Gimbal (2 nema 17 stepper, RAMPS, PC power supply; ATX Power supply extension cable, 2020 aluminum profiles, ABS 3d printed parts, aluminium brackets, screws and nuts, 608 bearings, dyneema 150kg rope) 


# Software : 
(I'll add a guide soon to help build the software stack, I'm not sure to be able to build some docker images because of the bluetooth driver, but for the remote art I'll probably do it)

- Lubuntu 16.04 (for its ability to Undecorate windows)
- OpenCV (You need to install from source so you can properly show images : I've followed https://medium.com/@debugvn/installing-opencv-3-3-0-on-ubuntu-16-04-lts-7db376f93961 install with python3 support )
- Python3 ( I've used a virtualenv as you probably should, I've not yet listed the dependencies in requirements.txt (damn OpenCv) : numpy pyserial evdev )
- Sixad (driver for bluetooth ps3 controller installed from source)
- Arduino IDE (You need to add your current user to the dialout group to resolve serial permission issues) (Load the ino project, Select board Mega 2560 and serial /dev/ttyUSB0 and then upload)
- Fast-Neural-style (https://github.com/jcjohnson/fast-neural-style) (Remember to update torch : see https://github.com/jcjohnson/fast-neural-style/issues/137 ) (Called by remoteart.sh replace "beast" in script by username@yourartserverip and make sure you can ssh without password to this machine before using ssh-copy-id)


# The variants :

If you have a good internet connectivity you don't need a PC with GPUs as the scripts are customizable to dispatch jobs over ssh to process the art. 

- So you can use a cheaper PC and rent some amazon cloud for the night.
- The webcam C922 or the older more famous C920 are only full HD, if you have extra money you should probably invest in the 4K resolution logitech Brio on a USB3.0 port (and adapt the code a little to set the resolution to 4K). (I have not tested the linux compatibility). 
- The TV can be substituted by some video projector like PicoPix if you have a good control of the lighting conditions and are ready to sacrifice some display quality for ease of transportation.
- The 2-axis DIY Gimbal can be substituted either with the small tripod which come with the C922 or with an aluminium pole and some 3d printed pole adapter parts. (change UseDummySerial to True in code if you don't use RAMPS)
- The PS3 controller can be substituted by a bluetooth keyboard.
- The printer is not necessary, as print requests are saved to a file. 


# The attention to detail :

Although this was only a 2-week project, I took care of a lot of small details which contributed a lot to the success :
- Safe (Low 12V voltage), very stable due to large base, well balanced, quite light,  well delimited by cables and water bottle packs so that people don't trip into it or go under overhanging arm, not very strong so it can't harm fingers if people touch it, TV taped to the table so it won't accidently fall.
- Cheap and reusable
- Software end stop for the camera movement, so users can't break the camera or mingle the cables, (even though without the end stop camera collisions are not to severe and are probably fine)
- Analogic speed variation for the camera movement
- Robust handling of errors (by calling subprocesses) and exceptions, so the program should not crash even when controller is disconnected or run out of battery
- Pre-print confirmation and acknowledgement of print
- Instant response via multithreaded application
- Partial documentation centered on the essential, but with some of the guests knowing the extra features to help new users.
- Capturing extra frames before (yes circular buffer trick :) ) and after pictures were taken (so we can do later some super resolution if necessary to compensate for 2MP sensor). We can have some extra footage of pre picture preparation.
- Correct prioritization depending on current selected art and picture of the art stilization tasks to mask as much as possible the computation time.
- Processing in the background when inactive all the different styles so that they are all computed.
- Delete with confirmation (not a real delete but a move to deleted picture folder so people can erase picture either for private pictures)
- Reset button of the RAMPS pressable at any time without crashing the software if necessary
- You can use v4l2-ctl to manually adjust some additional camera settings, like auto focus, gain, auto exposure, sharpness, backlight compensation, zoom (if you opt for the Brio), 
- The positionning of the TV with respect to the camera. As people will natually look toward the TV, you should take care that the angle between TV and camera give some good photos.
- Lighting : avoid as much as possible light behind the guests, eventually use additional light panel if you have extra money.
- Portrait vs Paysage : Both modes have been implemented. We chose Paysage for the party so that more people could be on the picture. Portrait would be better for single person from shoes to hat pictures. (Orientation 0 90 180 270 in code)
Currently we can't switch easily between both modes (i.e. displaying portraits while taking Paysage) except by relaunching the program in the other mode
- The photos were saved on the HDD the art was processed on the SSD, so I've always had 2 copies of the pictures to prevent data loss (If you have internet access you can rsync your folder to a remote directory or cloud drive)
- Using the power supply fans to assure the air flow over the ramp board.
- Easily adjustable cable tension by sliding brackets and weights.
- We customized the neural styles by training our own styles. (~3 hours per style 

# Making it fun : 
- We had some background picture frames where people can put their heads through, it induced a lot of funny but lower quality pictures.
- LINN can dance, if you loosen the cables you can make the camera oscillate quite a lot as the aluminium structure is intended flexible. (Be careful about nuts and bolts remaining tight enough if you push it a lot vibrations may make them loose over time) 
- You can increase the speed of the movement, disable the software endstops (so you don't care about missing step at high speed)
- I didn't have time to implement some auto tracking features for the party but you have all the pieces necessary to do it from python (And I'll probably add them if I have some spare time).
- You can probably add some automatic camera movement, and suggest photos to guests.
- You can probably add some active damping. 
- You can probably add some laser pointer to the gimbal and do some baloon popping.


# What went wrong or could be improved :
- Amazon was late for paper/ink delivery, we receive the extra paper/ink only on the next day, so we only had ~ 110 photos which we run through quite fast.
- Printer worked great, guests even reloaded the paper themselves, but they didn't understood that you should change ink and paper simulaneously as implicitely suggested by the ink/paper combo packaging so you are guarantee to have a good quality print. 
- I had to change the ink (very easy on this printer), so not yet fully autonomous, We probably should display a message on screen when ink level are low.
- Cables didn't have enough tension, so there was a little too much oscillations when moving. (Remember to test in real conditions : (while drunk :) ) )
- The ATX power supply extension cable got damaged during the transport, and one contact was loose.
- I had to reset (press the arduino button) once the RAMPS board as one axis of the motor was not responding. (It may also be some software bug about the arduino timer looping around every 72 minutes), or some over heating issue of the driver, I'll have to troubleshoot)
- The partial documentation caused some people to miss out on the changing of the styles. It was more prevalent among people of the older generations who had never used a PS3 controller and for which the L1/R1 L2/R2 buttons are not natural.
- The lighting environment could have been improved.
- The driver makes a little noise which can't be heared during a noisy event but you can improve using TMC2100 stepper driver with 32x microstepping.
- We can easily add some rotary encoder to the gear design for closed loop control/movement recording. (Use pin change interrupts to handle up to 5 encoder per RAMPS board)



# What went great :
- LINN is physically impressive because it takes quite a volume.
- The wireless movement control is quite magical even among the young generations.
- It got some people to speak together and explain some commands to each other.
- The instant printing (for all the times people had troubles with their printers the experience of pressing a button and having the print start instantly is fantastic, the Selphy CP800 doing multiple ink-pass also contribute to the fun of it.
- The neural art makes some nice unusual visual effects.
- People discovered on their own how they could get a second examplary for their picture
- Overall it was great fun.



# Instructions to build the 2-axis DIY gimbal (I'll make a video if the project receive enough attention) :

All parts must be printed 100% infill.

Both axis are identical, their parts must be assembled in a specific order : 
They consist of :

 8mm diameter 4cm long threaded bolt, 1mm metallic spacer, short support, nut
You may add an extra 1mm metallic spacer if you wish
Then attach the support to the aluminium profile.

pulleymountspacer, two-half gears on their 608 bearings, one 608 bearing ,  nut, 

Then tighten the nut now. You can also use a lock nut instead for extra safety.
Then mount the long pulleyMount on the last bearing
Use the 2x 4mm diameter 2.5cm long conical head screw with matching nut to attach the gears to the pulleyMount by placing the nuts in the hexagonal holes

Then attach the long pulleyMount to the aluminum profile.

The support and pulleyMount must be printed in the correct orientation (i.e. 2cm height) to provide good strength
The two-half gears should be marked so that they are correctly aligned (It's very error prone), and gears should be pointed in the correct direction. 
(I designed it in two half parts so that in an attempt to reduce backlash by having the top wheel a little offset)


You can then print without support the supportstepper and piniongear (remember to adjust the diameter to the diameter of your stepper motor so that the fit is tight.


# The secrets of robot arms : Proper balance
The center of mass of a rotating part should be as near as possible to the axis of rotation. You can use extra bearing with 10mm t-slot nuts and screw to balance the arm.
If you equilibrate properly it shouldn't rotate in either direction due to its own weight. This means that even when you have no power the arm shouldn't move.
Start by equilibring the axis which support the camera, then when it's well balance move to the next axis. You can add a lot of axis and everything will be balanced.
When the arm is well balanced you don't need a lot of force to move it and vibrations will be minimal.
Additionally in a gimbal the optical axis should be aligned with the axis of rotations or parallax effects will appear (Which can then be used to get extra 3d information).


# The support : 
This can vary quite a lot.
The wider the base the more stability. At a minimum the base should be longer than the arm so that there is no overhang. 
LINN was 2 meter height and a cross of 2 meter and 2 times 1 meter. I mounted the ramps board in its board on the pole, added the power supply on top so that air flows secured in place by cable, tape and an equerre5
Without water bottle counter weight When pushed its tendency was rather to slide away rather than tilting.
Doubling the aluminum profile can increase rigidity a lot.


# The cable system : 
It's time for you to reveal your inner spider.
I didn't have to cut my rope at all.
Use equerre5 (again printed in 2cm height direction) to thread some cables to increase the rigidity of the structure. You can slide them to increase the tension. The aluminium profile should bend.
Then when you will add some counter weight on the extremities to reduce the bending it will increase the tension even more. If you have a musical hear you can even tune them in harmony.
If these sort of designs interest you, you will probably be interested by the tensegrity concept. 
You should construct your cable system so you can adjust the height of the robot arm quite easily and then you can easily tense the cable again by sliding some equerre5. The long support cross helped in this regard.


# Not yet included features : 
-Video : you can probably do it in a few lines using the unassigned "circle" button (recording while the button is pressed) and save frame like it's done for extra frames), remember to display message indicating that it's currently recording and to handle the case where controller is deconnected. Getting the audio and encoding and synchronisation will be more complex.
-Serving the photos and art to a webserver where you could vote for the best pictures or style
-Serving the photos live to twitter


# What's next : 
At Gist Noesis we have some robotics division, where we design and print low cost customizable robots safe for the home. 
We are aiming for safe, cheap, robust, reusable and customizable but sacrificing look, precision and repeatablity.
If LINN get enough attention and enough donations we may hire someone to improve it, provide ready to rent boxes for events, or sell kits.
LINN may also interest some video streamers especially those not yet famous one which can use it as a cheap cameraman.
LINN can be converted in a 3d scanner turn table.