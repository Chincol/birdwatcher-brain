# Birdwatcher

Bird identification deep neural network using SqueezeNet architecture with simple bypass connections.

## Data

The data used in this model is scraped from various sources, mainly from http://xeno-canto.org.

### Preprocessing

The data is used as is, meaning no noise reduction of other filtering is
applied. Only some of the silent sections of the files are removed before
cutting the pieces in to 3sec mono audio files.

The splitting is applied with a sliding window of 1.5sec steps.
This way more audio samples are generated from each audio file.

The files are then written to a `TFRecord`-file. This way we minimize
the amount of readable files and minimize the amount of IO operations.

After this the samples are ready for use.

### Feeding data to the network

the 3sec files are then fed through a Short Time Fourier Transform (STFT)
to generate a spectral representation of the audio, that is suitable for
the SqueezeNet.

The data is read by a `TFRecordDataset` iterator from the `TFRecord`-file.
This allows nice API for multithreaded preloading and transformation of
the audio files. The code can be found in
[`generators.py`](birdwatcher/generators.py)

At first the network had own STFT-implementation, but due to the freezing
and inference optimization the preloading graph could not be used after
the optimization. This is why the project now uses
[kapre](https://github.com/keunwoochoi/kapre).

### The network

The network implementation can be found in [`main.py`](main.py).
It's a basic SqueezeNet that takes in the 3sec audiofiles and transforms
the files with STFT and injects some additive noise during training (so
that the train examples vary a little always between even epochs).

## Results

The current model has been trained 
