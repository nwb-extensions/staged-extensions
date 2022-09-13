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

sampling_rate, samples = wavfile.read(filepath)

acoustic_waveform_series = AcousticWaveformSeries(
    name="acoustic_stimulus",
    data=samples,
    rate=sampling_rate,
    description="acoustic stimulus",
)

nwbfile = NWBFile(...)
# if a recording of behavior, add to acquisition
nwbfile.add_acquisition(acoustic_waveform_series)

# if a stimulus, add to stimulus
nwbfile.add_stimulus(acoustic_waveform_series)
```

### Visualization

Use `plot_sound` on its own outside nwbwidgets.
```python
from ndx_sound.widgets import plot_sound

plot_sound(nwbfile.stimulus["acoustic_stimulus"])
```

![](https://github.com/catalystneuro/ndx-sound/blob/main/ndx_sound_plot.png)

Use `acoustic_waveform_widget` to include an Audio element that plays the sound.

```python
from ndx_sound.widgets import acoustic_waveform_widget

acoustic_waveform_widget(nwb.stimulus["acoustic_stimulus"])
```

![](acoustic_waveform_widget.png)



You can also have the sound widget automatically loaded into `nwb2widget`.
```python
from ndx_sound import widgets
```
Then run `nwb2widget` as normal.
```python
from nwbwidgets import nwb2widget

nwb2widget(nwbfile)
```



---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
