# ndx-franklab-novela Extension for NWB

# About
ndx-franklab-novela is a python package containing NWB custom extensions.

# Extensions

### AssociatedFiles
Representation of associated files in NWB <br>
**Attributes:** <br>
    **description**  `string`: description of associated file <br>
    **content**  `string`: content of associated file <br>

#### HeaderDevice
Representation of HeaderDevice in NWB. <br>
**Attributes:** <br>
    **headstage_serial**  `string`: headstage_serial from header global configuration <br>
    **headstage_smart_ref_on**  `string`: headstage_smart_ref_on from header global configuration <br>
    **realtime_mode**  `string`: realtime_mode from header global configuration <br>
    **headstage_auto_settle_on**  `string`: headstage_auto_settle_on from header global configuration <br>
    **timestamp_at_creation**  `string`: timestamp_at_creation from header global configuration <br>
    **controller_firmware_version**  `string`: conntroller_firmware_version from header global configuration <br>
    **controller_serial**  `string`: conntroller_serial from header global configuration <br>
    **save_displayed_chan_only**  `string`: save_displayed_chan_only from header global configuration <br>
    **headstage_firmware_version**  `string`: headstage_firmware_version from header global configuration <br>
    **qt_version**  `string`: qt_version from header global configuration <br>
    **compile_date**  `string`: compile_date from header global configuration <br>
    **compile_time**  `string`: compile_time from header global configuration <br>
    **file_prefix**  `string`: file_prefix from header global configuration <br>
    **headstage_gyro_sensor_on**  `string`: headstage_gyro_sensor_on from header global configuration <br>
    **headstage_mag_sensor_on**  `string`: headstage_mag_sensor_on from header global configuration <br>
    **trodes_version**  `string`: trodes_version from header global configuration <br>
    **headstage_accel_sensor_on**  `string`: headstage_accel_sensor_on from header global configuration <br>
    **commit_head**  `string`: commit_head from header global configuration <br>
    **system_time_at_creation**  `string`: system_time_at_creation from header global configuration <br>
    **file_path**  `string`: file_path from header global configuration <br>

#### ShanksElectrode
Representation of ShanksElectrode object in NWB <br>
**Attributes:** <br>
    **name**  `string`: name of the shank <br>
    **rel_x**  `float`: the rel_x value of this electrode <br>
    **rel_y**  `float`: the rel_y value of this electrode <br>
    **rel_z**  `float`: the rel_z value of this electrode of p

#### Shank
Representation of Shank object in NWB <br>
**Attributes:** <br>
    **name**  `string`: name of the shank <br>
    **shanks_electrodes**  `list`,`tuple`:  'electrodes in shank', 'default': list() <br>

#### Probe
Representation of Probe object in NWB <br>
**Attributes:** <br>
    **id**  `int`: unique id of the probe <br>
    **probe_type**  `string`: type of probe <br>
    **units**  `string`: units in device <br>
    **probe_description**  `string`: probe_description of probe <br>
    **num_shanks**  `int`: number of shanks associated with probe <br>
    **contact_side_numbering**  `bool`: is contact_side_numbering enabled <br>
    **contact_size**  `float`: value of contact size as float <br>
    **shanks**  `object`: shanks in the probe <br>

This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
