# ndx-speech Extension for NWB:N

This in a NeuroData Extension (NDX) for Neurodata Without Borders: Neurophysiology (NWB:N) for transcriptions of natural
speech. It consists of a single neurodata_type, `Transcription`, which is an `NWBDataInterface` that contains 
`TimeIntervals` objects labelled "sentences", "words", "syllabels", "phonemes", and "phoneme features". Each of these
`TimeIntervals` objects is optional, and may be included or omitted based on the resolution of the transcription.
![schematic](media/ndx-speech-schematic.png)

## Installation

```bash
pip install git+https://github.com/bendichter/ndx-speech.git
```

## Usage

### python
```python
from ndx_speech import Transcription
from pynwb.epoch import TimeIntervals

# build it yourself:
sentences=TimeIntervals()
sentences.add_row(start_time=0.1, stop_time=0.5, label='I never said she stole my money')
sentences.add_row(start_time=1.2, stop_time=1.3, label='Little pigs are bad carpenters')

# or use pandas DataFrames:
import pandas as pd
sentences = TimeIntervals.from_dataframe(pd.read_csv('some_file.csv'))

# similar for words, syllables, phonemes, and phoneme features

transcription = Transcription(sentences=sentences)
```

### MATLAB

documentation under construction