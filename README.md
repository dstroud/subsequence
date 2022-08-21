# Subsequence
A chord sequencer and CV-harmonizer for Monome Teletype + Grid




Tutorial: Forthcoming at https://www.youtube.com/modularbeat 


## Introduction

Subsequence is a chord-based sequencer with a harmonizer function that is fed from an external CV source. With it, you can quickly create up to 4 chord progressions, sequence those into a composition using the Pattern Arranger, then send the chords out over i2c and CV 1-3 while CV 4 carries the output of the harmonizer. Even if you don't have polyphonic sound sources, the harmonizer is a fun way to explore adding chord-based structure to your Eurorack system.

IMPORTANT: Subsequence requires multiple scenes to operate! The "Launcher" scene is mandatory and must be used to load the scene variants which are tuned for the following i2c-capable modules:

- Just Friends
- Disting EX SD Multisample
- Disting EX Poly Wavetable Synth
- i2c2midi (requires [Teletype I2M BETA 2 firmware](https://github.com/attowatt/i2c2midi/tree/main/firmware/teletype_firmware))

If you don't have any i2c modules, any of the above variants will still output CV and triggers equally well.

Two versions of the Launcher are included depending on whether you have an i2c2midi (I2M) module or not:

- **subsequence_launcher_i2m.txt**    Use this if you have installed the I2M Beta 2 firmware

- **subsequence_launcher.txt**        Use this if you don’t have i2c2midi


## Installation

1. Download the launcher .txt file and rename it to the scene slot you wish to overwrite (e.g. tt10.txt). 
2. Install the variant scenes you want to use and rename the .txt using the following structure:

    | Scene          | Scene Slot | Ex. Filename | Launcher $ |
    |----------------|------------|--------------|------------|
    | Launcher       | x          | tt10.txt     | n/a        |
    | Just Friends   | x+1        | tt11.txt     | Script 1   |
    | EX Multisample | x+2        | tt12.txt     | Script 2   |
    | EX Wavetable   | x+3        | tt13.txt     | Script 3   |
    | i2c2midi       | x+4        | tt15.txt     | Script 4   |

    You’re welcome to exclude or re-order scenes as you see fit, just be sure to go into the Launcher .txt file and delete or move the contents of the associated script (1-4) into whatever script matches the scene’s new location. E.g., if you only want to install the i2c2midi variant, you can load the launcher as tt10.txt, the i2c2midi scene as tt11.txt, and then copy the contents of the Launcher script 4 into script 1. You may also edit the scene description and the last number in the "G.GBX…" line in the Launcher’s Init script to reflect the total number of variant scenes installed.


3. Once the scenes have been renamed, drop them on a thumb drive and plug into into Teletype. Make sure to delete those files off the thumb drive afterward so you don’t inadvertently overwrite a scene the next time you plug in the drive.
4. To load a variant scene, load the Launcher script with Grid connected, reference the list in the scene description, and press the corresponding button on Grid.


## Patching

- **IN** Send CV from an external sequencer, function generator, etc… into IN. A change of 1/12v (1 semitone) here results in change of 1 interval out on CV 4. Voltage is sampled when a trigger is recieved on IN 3.
- **IN 1** Send triggers into IN 1 to advance the chord sequence.
- **IN 2** To stop chord notes, send a trigger to IN 2 (useful if you have a sequencer/clock that outputs a trigger on stop such as Pamela’s New Workout).
- **IN 3** Send triggers to IN 3 to sample CV from IN. Make sure triggers come after the pitch (may need to add a bit of trigger delay if results are unpredictable).
- **IN 4** When follow mode is on, a trigger to IN 4 will reset the arranger to the first step. When follow mode is off, it will reset the current chord pattern to its first step. Usually multed to IN 2 to stop hanging chords and reset when the clock stops.
- **TR 1 OUT** Triggers whenever a new chord is played. Typical use-case is triggering an envelope for further processing chords with Eurorack effects.
- **TR 2 OUT** Triggers whenever the arranger moves to a new pattern. This can be used to trigger events elsewhere in your system corresponding with new song sections. Try S&H or a sequential switch.
- **TR 3 OUT** Primary trigger for the harmonizer output.
- **TR 4 OUT** Alternate trigger output for the harmonizer that adds rests when duplicate notes are detected within the active chord step/bar. "Transitional" duplicate notes that occur across steps/bars are allowed to pass for consistent behavior during chord changes.
- **Param Knob** Used to set velocity of the i2c chord output. Note that the readout on the Teletype dashboard is for the last-sent chord. Levels can get quite hot so turn this down if you’re hearing unwanted distortion.


## Grid UX

After choosing an option from the Launcher, you’ll find yourself in the Chord View.

### Chord View

- **Chord Sequence** Columns 1-14 are the Chord Sequence, which plays from top to bottom. The first 7 columns correspond to chords 1-7 in the N.CS op. Columns 8-14 are the same chords played one octave higher. Blank steps will hold the previously-played chord without re-triggering.
- **Sequence End/Loop** Column 15 sets the end/loop point for the Chord Sequence.
- **Current/Next Pattern** The top 4 rows of the 16th column indicate the active pattern (bright LED) as well as the next pattern queued in the Pattern Arranger (dimmer LED). Pressing one of the buttons will turn off Follow Mode, load the selected Pattern, and begin playing the pattern as soon as the active Chord Sequence step completes. Note that the next pattern will always be dimly-illuminated, even when Follow Mode is off. This indicates where the Pattern Arranger sequence will pick up when Follow Mode is enabled.
- **Follow Mode toggle** Row 6 on column 16 enables (bright) or disables (dim) Follow Mode. When Follow Mode is enabled, the active pattern is determined by the Pattern Arranger. When disabled, the current chord sequence will loop indefinitely.
- **Chord/Pattern Arranger switch** _The button in the bottom right corner switches between Chord View and Pattern Arranger View.

### Pattern Arranger View

- **Pattern Arranger** Rows 1-4 are the Pattern Arranger, where each row corresponds to patterns 1-4 in the Chord View. The Pattern Arranger progresses from left-to-right when Follow Mode is enabled. The Pattern Arranger loops to step 1 once it reaches the first blank column (i.e each step must be explicity set since there are no buttons to set loop length directly).
- **Follow Mode toggle** As in Chord View, row 6 on column 16 enables (bright) or disables (dim) Follow Mode. When Follow Mode is enabled, the active pattern is determined by the Pattern Arranger. When disabled, the current chord sequence will loop indefinitely.
- **Scale Selector** The 9 buttons in the bottom left corner select a scale:
	- 0-major
	- 1-natural-minor
	- 2-harmonic-minor
	- 3-melodic-minor
	- 4-dorian	
	- 5-phrygian
	- 6-lydian
	- 7-mixolydian
	- 8-locrian
 - **Harmonizer Intervals** The button at the bottom of column 11 toggles the harmonizer output on CV 4 between 3 or 4 intervals per chord (e.g. Cmaj vs. Cmaj7). The pitch range of the output sequence will be reduced when 4 intervals are enabled since it now takes an extra semitone to wrap to the next octave.
 - **Chord/Pattern Arranger switch**  The button in the bottom right corner switches back to the Chord View.


## Variant Notes

### Just Friends
- Just using 3 voices here although you can play with adding more voices by modifying X.
- Although chords will be held, JF's envelope will limit how long they play when Transient mode is engaged.
- Alternatively, Sustain mode may be used with an envelope triggered by Teletype's TR 1.	
	
### Disting EX SD Multisample
- Alternates between voices 1-3 and 4-6 so you can use EX's Release parameter to have some overlapping chords. Nice for acoustic samples with a long tail, strings, etc...
- There seem to be issues with some EX ops. Update EX if you're having issues with notes hanging (EX.AO didnt' work until I updated).

### Disting EX Poly Wavetable Synth
- Again, make sure EX firmware is updated. This was a quick mod and it wasn't even clear if the utilizes ops are supposed to work with this algo so YMMV. 

### i2c2midi
- Requires [Teletype I2M BETA 2 firmware](https://github.com/attowatt/i2c2midi/tree/main/firmware/teletype_firmware)
- Script $ 2 does a hacky job translating N.CS into the I2M.C op so you can then apply chord transformations. 
- While X can be changed to extend the number of intervals in the chord output, it can also throw off the Sync used for Every X in $ 3. You will also need to change the chord length for the i2m variant using I2M.C.L.
- Strumming in I2M mode doesn't always work as expected, likely because of the method of holding notes until the next note is played.

### Creating New Variants
- To make variants for other polyphonic i2c devices, you'll likely just need to change script $ 2.
- If you'd like to send the harmonizer output somewhere other than CV 4 (like to an i2c device), change script $ 3.
- Be sure to update the Launcher scene, a couple of scripts have been left available for this.


## Addendum


![Subsequence flow](https://raw.githubusercontent.com/dstroud/subsequence/60eaadfa3bcb4954c74ffa0008013ef1d305bf59/Subsequence.drawio.svg)


### Variables
- A Chord octave (0/1)
- B Prev chord
- C Current chord
- D Next pattern
- X Intervals in chord (3/4)
- Y Reset enabled
- Z Current pattern
- T Follow Mode T/F
- R Flip each $ 1
- PRT 7 Harmonizer intervals (3/4)
- P 63/PRT 8 Scale (0-8)
- PRT 9 Last sent velocity

### Work In Progress
- Currently there is no indication when a reset has occurred. Not ideal but there's not much room to improve this at the moment.
- A CV-only variant will likely be made at some point, allowing for more chord voicing using the param knob (see [MINIM](https://github.com/dstroud/minim)).
