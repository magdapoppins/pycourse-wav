# SnakeChrmr - Dabbles in music
Exploration in how to use music in python with jupyter. 

### Tools 
For dealing with the `.wav` file format, you need to import `wave`. To be able to open a `.wav` file for reading and writing, one can use the `scipy.io.wav` modules `read()` function. This version of Jupyter Notebook uses Python 3.6. For plotting the data, `matplotlib.pyplot` is used. 

### What is a .wav file?
The WAV (Waveform Audio File Format) file format is a Microsoft standard format for storing audio files on pcs. It is compatible with Windows, MacOS and Linux operating systems. Although WAV files can contain compressed audio, the most common audio format of these files is uncompressed audio in the linear pulse code manipulation (LPCM) format. The WAV format files are limited to a size of 4 GiB since they use a 32-bit unsigned int to record the file size header.

### And the RIFF format?
The basis of WAV is the RIFF (resource interchange file format), a generic file container format for storing data in tagged chunks. It is usually used to store multimedia such as audio and video. Like WAV, RIFF is also a brain child of Microsoft. 

### Some memos
Python has an inbuilt module called `wave`, which contains tools for working with WAV files. 
```python
import wave
w = wave.open('sounds.wav', 'r')
# Walk through the frames
for i in range(len(w.getframes())):
  frame = w.readframes(i)
```
The frames are byte strings with hex values. Stereo files will have four bytes and if it is mono, it will contain half the bytes (2). Each channel is 2 bytes long because the audio is 16 bits. The `getchannels()` function will determine if the file is mono or stereo.

```python
import wave
w = wave.open('beeps.wav', 'r')
for i in range(w.getnframes()):
    ### read 1 frame and the position will updated ###
    frame = w.readframes(1)

    all_zero = True
    for j in range(len(frame)):
        # check if amplitude is greater than 0
        if ord(frame[j]) > 0:
            all_zero = False
            break

    if all_zero:
        # perform your cut here
        print 'silence found at frame %s' % w.tell()
        print 'silence found at second %s' % (w.tell()/w..getframerate())
```

And:
```python
# based on : www.daniweb.com/code/snippet263775.html
import math
import wave
import struct

# Audio will contain a long list of samples (i.e. floating point numbers describing the
# waveform).  If you were working with a very long sound you'd want to stream this to
# disk instead of buffering it all in memory list this.  But most sounds will fit in 
# memory.
audio = []
sample_rate = 44100.0


def append_silence(duration_milliseconds=500):
    """
    Adding silence is easy - we add zeros to the end of our array
    """
    num_samples = duration_milliseconds * (sample_rate / 1000.0)

    for x in range(int(num_samples)): 
        audio.append(0.0)

    return


def append_sinewave(
        freq=440.0, 
        duration_milliseconds=500, 
        volume=1.0):
    """
    The sine wave generated here is the standard beep.  If you want something
    more aggresive you could try a square or saw tooth waveform.   Though there
    are some rather complicated issues with making high quality square and
    sawtooth waves... which we won't address here :) 
    """ 

    global audio # using global variables isn't cool.

    num_samples = duration_milliseconds * (sample_rate / 1000.0)

    for x in range(int(num_samples)):
        audio.append(volume * math.sin(2 * math.pi * freq * ( x / sample_rate )))

    return


def save_wav(file_name):
    # Open up a wav file
    wav_file=wave.open(file_name,"w")

    # wav params
    nchannels = 1

    sampwidth = 2

    # 44100 is the industry standard sample rate - CD quality.  If you need to
    # save on file size you can adjust it downwards. The stanard for low quality
    # is 8000 or 8kHz.
    nframes = len(audio)
    comptype = "NONE"
    compname = "not compressed"
    wav_file.setparams((nchannels, sampwidth, sample_rate, nframes, comptype, compname))

    # WAV files here are using short, 16 bit, signed integers for the 
    # sample size.  So we multiply the floating point data we have by 32767, the
    # maximum value for a short integer.  NOTE: It is theortically possible to
    # use the floating point -1.0 to 1.0 data directly in a WAV file but not
    # obvious how to do that using the wave module in python.
    for sample in audio:
        wav_file.writeframes(struct.pack('h', int( sample * 32767.0 )))

    wav_file.close()

    return
```
### Sound as data
![Sound wave shapes](http://www.indiana.edu/~emusic/etext/acoustics/acoustics_images/ch1_images/Fig27.gif)
The sine curve is a "standard beep" - more uneven sounds require more uneven wave forms. It is common to preform a Fourier analysis on audio data. J. B. Fourier determined that
1) all complex periodic waves may be expressed as the sum of a series of sinusoidal waves
2) that these waves are all harmonics of the fundamental and
3) that each harmonic has its own amplitude and phase (which we have not discussed yet)



