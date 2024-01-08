#nocturne #outpost4
Backlinked: [[Nocturne Technical & Design Critique Homepage]]
## Scripts and important variables

This list contains a list of scripts and what they do.

There are many more scripts in the game than are listed here, but most of those are basic...
these are the ones which are complex and need explanation.

To play around with a script during runtime, try searching for it in the search box on the hierarchy
window, then selecting the object. You can now change it’s values during runtime with the
inspector panel. Be careful not to play with variables outside of runtime

These objects will prove particularly useful:

- Instrument, which contains the Nocturne.cs script and draws the keyboard to the
    screen. You can find it in the hierarchy under InstrumentObjects > InstumentMove > Instrument
- Score: You can find this inside the instrument object, and it draws notes to the
    screen to guide the player while playing in songbook mode, the beat saber-like
    mode of play.
- LeftSphere: this object can be found under OVRCameraRig / TrackingSpace /
    LeftHandAnchor / Left Controller Anchor / LeftSphere. It contains
    PressObjectScript.cs, which is the script which presses the keyboard down and
    synthesizes the sound. You can find RightSphere the same way, for the right hand
    instead of the left.

## MenuTransition.cs

Nocturne only needs 1 single scene to work. Everything is in this scene and no additional
scenes need to load. This decreases loading time and makes it easier to include smooth
transitions in the app, but it also makes things a little more complicated. The script
menutransition.cs contains a variable called state. If state = menuState.Menu, the main menu
will be displayed; if state = Freeplay, then the instrument will be displayed.

MenuTransition.cs also controls the shader on a sphere which surrounds the player. This sphere
is white when the player is in the menu, and transparent when they are not.


This whole script might be obsolete by the time you’re finished.
## Nocturne.cs

Nocturne.cs draws the instrument to the screen, instantiating the prefabs and making sure of
where they go. It includes code for placing the keys, detecting when they are being depressed
by the drumsticks, and making the keys light up with the right colors. You probably won’t need to
mess with this script at all, but it’s important to know what it does. You can find the Keyboard
object in the editor, which contains this script, and try changing the variables to see what they
do.

- spiralShape: This variable determines whether the spiral is a keyboard or a spiral.
- NoteType: We’ve gone through several iterations of what the notes should look like
    on the keyboard, and this variable lets you choose between them.
- bool blackandwhite: are the keys colorful or piano colored?

### Score.cs

Score.cs draws overlays of notes that move towards the player like in beatsaber.

Score uses a lot of the static functions within Nocturne.cs to determine where the notes go.

- float scale: this float determines whether the notes are long and approach quickly,
    or short and approach slowly. It simply scales the musical notes up and down.

### pressObjectScript.cs

pressObjectScript.cs is the script for the wands/drumsticks you use to press the keys down. This
is the script that contains the code which communicates with the synthesizer to generate the
sound. It also has several variables which perform important functions such as shifting the
octaves of the sound up and down, autotuning the pitch by snapping it to notes, and so on.


- float vibratoMaxDepth: controls the amplitude of the vibrato when at maximum. In
    practice this means if the hand trigger is squeezed in all the way then this will be
    the vibrato depth.
- float vibratoFrequency: this is the minimum freq of the vibrato. A value of 4 means
    4 hz.
- float vibratoFreqScale: this is the amount the vibrato frequency will be increased
    as the intensity increases. If it’s set to zero, the vibrato will have a uniform
    frequency, but with a positive value this can create a less mechanical feel.


# UI Scripts

## Knob.cs

Knob.cs is the script that makes the synth knobs work.
# I/O scripts

## ProjectMenu.cs

This script simply finds all the subdirectories in the /projects directory and lists them by loading a
prefab for each one, which contains the projectItem script.

## ProjectItem.cs

This script is attached to the prefab which displays the name of each project in the list. When the
prefab clicked the OpenProject() function is called which calls the openProject function on the
ProjectWindow.cs script.

## ShapesSequencer.cs

The shapes sequencer draws a grid which shows the note sequence. The user can click and
drag on the grid to create drum loops and so on.

## ProjectWindow.cs

The project window contains the scripts which loads all the audio tracks and displays them in the
interface.

- openProject(string Path, string Name): when this function is called, the app looks
    into the application persistent data directory, and finds all the files in the path and
    loads all .mid, .wav and .mp4 files into the scene. .mid files are loaded as midi
    tracks, .wav files are loaded as sound tracks, .mp4 files are loaded as videos.

## AudioTrack.cs

This script is attached to the prefab for audio tracks and loads a .wav audio file and draws it to a
texture to display the waveform.


```
I/O scripts
```
## NoteTrack.cs

This script is attached to the prefab for note tracks and loads a .mid music file and draws it to a
texture to display the notes.

## OutputAudioRecorder.cs

This script records the audio output from the instrument into a wav file. It contains a bunch of
functions for starting recordings and stopping them, and once a recording is stopped it’s saved
in the projects folder.

## playpause.cs

This script has functions which are triggered when the play/pause buttons on the navigator are
pushed. It also has code for drawing the time to the screen.


