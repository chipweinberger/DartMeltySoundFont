# DartMeltySoundFont

DartMeltySoundFont is a SoundFont synthesizer (i.e. '.sf2' player) written in pure Dart.

It is a port of MeltySynth (C#, MIT License) written by Nobuaki Tanaka, to Dart.

https://github.com/sinshu/meltysynth

## Dependencies

This package has no dependencies.

## Maintanence

This project was specifically designed to not require maintanence, in large part by not having any dependencies. Apart from breaking changes to the Dart language (rare), it should be solid code that works for decades. It will work on any Dart SDK sdk>=2.12 && sdk<3.0 indefinitely. 

This package was written against Dart SDK 2.16.1.

## Example

Synthesize a simple chord:

```
// Necessary Imports
import 'package:dart_melty_soundfont/dart_melty_soundfont.dart';
import 'package:flutter/services.dart' show rootBundle;

// Create the synthesizer.
ByteData bytes = await rootBundle.load('assets/akai_steinway.sf2');

Synthesizer synth = Synthesizer.loadByteData(bytes, 
    SynthesizerSettings(
        sampleRate: 44100, 
        blockSize: 64, 
        maximumPolyphony: 64, 
        enableReverbAndChorus: true,
    ));

// you might want to select the patchNumber & bankNumber
// of the instrument you want to play, if it's not 0.
//
// Look through synth.soundFont.presets[X].patchNumber & bankNumber
// for all valid instrument choices.
//
// Example for setting patchNumer:
//
// int patchNumber = synth.soundFont.presets[0].patchNumber 
// synth.processMidiMessage(
//  channel:0, 
//  command:0xC0, // 0xC0 = program changes
//  data1:patchNumber, 
//  data2:0
// );
//
// Example for setting bankNumber:
//
// int bankNumber = synth.soundFont.presets[0].bankNumber 
// synth.processMidiMessage(
//  channel:0, 
//  command:0xB0, // 0xB0 = control change
//  data1:0x00,   // 0x00 = bank select
//  data2:bankNumber,
// );


// Turn on some notes
synth.noteOn(channel: 0, key: 72, velocity: 120);
synth.noteOn(channel: 0, key: 76, velocity: 120);
synth.noteOn(channel: 0, key: 79, velocity: 120);
synth.noteOn(channel: 0, key: 82, velocity: 120);


// Render the waveform (3 seconds)
ArrayInt16 buf16 = ArrayInt16.zeros(numShorts: 44100 * 3);

synth.renderMonoInt16(buf16);
```

## Playing Sound

This library does not audibly make sound, it only generates the PCM waveform. 

To actually hear something, you need to pass the generated PCM waveform to your device's speakers. I recommend using [raw_sound](https://pub.dev/packages/raw_sound) or [Web Audio](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) for that task, depending on your platform.

See [raw_sound's PCM audio example](https://pub.dev/packages/raw_sound/example), for iOS and Android.

## Features

* No memory allocation in the rendering process.

* __Wave synthesis__
    - [x] SoundFont reader
    - [x] Waveform generator
    - [x] Envelope generator
    - [x] Low-pass filter
    - [x] Vibrato LFO
    - [x] Modulation LFO
* __MIDI message processing__
    - [x] Note on/off
    - [x] Bank selection
    - [x] Modulation
    - [x] Volume control
    - [x] Pan
    - [x] Expression
    - [x] Hold pedal
    - [x] Program change
    - [x] Pitch bend
    - [x] Tuning
* __Effects__
    - [x] Reverb
    - [x] Chorus
* __Other things__
    - [x] Loop extension support
    - [x] Performace optimization


## Todo

- MIDI file support. 

Feel free to port MIDI file support to Dart and make a pull request. 

Its usage would look like this when implemented:

```
// Create the synthesizer.
var sampleRate = 44100;
var synthesizer = new Synthesizer("TimGM6mb.sf2", sampleRate);

// Read the MIDI file.
var midiFile = MidiFile("flourish.mid");
var sequencer = MidiFileSequencer(synthesizer);
sequencer.play(midiFile, false);

// Render the waveform (3 seconds)
ArrayInt16 buf16 = ArrayInt16.zeros(numShorts: 44100 * 3);

sequencer.renderMonoInt16(buf16);
```


## License

DartMeltySoundFont is available under [the MIT license](LICENSE.txt).



## References

* __SoundFont&reg; Technical Specification__  
http://www.synthfont.com/SFSPEC21.PDF

* __Polyphone Soundfont Editor__  
Some of the test cases were generated with Polyphone.  
https://www.polyphone-soundfonts.com/

* __Freeverb by Jezar at Dreampoint__  
The implementation of the reverb effect is based on Freeverb.  
https://music.columbia.edu/pipermail/music-dsp/2001-October/045433.html