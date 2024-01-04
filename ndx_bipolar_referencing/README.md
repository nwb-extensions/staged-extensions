# ndx-bipolar-referencing Extension for NWB:N

Structure for storing the bipolar schema of a recording in an NWB file.

![schema schema](https://github.com/ben-dichter-consulting/ndx-bipolar-referencing/blob/master/docs/media/bipolar_reference_schema_schema.png?raw=true)

## python installation
```bash
$ pip install ndx-bipolar-referencing
```

## python usage

```python
from pynwb import NWBHDF5IO, NWBFile
from pynwb.file import DynamicTable, DynamicTableRegion
from datetime import datetime
from ndx_bipolar_referencing import EcephysExt
from pynwb.ecephys import ElectricalSeries

import numpy as np

nwbfile = NWBFile('description', 'id', datetime.now().astimezone())

device = nwbfile.create_device('device_name')

electrode_group = nwbfile.create_electrode_group('electrode_group',
                                                 'desc', 'loc', device=device)

for _ in range(20):
    nwbfile.add_electrode(np.nan, np.nan, np.nan, np.nan, 'loc', 'filt',
                          electrode_group)

positive_electrodes = DynamicTableRegion('positive_electrodes',
                                         np.arange(0, 20, 2),
                                         'desc',
                                         nwbfile.electrodes)
negative_electrodes = DynamicTableRegion('negative_electrodes',
                                         np.arange(1, 20, 2),
                                         'desc',
                                         nwbfile.electrodes)

bipolar_reference_scheme = DynamicTable(name='bipolar_reference_scheme',
                                        description='desc',
                                        columns=[positive_electrodes,
                                                 negative_electrodes])

ecephys_ext = EcephysExt(bipolar_reference_scheme=bipolar_reference_scheme)
nwbfile.add_lab_meta_data(ecephys_ext)

bipolar_scheme = DynamicTableRegion(
    name='electrodes',
    data=np.arange(0, 10),
    description='desc',
    table=nwbfile.lab_meta_data['extracellular_electrophysiology_extensions'].bipolar_reference_scheme)

ec_series = ElectricalSeries(name='test_ec_series',
                             description='desc',
                             data=np.random.rand(100, 10),
                             rate=1000.,
                             electrodes=bipolar_scheme)

nwbfile.add_acquisition(ec_series)

with NWBHDF5IO('test_nwb.nwb', 'w') as io:
    io.write(nwbfile)

with NWBHDF5IO('test_nwb.nwb', 'r', load_namespaces=True) as io:
    nwbfile = io.read()
    print(nwbfile.acquisition['test_ec_series'].electrodes.table['positive_electrodes'].data)
```
