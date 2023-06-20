# ndx-acquisition-module

[![pipeline status](https://img.shields.io/gitlab/pipeline-status/fleischmann-lab/ndx/ndx-acquisition-module?branch=main&label=pipeline&style=flat-square)](https://gitlab.com/fleischmann-lab/ndx/ndx-acquisition-module/-/commits/main)
[![license](https://img.shields.io/gitlab/license/fleischmann-lab/ndx/ndx-acquisition-module?color=yellow&label=license&style=flat-square)](LICENSE.txt)


![python version](https://img.shields.io/pypi/pyversions/ndx-acquisition-module?style=flat-square)
[![release](https://img.shields.io/gitlab/v/release/fleischmann-lab/ndx/ndx-acquisition-module?label=release&sort=date&style=flat-square)](https://gitlab.com/fleischmann-lab/ndx/ndx-acquisition-module/-/releases)
[![pypi package](https://img.shields.io/pypi/v/ndx-acquisition-module?label=pypi%20package&style=flat-square&color=blue)](https://pypi.org/pypi/ndx-acquisition-module)
[![conda package](https://img.shields.io/conda/v/fleischmannlab/ndx-acquisition-module?color=green&style=flat-square)](https://anaconda.org/FleischmannLab/ndx-acquisition-module)

This extension is used to allow adding modules in `nwbfile.acquisition`, similarly to how `nwbfile.processing` allows adding modules.

More specifically, this allows creating a module that has `TimeSeries` and `DynamicTable` objects, then users can add this module.

This is in alpha development stages. Please use with discretion.

## Installation

You can install via `pip`:

```bash
pip install ndx-acquisition-module
```

Or `conda`:

```bash
conda install -c fleischmannlab ndx-acquisition-module
```

Or directly from the `git` repository:

```bash
pip install git+https://gitlab.com/fleischmann-lab/ndx/ndx-acquisition-module
```

## Usage

### Main usage

Here's a short example to create the module, add objects into it then add to acquisition

```python
from ndx_acquisition_module import AcquisitionModule

mod = AcquisitionModule(name="raw_mod", description="raw acq module")

# Add data objects to created AcquisitionModule
mod.add(time_series)  # add time series
mod.add(dynamic_table)  # add dynamic table

# Add AcquisitionModule to nwbfile.acquisition
nwbfile.add_acquisition(mod)
```

### Full example

Here's a full example that you can copy and paste in a script/notebook and run. A `test.nwb` file would be created.

<details><summary>Expand to see full example script</summary>

```python
from datetime import datetime

import numpy as np
from dateutil import tz
from hdmf.common import DynamicTable, VectorData
from ndx_acquisition_module import AcquisitionModule

from pynwb import NWBHDF5IO, NWBFile, TimeSeries

# Create an example NWBFile
nwbfile = NWBFile(
  session_description="test session description",
  identifier="unique_identifier",
  session_start_time=datetime(2012, 2, 21, tzinfo=tz.gettz("US/Pacific")),
)

# Create time series
ts = TimeSeries(
  name="choice_series",
  description="raw choice series",
  data=np.random.randint(4, size=100),
  timestamps=(np.arange(100).astype("float") + 2) / 30,
  unit="-",
)

# Create dynamic table
tbl = DynamicTable(
  name="lookup_table",
  description="lookup table for `choice_series`",
  columns=[
    VectorData(
      name="lookup_id", description="ID to look up", data=[0, 1, 2, 3]
    ),
    VectorData(
      name="lookup_name",
      description="name of ID",
      data=["water", "earth", "fire", "air"],
    ),
  ],
)

# Create AcquisitionModule to store these objects
mod = AcquisitionModule(name="raw_mod", description="raw acq module")

# Add data objects to created AcquisitionModule
mod.add(ts)  # add time series
mod.add(tbl)  # add dynamic table

# Add AcquisitionModule to nwbfile.acquisition
nwbfile.add_acquisition(mod)

# Write the file to disk
filename = "test.nwb"
with NWBHDF5IO(path=filename, mode="w") as io:
  io.write(nwbfile)

```

</details>


## API usage notes and limitations

### With package installed

Currently to use `mod.get(<object_name>)` or `mod[<object_name>]`, users would also need to install this package, for example with

```bash
pip install ndx-acquisition-module
```

And import, using `NWBHDF5IO(..., load_namespaces=True)` would not be enough.

```python
# new file completely
from pynwb import  NWBHDF5IO
from ndx_acquisition_module import AcquisitionModule
nwb = NWBHDF5IO('test.nwb', mode='r').read() # notice `load_namepsaces` is not needed

print(nwb.acquisition['raw_mod'])
```

which outputs:

```text
raw_mod ndx_acquisition_module.AcquisitionModule at 0x139742592581104
Fields:
  data_interfaces: {
    choice_series <class 'pynwb.base.TimeSeries'>,
    lookup_table <class 'hdmf.common.table.DynamicTable'>
  }
```

To access:

```python
nwb.acquisition['raw_mod']['lookup_table']
nwb.acquisition['raw_mod']['choice_series']
```

### Without package installed

Otherwise, if `ndx-acquisition-module` is not installed, accessing the inside objects have to be done based on types:

```python
# new file completely
from pynwb import  NWBHDF5IO
nwb = NWBHDF5IO('test.nwb', mode='r', load_namespaces=True).read() # notice `load_namepsaces` is NEEDED

print(nwb.acquisition['raw_mod'])
```

which outputs:

```text
raw_mod abc.AcquisitionModule at 0x140252603705728
Fields:
  description: raw acq module
  dynamic_tables: {
    lookup_table <class 'hdmf.common.table.DynamicTable'>
  }
  nwb_data_interfaces: {
    choice_series <class 'pynwb.base.TimeSeries'>
  }
```

To access:

```python
nwb.acquisition['raw_mod'].dynamic_tables['lookup_table']
nwb.acquisition['raw_mod'].nwb_data_interfaces['choice_series']
```

---

This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template)
