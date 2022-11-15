![PyPI](https://img.shields.io/pypi/v/ndx-sound?color=blue)

# ndx-sound Extension for NWB

NWB extension for sounds.

## Installation

```shell
pip install ndx-sound
```

## Usage

## Python

### Add to an NWB file
```python
from pynwb import NWBFile
from scipy.io import wavfile

from ndx_sound import AcousticWaveformSeries

# The file path to the audio file
file_path = "audio_data.wav"

# Read the audio file to get the rate of the recording and the waveform
sampling_rate, samples = wavfile.read(file_path)

# Create an AcousticWaveformSeries object with a given name and description
acoustic_waveform_series = AcousticWaveformSeries(
    name="acoustic_stimulus",
    data=samples,
    rate=sampling_rate,
    description="acoustic stimulus",
)

# Create an NWBFile object where this AcousticWaveformSeries can be added to
nwbfile = NWBFile(
    session_description=...,
    identifier=...,
    session_start_time=...,
)

# If a recording of behavior, add to acquisition
nwbfile.add_acquisition(acoustic_waveform_series)

# If a stimulus, add to stimulus
nwbfile.add_stimulus(acoustic_waveform_series)
```

### Visualization

#### Static widgets
Use `plot_sound` to visualize the waveform series and the spectrogram.
For longer recordings, specify the `time_window` argument for the start and end time
of the recording to be shown.
```python
from ndx_sound.widgets import plot_sound

plot_sound(nwbfile.stimulus["acoustic_stimulus"])

# Show only from 5 to 15 seconds
plot_sound(nwbfile.stimulus["acoustic_stimulus"], time_window=(5, 15))
```

![](https://github.com/catalystneuro/ndx-sound/blob/main/ndx_sound_plot_timewindow.png)

Use `acoustic_waveform_widget` to include an Audio element that plays the sound.

```python
from ndx_sound.widgets import acoustic_waveform_widget

acoustic_waveform_widget(nwbfile.stimulus["acoustic_stimulus"], time_window=(5, 15))
```

![](https://github.com/catalystneuro/ndx-sound/blob/main/acoustic_waveform_widget_timewindow.png)

#### Interactive widgets
Use `AcousticWaveformWidget` to use a slider for interactively scrolling through the
recording and a button for changing the duration of the sound that is being shown.

```python
from ndx_sound.widgets import AcousticWaveformWidget

AcousticWaveformWidget(nwbfile.stimulus["acoustic_stimulus"])
```

![](https://github.com/catalystneuro/ndx-sound/blob/main/interactive_widget.png)

### nwbwidgets
Use `load_widgets` to load the interactive sound widget into `nwb2widget`.

```python
from ndx_sound.widgets import load_widgets
from nwbwidgets import nwb2widget

load_widgets()

nwb2widget(nwbfile)
```

![](https://github.com/catalystneuro/ndx-sound/blob/main/ndx_sound_in_nwbwidgets.png)

#### nwbwidgets and HDF5IO
When using `nwb2widget` with an NWB file that is read from disk, make sure to have
`load_widgets` imported within the same Jupyter cell where your data is being loaded.

```python
from pynwb import NWBHDF5IO
from ndx_sound.widgets import load_widgets
from nwbwidgets import nwb2widget

load_widgets()


io = NWBHDF5IO("audio.nwb", mode="r", load_namespaces=True)
nwbfile = io.read()
nwb2widget(nwbfile)
```

---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
