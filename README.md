# Subsequence
A chord sequencer and CV-harmonizer for Monome Teletype + Grid




[Tutorial available on YouTube](https://youtu.be/BKh8ZLkSvl4)


## Introduction

Subsequence is a chord-based sequencer for Monome Teletype. It includes a handy harmonizer function that is fed from an external CV source. Even if you don't have polyphonic modules, the harmonizer is a fun way to explore adding chord-based structure to your Eurorack system.

IMPORTANT: Subsequence requires that you make some simple edits to a "Launcher" scene that defines which of the supported modules and their associated variant scenes you'll be using. You MUST download and configure the tt00_subsequence_launcher.txt file and edit it per the instructions in launcher_scripts.txt. It's not as scary as it sounds.

Here are the supported modules as of V.2:

- Just Friends
- Disting EX SD Multisample
- Disting EX Poly Wavetable Synth
- i2c2midi (requires [Teletype I2M BETA 2 or later firmware](https://github.com/attowatt/i2c2midi/releases))
- Plaits (requires alternate firmware to enable Teletype chords)
- No CV-specific variant exists YET but all versions except for Plaits output chord triads and the harmonizer output over CV.


## Installation and Launching Scenes

1. Download the tt00_subsequence_launcher.txt file and rename it to the scene slot you wish to overwrite (e.g. tt00.txt).
2. Download the variant scenes you want to use and rename the files using the following structure:

    | Scene          | Scene Slot | Ex. Filename | Launcher $ |
    |----------------|------------|--------------|------------|
    | Launcher       | x          | tt00.txt     | n/a        |
    | Just Friends   | x+1        | tt01.txt     | Script #1  |
    | EX Multisample | x+2        | tt02.txt     | Script #2  |
    | EX Wavetable   | x+3        | tt03.txt     | Script #3  |
    | i2c2midi       | x+4        | tt05.txt     | Script #4  |
    | Plaits         | x+5        | tt06.txt     | Script #5  |
    
3. Edit tt00_subsequence_launcher.txt per the instructions in launcher_scripts.txt
4. Drop the scenes on a thumb drive and plug into into Teletype. Make sure to delete those files off the thumb drive afterward so you don’t inadvertently overwrite a scene the next time you plug in the drive.
4. To load a variant scene, load the Launcher script with Grid connected, reference the list in the scene description, and press the corresponding button on Grid.


## Patching

- **IN** Send CV from an external sequencer, function generator, etc… into IN. A change of 1/12v (1 semitone) here results in change of 1 interval (based on the underlying chord) sent out on CV 4. Voltage is sampled when a trigger is recieved on IN 3.
- **IN 1** Send triggers into IN 1 to advance the chord sequence.
- **IN 2** To stop chord notes, send a trigger to IN 2 (useful if you have a sequencer/clock that outputs a trigger on stop such as Pamela’s New Workout).
- **IN 3** Send triggers to IN 3 to sample CV from IN. Make sure triggers come after the pitch (may need to add a bit of trigger delay if results are unpredictable).
- **IN 4** When follow mode is on, a trigger to IN 4 will reset the arranger to the first step. When follow mode is off, it will reset the current chord pattern to its first step. Usually multed to IN 2 to stop hanging chords and reset when the clock stops.
- **TR 1 OUT** Triggers whenever a new chord is played. Typical use-case is triggering an envelope for further processing chords with Eurorack effects.
- **TR 2 OUT** Triggers whenever the arranger moves to a new pattern. This can be used to trigger events elsewhere in your system corresponding with new song sections. Try S&H or a sequential switch.
- **TR 3 OUT** Primary trigger for the harmonizer output.
- **TR 4 OUT** Alternate trigger output for the harmonizer that adds rests when duplicate notes are detected within the active chord step/bar.
"Transitional" duplicate notes that occur across steps/bars are allowed to pass for consistent behavior during chord changes.
- **CV 1** Outputs root note in all variants
- **CV 2** Outputs second note in chord except for Plaits variant in which case this outputs CV for the Harmonics input on Plaits.
- **CV 3** Outputs third note in chord except for Plaits variant in which case this can be fed to Plaits' Level input to control the LPG via Param knob.
- **Param Knob** Used to set velocity of the i2c chord output. Levels can go from totally muted to very hot and this varies depending on your module setup and patching so check here first if something isn't working like you expect. On the Plaits variant, this is used to set the voltage sent to Level when the sequence is running (and mute the output when IN 2 is triggered).


## Grid Chord View
![subsequence_chord_view](https://user-images.githubusercontent.com/435570/185812855-325d6ab6-e8ec-49ed-b29f-088b203a5051.svg)

After choosing an option from the Launcher, you’ll find yourself in the Chord View.

- **Chord Sequence** Columns 1-14 are the Chord Sequence, which plays from top to bottom. The first 7 columns correspond to chords 1-7 in the N.CS op. Columns 8-14 are the same chords played one octave higher. Blank steps will hold the previously-played chord without re-triggering.
- **Sequence End/Loop** Column 15 sets the end/loop point for the Chord Sequence.
- **Current/Next Pattern** The top 4 rows of the 16th column indicate the active pattern (bright LED) as well as the next pattern queued in the Pattern Arranger (dimmer LED). Pressing one of the buttons will turn off Follow Mode, load the selected Pattern, and begin playing the pattern as soon as the active Chord Sequence step completes. Note that the next pattern will always be dimly-illuminated, even when Follow Mode is off. This indicates where the Pattern Arranger sequence will pick up when Follow Mode is enabled.
- **Follow Mode toggle** Row 6 on column 16 enables (bright) or disables (dim) Follow Mode. When Follow Mode is enabled, the active pattern is determined by the Pattern Arranger. When disabled, the current chord sequence will loop indefinitely.
- **Chord/Pattern Arranger switch** _The button in the bottom right corner switches between Chord View and Pattern Arranger View.

## Grid Pattern Arranger View
![subsequence_arranger_view](https://user-images.githubusercontent.com/435570/187570196-07e7dc44-d02e-49be-8c25-026991be8f5e.svg)

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
	- 7-locrian (should be mixolydian, see https://github.com/monome/teletype/issues/289)
	- 8-mixolydian (should be locrian, same issue as above)
- **Chord Mode** The button at the bottom of column 11 toggles between triads (off) and 7th chord (on).
- **Harmonizer Mode** The button at the bottom of column 12 toggles the harmonizer output on CV 4 between triad (off) and 7th chord (on) quantization. The pitch range of the output sequence will be reduced when the button is illuminated since it now takes an extra semitone to get to the next octave.
- **Chord/Pattern Arranger switch**  The button in the bottom right corner switches back to the Chord View.


## Variant Notes

### Just Friends
- The volume range gets extremely loud in case you want to use the independent outs on JF. Turn down the param knob on Teletype to around 9 o'clock if using Mix out.
- Although chords will be held, JF's envelope will limit how long they play when Transient mode is engaged.
- Alternatively, Sustain mode may be used with an envelope triggered by Teletype's TR 1.	
	
### Disting EX SD Multisample
- Alternates between voices 1-4 and 5-8 so you can use EX's Release parameter to have some overlapping chords. This is nice for acoustic samples with a long tail, strings, etc...
- Make sure to update Dising EX's firmware if you're having issues with notes hanging.

### Disting EX Poly Wavetable Synth
- Again, make sure EX firmware is updated. This was a quick mod and it wasn't even clear if the utilizes ops are supposed to work with this algo so YMMV. 

### i2c2midi
- Requires [Teletype I2M BETA 2 or later firmware](https://github.com/attowatt/i2c2midi/releases)
- Chords are packaged using the I2M.C op so you can use i2c2midi's extremely cool chord transformation ops.

### Plaits
- Requires installation of one of the custom Plaits firmwares listed in the source. The _extended file contains 7th chords while the _triad file only contains the first 3 notes and adds the root note one octave up.
- Due to a bug in the Teletype source code, the order of the last two scales is different from what is listed in the Teletype manual. They are locrian and mixolydian, respectively. This will require updating the lookup values in PN 2 once the issue has been resolved.
- Send CV 1 out to Plaits' V/Oct, CV 2 to HARMO, and CV 3 to Level. Use the param knob to set the level and amount of LPG/LPFA.

### Creating New Variants
- To make variants for other polyphonic i2c devices, you'll likely just need to change script $ 2 which handles the chord bits.
- If you'd like to send the harmonizer output somewhere other than CV 4 (like to an i2c device), change script $ 3.


## Addendum


![Subsequence flow](https://raw.githubusercontent.com/dstroud/subsequence/60eaadfa3bcb4954c74ffa0008013ef1d305bf59/Subsequence.drawio.svg)


### Variables
- A Chord octave (0/1)
- B Prev chord
- C Current chord
- D Next pattern
- X/P 61 Notes in chord (-1)
- Y Reset enabled
- Z Current pattern
- T Follow Mode T/F
- R Flip each $ 1
- P 62 Notes in harmonizer (-1)
- P 63/PRT 8 Scale (0-8)

### Work In Progress
- A bug in the Teletype code may result in unexpected results in the last two scales. See https://github.com/monome/teletype/issues/289
- Currently there is no indication when a reset has occurred. Not ideal but there's not much room to improve this at the moment.
- A CV-only variant will likely be made at some point, allowing for more chord voicing using the param knob (see [MINIM](https://github.com/dstroud/minim)).
