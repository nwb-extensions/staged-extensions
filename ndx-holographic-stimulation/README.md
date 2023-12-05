# ndx-holographic-stimulation Extension for NWB
![extension schema](./schema.png)


## Installation

To install the extension, first clone the `ndx_holographic_stimulation` repository to the desired folder using the command
```angular2svg
git clone https://github.com/catalystneuro/ndx-holographic-stimulation.git
```
Then, to install the requisite python packages and extension, run:
```angular2svg
python -m pip install -r requirements.txt -r requirements-dev.txt
python setup.py install
```
The extension can then be imported into python scripts via `import ndx_holographic_stimulation`.

## Usage

```python

import datetime
import numpy as np
from pynwb import NWBFile, NWBHDF5IO
from ndx_holographic_stimulation import (
    PatternedOptogeneticSeries,
    PatternedOptogeneticStimulusSite,
    OptogeneticStimulusPatternTable,
    OptogeneticStimulusPattern,
    SpiralScanning,
    TemporalFocusing,
    SpatialLightModulator,
    LightSource,
)
from pynwb.ophys import PlaneSegmentation, ImageSegmentation, OpticalChannel

nwbfile = NWBFile(
    session_description="session_description",
    identifier="identifier",
    session_start_time=datetime.datetime.now(datetime.timezone.utc),
)
```
We need to define a `roi_table_region` in the imaging acquisition system, to link the holographic stimulus to a set of ROIs,

```python
recording_device = nwbfile.create_device(
    name="2P_microscope",
    description="My two-photon microscope",
    manufacturer="The best microscope manufacturer",
)

optical_channel = OpticalChannel(
    name="OpticalChannel",
    description="an optical channel",
    emission_lambda=500.0,
)
imaging_plane = nwbfile.create_imaging_plane(
    name="ImagingPlane",
    optical_channel=optical_channel,
    imaging_rate=30.0,
    description="a very interesting part of the brain",
    device=recording_device,
    excitation_lambda=600.0,
    indicator="GFP",
    location="V1",
    grid_spacing=[0.01, 0.01],
    grid_spacing_unit="meters",
    origin_coords=[1.0, 2.0, 3.0],
    origin_coords_unit="meters",
)

n_rois = 2
plane_segmentation = PlaneSegmentation(
    name="PlaneSegmentation",
    description="output from segmenting my favorite imaging plane",
    imaging_plane=imaging_plane,
)
for _ in range(n_rois):
    plane_segmentation.add_roi(image_mask=np.zeros((10, 10)))

if nwbfile is not None:
    if "ophys" not in nwbfile.processing:
        nwbfile.create_processing_module("ophys", "ophys")
    nwbfile.processing["ophys"].add(plane_segmentation)

roi_table_region = plane_segmentation.create_roi_table_region(
    region=[0, 1], description="the first of two ROIs"
)

```
Define optical device that provide the stimulus
```python
# metadata for spiatial light modulator
slm_name = "spatial_light_modulator"
slm_description = "Generic description for the slm"
slm_model = "model"
slm_resolution = 1.0

spatial_light_modulator = SpatialLightModulator(
    name=slm_name,
    description=slm_description,
    model=slm_model,
    resolution=slm_resolution,
)
nwbfile.add_device(spatial_light_modulator)

# metadata for the light source
light_source_name = "light_source"
light_source_description = "Generic description for the laser"
light_source_manifacturer = "manifacturer"
stimulation_wavelength = 600.0
filter_description = "600/50"
power = 8.0
intensity = 100.0
exposure_time = 1e-6
pulse_rate = 1 / exposure_time

light_source = LightSource(
    name=light_source_name,
    stimulation_wavelength=stimulation_wavelength,  # nm
    description=light_source_description,
    filter_description=filter_description,
    power=power,
    intensity=intensity,
    exposure_time=exposure_time,
    pulse_rate=pulse_rate,
)
nwbfile.add_device(light_source)

# metadata for the stimulating device
stimulating_device = nwbfile.create_device(
    name="device",
    description="Microsope used for optogenetic stimulation",
)
```
Define the stimulus pattern and pattern table:
```python
# metadata for  stimulus pattern
pattern_name = "generic_stimulus_pattern"
pattern_description = "beam pattern"
duration = 10e-3
number_of_stimulus_presentation = 10
inter_stimulus_interval = 0.02
# metadata for spiral scanning pattern
spiral_scanning_name = "spiral_scanning"
spiral_diameter = 15e-6
spiral_height = 10e-6
num_revolutions = 5
# metadata for temporal focusing pattern
temporal_focusing_name = "temporal_focusing"
lateral_psf = "9e-6 m ± 0.7e-6 m"
axial_psf = "32e-6 m ± 1.6e-6 m"

spiral_scanning = SpiralScanning(
    name=spiral_scanning_name,
    diameter=spiral_diameter,
    height=spiral_height,
    number_of_revolutions=num_revolutions,
    description=pattern_description,
    duration=duration,
    number_of_stimulus_presentation=number_of_stimulus_presentation,
    inter_stimulus_interval=inter_stimulus_interval,
)

nwbfile.add_lab_meta_data(spiral_scanning)

temporal_focusing = TemporalFocusing(
    name=temporal_focusing_name,
    description=pattern_description,
    lateral_point_spread_function=lateral_psf,
    axial_point_spread_function=axial_psf,
    duration=duration,
    number_of_stimulus_presentation=number_of_stimulus_presentation,
    inter_stimulus_interval=inter_stimulus_interval,
)

nwbfile.add_lab_meta_data(temporal_focusing)

generic_pattern = OptogeneticStimulusPattern(
    name=pattern_name,
    description=pattern_description,
    duration=duration,
    number_of_stimulus_presentation=number_of_stimulus_presentation,
    inter_stimulus_interval=inter_stimulus_interval,
)

nwbfile.add_lab_meta_data(generic_pattern)

stimulus_pattern_table = OptogeneticStimulusPatternTable(
    name="stimulus_pattern_table",
    description=pattern_description,
)
stimulus_pattern_table.add_row(stimulus_pattern=generic_pattern)
stimulus_pattern_table.add_row(stimulus_pattern=spiral_scanning)
stimulus_pattern_table.add_row(stimulus_pattern=temporal_focusing)

stimulus_pattern_ids = np.random.choice([0, 1, 2], size=[100, n_rois])

```
Define the stimulus site: specify the effector and the stimulus pattern used
```python
# metadata for Patterned stimulus site
site_name = "site"
site_description = "This is an example Patterned site."
effector = "ChR2"
location = "VISrl"

stim_site = PatternedOptogeneticStimulusSite(
    name=site_name,
    device=stimulating_device,
    description=site_description,
    excitation_lambda=stimulation_wavelength,  # nm
    effector=effector,
    location=location,
)
nwbfile.add_ogen_site(stim_site)
```
Define the stimulus sequence on the ROIs previously defined in the imaging frame coordinates
```python
# metadata for Patterned series
series_name = "PatternedOptogeneticSeries"
series_description = "Patterned stimulus on 2 rois"
unit = "watts"
data = np.random.rand(100, n_rois)  # ntime x nroi
timestamps = np.linspace(0, 10, num=100)  # a timestamp for every frame

photostimulation = PatternedOptogeneticSeries(
    name=series_name,
    description=series_description,
    data=data,
    unit=unit,
    timestamps=timestamps,
    rois=roi_table_region,
    stimulus_pattern_ids=stimulus_pattern_ids,
    stimulus_pattern_table=stimulus_pattern_table,
    site=stim_site,
    device=stimulating_device,
    light_source=light_source,
    spatial_light_modulator=spatial_light_modulator,
)

nwbfile.add_stimulus(photostimulation)

```
Write and read the NWBFile 
```python

nwbfile_path = "test_photostimulation_nwb.nwb"
with NWBHDF5IO(nwbfile_path, mode="w") as io:
    io.write(nwbfile)

with NWBHDF5IO(nwbfile_path, mode="r") as io:
    nwbfile_in = io.read()

```
## Running tests

<a href="https://pynwb.readthedocs.io/en/stable/software_process.html#continuous-integration">Unit and integration
tests</a> are implemented using <a href="https://docs.pytest.org/en/7.2.x/">pytest</a>, and can be run via the command 
`pytest` from the root of the extension directory (i.e., inside `ndx-holographi-stimulation/src`).

### Specification

Documentation for the extension's <a href="https://schema-language.readthedocs.io/en/latest/">specification</a>, which is based on the YAML files, is generated and stored in
the `./docs` folder. To create it, run the following from the home directory:
```angular2svg
cd docs
make fulldoc
```
This will save documentation to the `./docs/build` folder, and can be accessed via the 
`./docs/build/html/index.html` file.




This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
