# ndx-dbs Extension for NWB

This extension is developed to extend NWB data standards to incorporate required (meta)data for DBS experiments. `DBSGroup`, the main neurodata-type in this extension, in fact extends the `LabMetaData` which itself extends the NWBContainer base type and incorporates data types of `DBSMeta`(as an extension of LabMetaData), `DBSSubject`(as an extension of LabMetaData) and `DBSDevice`(as an extension of Device) which itself includes `DBSElectrodes`(as an extension of DynamicTable). Instances of these data types are interlinked to each other to account for the comprehensiveness of all the required meta(data) in a general experiment including DBS.

<div align="center">
<img src="https://github.com/Hamidreza-Alimohammadi/ndx-dbs/assets/63550467/63a919ff-d564-49de-98e7-4893bfc3e43f" width="1000">
</div>

## Installation
Can be installed directly from PyPI:
```
pip install ndx-dbs
```
or simply clone the repo and navigate to the root directory, then:
```
pip install .
```
## Test
A roundTrip test is runnable through ```pytest``` from the root. The test script can be found here:
```
\src\pynwb\tests
```
## An example use-case
The following is an example use case of ```ndx-dbs``` with explanatory comments. First, we build up an ```nwb_file``` and define an endpoint recording device:
```python
from datetime import datetime
from uuid import uuid4
from dateutil.tz import tzlocal
from pynwb import NWBHDF5IO, NWBFile

from ndx_dbs import (
    DBSDevice,
    DBSElectrodes,
    DBSMeta,
    DBSSubject,
    DBSGroup
)

nwb_file = NWBFile(
    session_description='DBS mock session',
    identifier=str(uuid4()),
    session_start_time=datetime.now(tzlocal()),
    experimenter='experimenter',
    lab='ChiWangLab',
    institution='UKW',
    experiment_description='',
    session_id='',
)

# define an endpoint main recording device
main_device = nwb_file.create_device(
    name='endpoint_recording_device',
    description='description_of_the_ERD',  # ERD: Endpoint recording device
    manufacturer='manufacturer_of_the_ERD'
)
```
Then, we define an instance of `DBSElectrodes` to represent the meta-data on the recording electrodes:
```python
'''
creating an DBS electrodes table
as a DynamicTable
'''
dbs_electrodes_table = DBSElectrodes(
    description='descriptive meta-data on DBS stimulus electrodes'
)

# add electrodes
dbs_electrodes_table.add_row(
    el_id='el_0',
    polarity='negative electrode (stimulation electrode, cathode)',
    impedance='0.8 MOhm',
    length='X cm',
    tip='tip surface ~ XX micrometer sq',
    material='platinum/iridium',
    location='STN',
    comment='none',
)
dbs_electrodes_table.add_row(
    el_id='el_1',
    polarity='positive electrode (reference electrode, anode)',
    impedance='1 MOhm',
    length='Y cm',
    tip='tip surface ~ YY micrometer sq',
    material='platinum/iridium',
    location='scalp surface',
    comment='distance D from el_0',
)
# adding the object of DynamicTable
nwb_file.add_acquisition(dbs_electrodes_table)  # storage point for DT
```
Now, we can define an instance of ```DBSDevice```:
```python
# define an DBSDevice-type device for ecg recording
dbs_device = DBSDevice(
    name='DBS_device',
    description='cable-bound multichannel systems stimulus generator; TypeSTG4004',
    manufacturer='MultichannelSystems, Reutlingen, Germany',
    synchronization='taken care of via ...',
    electrodes_group=dbs_electrodes_table,
    endpoint_recording_device=main_device
)
# adding the object of DBSDevice
nwb_file.add_device(dbs_device)
```
And also an instance of ```DBSMeta``` to store the meta-data for a DBS experiment:
```python
dbs_meta_group = DBSMeta(
    name='DBS_meta',
    stim_state='ON',
    stim_type='unipolar',
    stim_area='STN',
    stim_coordinates='–3.6mmAP, either–2.5mm (right) or 12.5mm(left)ML, and–7.7mmDV',
    pulse_shape='rectangular',
    pulse_width='60 micro-seconds',
    pulse_frequency='130 Hz',
    pulse_intensity='50 micro-Ampere',
    charge_balance='pulse symmetric; set to be theoretically zero',
)
# adding the object of DBSMeta
nwb_file.add_lab_meta_data(dbs_meta_group)  # storage point for custom LMD
```
Along with an instance of `DBSSubject`:
```python
dbs_subject_group = DBSSubject(
    name='DBS_subject',
    model='6-OHDA',
    controls='specific control group in this experiment',
    comment='any comments on this subject',
)
# adding the object of DBSSubject
nwb_file.add_lab_meta_data(dbs_subject_group)  # storage point for custom LMD
```
Now that we have all the required components, we define the main group for DBS to connect them all:
```python
dbs_main_group = DBSGroup(
    name='DBS_main_container',
    DBS_phase='first phase after implementation recovery',
    DBS_device=dbs_device,
    DBS_meta=dbs_meta_group,
    DBS_subject=dbs_subject_group,
    comment='any comments ...',
)
# adding the object of DBSSubject
nwb_file.add_lab_meta_data(dbs_main_group)  # storage point for custom LMD
```
Now, the `nwb_file` is ready to be written on the disk and read back. 
