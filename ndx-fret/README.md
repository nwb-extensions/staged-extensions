# ndx-fret
[![PyPI version](https://badge.fury.io/py/ndx-fret.svg)](https://badge.fury.io/py/ndx-fret)

NWB extension for storing Fluorescence Resonance Energy Transfer (FRET) experimental data.
A collaboration with [Jaeger Lab](https://scholarblogs.emory.edu/jaegerlab/), [Emory University](https://www.emory.edu/home/index.html) and [The Kavli Foundation](https://www.kavlifoundation.org/).

<p align="center">
<img src="media/FRET_schematic.png" width="400">
</p>

### Python Installation
```bash
pip install ndx-fret
```

### Python Usage
```python
from pynwb import NWBFile, NWBHDF5IO
from pynwb.device import Device
from pynwb.ophys import OpticalChannel
from ndx_fret import FRET, FRETSeries

from datetime import datetime
import numpy as np

nwb = NWBFile('session_description', 'identifier', datetime.now().astimezone())

# Create and add device
device = Device(name='Device')
nwb.add_device(device)

# Create optical channels
opt_ch_d = OpticalChannel(
    name='optical_channel',
    description='optical_channel_description',
    emission_lambda=529.
)
opt_ch_a = OpticalChannel(
    name='optical_channel',
    description='optical_channel_description',
    emission_lambda=633.
)

# Create FRET
fs_d = FRETSeries(
    name='donor',
    fluorophore='mCitrine',
    optical_channel=opt_ch_d,
    device=device,
    description='description of donor series',
    data=np.random.randn(100, 10, 10),
    rate=200.,
)
fs_a = FRETSeries(
    name='acceptor',
    fluorophore='mKate2',
    optical_channel=opt_ch_a,
    device=device,
    description='description of acceptor series',
    data=np.random.randn(100, 10, 10),
    rate=200.,
)

fret = FRET(
    name='FRET',
    excitation_lambda=482.,
    donor=fs_d,
    acceptor=fs_a
)
nwb.add_acquisition(fret)

# Write nwb file
with NWBHDF5IO('test_fret.nwb', 'w') as io:
    io.write(nwb)
    print('NWB file written')

# Read nwb file and check its content
with NWBHDF5IO('test_fret.nwb', 'r', load_namespaces=True) as io:
    nwb = io.read()
    print(nwb)
```
