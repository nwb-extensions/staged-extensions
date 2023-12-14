# ndx-franklab-novela Extension for NWB

# About
ndx-franklab-novela is a python package containing NWB custom extensions for Loren Frank's Lab.

# How to install

Add ndx-franklab-novela to your conda environment

`pip install git+git://github.com/LorenFrankLab/ndx-franklab-novela`

The original published extension maintained by NovelaNeuro can be installed using:

`conda install -c conda-forge -c novelakrk ndx-franklab-novela`


# How to install

Add ndx-franklab-novela to your conda environment<br>
```pip install git+git://github.com/LorenFrankLab/ndx-franklab-novela```

The original published extension maintained by NovelaNeuro can be installed using:
```conda install -c conda-forge -c novelakrk ndx-franklab-novela```


# Extensions

## AssociatedFiles
Representation of associated files in NWB.

**Attributes:**
- **description**  `string`: description of associated file
- **content**  `string`: content of associated file
- **task_epochs**  `string`: id of epochs with task that is descripted by associated files

## HeaderDevice
Representation of HeaderDevice in NWB.

**Attributes:**
- **headstage_serial**  `string`: headstage_serial from header global configuration
- **headstage_smart_ref_on**  `string`: headstage_smart_ref_on from header global configuration
- **realtime_mode**  `string`: realtime_mode from header global configuration
- **headstage_auto_settle_on**  `string`: headstage_auto_settle_on from header global configuration
- **timestamp_at_creation**  `string`: timestamp_at_creation from header global configuration
- **controller_firmware_version**  `string`: conntroller_firmware_version from header global configuration
- **controller_serial**  `string`: conntroller_serial from header global configuration
- **save_displayed_chan_only**  `string`: save_displayed_chan_only from header global configuration
- **headstage_firmware_version**  `string`: headstage_firmware_version from header global configuration
- **qt_version**  `string`: qt_version from header global configuration
- **compile_date**  `string`: compile_date from header global configuration
- **compile_time**  `string`: compile_time from header global configuration
- **file_prefix**  `string`: file_prefix from header global configuration
- **headstage_gyro_sensor_on**  `string`: headstage_gyro_sensor_on from header global configuration
- **headstage_mag_sensor_on**  `string`: headstage_mag_sensor_on from header global configuration
- **trodes_version**  `string`: trodes_version from header global configuration
- **headstage_accel_sensor_on**  `string`: headstage_accel_sensor_on from header global configuration
- **commit_head**  `string`: commit_head from header global configuration
- **system_time_at_creation**  `string`: system_time_at_creation from header global configuration
- **file_path**  `string`: file_path from header global configuration

## ShanksElectrode
Representation of electrodes of a shank in NWB.

**Attributes:**
- **name**  `string`: name of the shank
- **rel_x**  `float`: the rel_x value of this electrode
- **rel_y**  `float`: the rel_y value of this electrode
- **rel_z**  `float`: the rel_z value of this electrode

## Shank
Representation of a shank in NWB.

**Attributes:**
- **name**  `string`: name of the shank
- **shanks_electrodes**  `dict`: electrodes in the shank

## Probe
Representation of a probe in NWB.

**Attributes:**
- **id**  `int`: unique id of the probe
- **probe_type**  `string`: type of probe
- **units**  `string`: units in device
- **probe_description**  `string`: description of probe
- **contact_side_numbering**  `bool`: is contact_side_numbering enabled
- **contact_size**  `float`: value of contact size as float
- **shanks**  `dict`: shanks in the probe

## DataAcqDevice
Representation of data acquisition device in NWB.

**Attributes:**
- **system**  `string`: system of device
- **amplifier**  `string`: amplifier (optional)
- **adc_circuit**  `string`: adc_circuit (optional)

## CameraDevice
Representation of a camera device in NWB.

**Attributes:**
- **meters_per_pixel**  `float`: meters per pixel
- **model**  `string`: model of this camera device
- **lens**  `string`: info about lens in this camera
- **camera_name**  `string`: name of this camera

---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
