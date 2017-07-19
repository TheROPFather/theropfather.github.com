What is Etterna?
==================


Welcome to Etterna Online! This guide will serve as a reference for information regarding StepMania and the much improved community built known as Etterna. Just want to play the game? Head [here](http://etternaonline.com/downloads/) to grab the installer for Windows and read the Etterna Setup section of the guide to learn how to customize your experience.

----------


StepMania Basics
-------------

StepMania is software that was originally created in 2001 to serve as a Dance Dance Revolution simulator, during its many years of development the software grew to support even more [game modes](#gamemodes) such as Pump It Up and Beatmania. The single most common game type however is the original DDR style, this game mode can be played on a [Dance Pad](#dancepad) or keyboard (Or anything you can map 4 keys too such as a Guitar Hero controller or Gamepad). Over the years the keyboard community for the game has grown extensively and broken barriers in relation to what [charts](#charts) are possible to clear; Etterna is a community developed version of StepMania 5 specifically made with keyboard players in mind. 



> **Note:**

> - StepMania is open source, it's code can be viewed [here](https://github.com/stepmania/stepmania).
> - Anything that can be done in StepMania 5 can also be achieved in Etterna, this guide will focus exclusively on Etterna however should you choose to use the vanilla StepMania game the guide should still apply.

------------------

### Getting Started with Etterna

Once you've downloaded Etterna follow the usual steps for installing the software (The installer is very clear). At this point you may wish to make a shortcut to the Etterna executable on your desktop, the executable can be found in the Program folder of your install. The Etterna folder structure should look like the following. 

![Etterna Folder Structure](http://i.imgur.com/ICRIoyH.png)

The most important folders too us are

> - Songs
> - NoteSkins
> - Save

At this point you could simply start the game and be able to play a very limited set of songs that are available by default with the install, most of the charts for these songs are geared towards more advanced players however the song Remembrance has a few easy charts. To get the most out of the game however you will want to add your own songs to the game I will discuss this next. 

---

####Adding Songs

First take a look at the [pack list](http://etternaonline.com/packs/) available on the site all of these packs are officially supported by the website meaning you will be able to track your scores should you choose to. Packs are simply a collection of songs and their respective chart that are curated or created entirely by one author. A song can have many charts, a chart is the part you play it contains the arrows, for example the song Panda may have an easy chart, hard chart and expert chart. You can learn more about charts in the [chart](#charts) section. 

The packs list can be quite intimidating however thanks to Etterna's difficulty calculator you can get a general idea of how hard the charts are; opening a pack shows the songs it contains and in the last column the difficulty of that file. As a complete beginner ratings of anywhere from 3-10 should be suitable, as you progress to intermediate you would want files in the 13-18 range. 

Once you've chose and downloaded a pack (I will use the Super MeatBoy Keyboard Pack for this example) you will unzip the file leaving a folder, this folder will be copied to your songs folder in its entirety, ensure that packs have the following structure ```Songs\packname\{songs}``` e.g. ```Songs\Super Meat Boy! Full-Keyboard Stepmania Pack\{songs}```. 

At this point you can start the game and your pack will be available to play, with that said let's move onto the basic Etterna interface.

---

####User Interface


Once you hit the Game Start option you will be presented with a page similar to the following. 

![Etterna Profile Select](http://i.imgur.com/MTw6YWs.png)

The only option that will be available at first is the Default Profile, to create your own profile head to Options -> Profiles -> New Profile. Here you can enter the name for your profile, once created if you hit enter on your newly created name you can choose to move it up the list so it will be the first option you see like ```theropfather``` in the above image. 

![User Interface](http://i.imgur.com/gwThMtT.png)

Now you have the game loaded I will explain a few of the important user interface elements. 

 1. This is information about your profile, mostly self explanatory however the number next to your profile name (17.40 for me) is your skill rating as a player, this gives you a general idea of where your skill level is. 
 2. This is the music wheel, here you can view your packs, open them and choose songs to play. The music wheel can be navigated using the <kbd>→</kbd> and <kbd>←</kbd>arrows on your keyboard or by using the mouse.
 3. This is the list of charts for a certain song, to change charts use the <kbd>↑</kbd> and <kbd>↓</kbd> arrows on your keyboard. 
 4. This is the calculated difficulty of the chart selected, if you choose another chart this number will change (Try it out) 
 5. This is the rate of the song, basically how fast the song is. As an example a 100BPM song on 1.2x Music would become 120BPM this feature allows you to increase or decrease the difficulty of a chart. Rates currently go from 0.7 to 3.0x.
 6. This is the judge difficulty, how precise the timings are (how accurate you have to be) I would strongly advise never going below 4 since the timing windows become to large and your scores will generally not be considered to be valid by the community. 

The last thing you need to know if the keys for your arrows.
By default they are set to <kbd>z</kbd><kbd>x</kbd><kbd>,</kbd><kbd>.</kbd> and correspond to <kbd>←</kbd><kbd>↓</kbd><kbd>↑</kbd><kbd>→</kbd>

Thanks to Etterna having sane default settings at this point you can start playing to get a feel for the game and have fun! If you want to customize your game play experience even further (which I recommend) see the next section.

----

#### Etterna Options Explained

When you hit enter to choose a song if you quickly hit the enter key again you will be taken to the options menu, this is where you can customize your gameplay experience I will give a brief description of the most important options. 

![Options](http://i.imgur.com/cb0V7ja.png)

 - Speed: This is the speed that the arrows will scroll. The default option is C400 this is a good starting point as a beginner. You can increase or decrease this to your liking. At the end notice the words Xmod, Cmod, Mmod - these affect the way the arrows speed is shown.
	 -  Cmod stands for constant mod and is the **most used by far** this keeps the speed of the arrows constant regardless of BPM changes in the chart.
	 - Xmod will change the speed of the arrows based on the songs BPM this is not a commonly used mod however it certainly has its place and can make some charts very interesting.
	 -  Mmod simply chooses an Xmod value for you based on a speed value you provide, the same as Xmod this is not used very often but does have its place.
 - Rate: This allows you to choose the speed of the song as discussed above a 1.2x rate will make a 100BPM song become 120BPM.
 - Note Skins: This allows you to choose a note skin that you have installed, for more information on Note skins [click here.](#noteskins)
 - Receptor Size: This will change the overall size of the arrows and the receptors.
 - Scroll: Most commonly either nothing is selected here meaning that arrows scroll up towards the top of your screen like DDR or reverse which will make them scroll to the bottom. Which you choose is entirely preference it has no effect on your ability to progress; some of the best players in the world use standard and some use reverse.

![Options 2](http://i.imgur.com/P1Is0lZ.png)

- Mines: This option allows you to turn on or off mines in charts, by default they are on and most people would recommend you keep it that way some consider playing with mines off to be "cheating" - however do whatever is fun for you, but at a competitive level mines on is required.
- Tracker Goal: This is the goal you wish to achieve, by default this is 93% which is a grade AA you can change this if you want, all this does is show a small tracker on top of the play field to show you how close or far you are from your target. 
- Judge Counter: This enables a table to the left of the play field letting you see how many Marvelous, Perfect, Great, Good, Bad and Misses you get. There is also NG which is increased when you let go of a hold note too early. 
- Error Bar: This will appear just below your Tracker Goal if you have it enabled, it is a small bar that when a note is hit shows how accurate you were. If you hit too early a small glyph will appear to the left of the bar, too late and it will appear to the right - this can be helpful in correcting your timing in a long stream section for example.

![Options 3](http://i.imgur.com/KLOezpC.png)

- Screen Filter: This will set the opacity of the play field, setting it to 1.0 sets the play field to have a solid background, this is again preference but 1.0 helps with not getting distracted by background artwork or video.
- NPS Display: These two options show how many notes per second you are hitting the first option just shows the count and the graph shows a nice visual of your notes per second.
- Background Type: Charts can have background videos, this can add to the enjoyment of the chart but can also serve as a distraction to disable them simply choose Static Background. 
- Life Difficulty: This option will determine how fast your health drains the default is the best option. 
- Judge Difficulty: Here you can set the judge difficulty, however as mentioned above it is **highly recommended** that you never go below 4. You may wish to have a harder judge and in that case you can select 5 or above. 

These are the most important options in the Player Options screen these allow you to fully customize your game play experience to your liking, play about with things and see what they do - for example dizzy and drunk can be very entertaining. 