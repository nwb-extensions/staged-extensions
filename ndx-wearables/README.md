# ndx-wearables Extension for NWB

Store data from wearable devices in NWB

This extension is designed to help store data collected from a wide variety of wearable devices in a cross-device 
capable way, and with an eye towards clinical applications.
For more details about the extension, see the paper "NDX-Wearables: An NWB Extension for Clinical Neuroscience" 
(to be presented at NER 2025).


## Installation

Create a new Python environment. Python 3.11 works well, python 3.8-3.12 have been tested.

```terminal
conda create -n <env_name> python=3.11
```
You should be able to install from pypi using `pip install ndx-wearables`

If you would like to install and contribute to developing the package, follow the instructions below 
in [Installing in editable mode](#Installing-in-editable-mode)

## Usage

To see how the data stored using this extension looks, visit our example dataset on the
[EMBER Archive](https://dandi.emberarchive.org/dandiset/000207).

You can also generate a local copy of a synthetic datasets by running the relevant `examples/` scripts, for instance
```
python examples/activity_classification.py
```


## Notes on Extension Usage

This extension provides three key classes (`WearableTimeSeries`, `WearableEnumSeries`, `WearableEvents`) to cover:
- `WearableTimeSeries` for general time series data sampled from a wearable device, such as blood oxygenation level
- `WearableEnumSeries` for categorical variables over time with pre-defined meanings (for example sleep stages over time)
- `WearableEvents` for sparse events over long time periods (e.g. exercise events)

In addition to these classes, metadata may be required, including:
- `wearable_device` is required for classes that link to a device 
- `algorithm` is required for many classes to indicate how the data was derived 

The `PhysiologicalMeasure` class is povided to group together similar physiological measurements (e.g. heart rate variability, motion) from different devices/sources. This could include redundant measurements of the same physiological variable, or multiple measurements which will be fused by later processing to estimate an unknown quantity (e.g. multiple accelerometer measurements).

If these arguments are omitted, instantiating the class will raise an error. You can find working examples in the scripts under `examples`.

## Arguments for ndx-wearables Classes

| Class Name             | Required Arguments                                                     | Optional Arguments                            |
|------------------------|------------------------------------------------------------------------|-----------------------------------------------|
| `WearableTimeSeries`   | `name`, `description`, `data`, `timestamps`, `wearable_device`, `algorithm`             | `comments`, `resolution`, `conversion`        |
| `WearableEnumSeries`   | `name`, `description`, `data`, `timestamps`, `wearable_device`, `meanings`, `algorithm` | `resolution`, `conversion`, `comments`        |
| `WearableEvents`       | `name`, `description`, `columns`, `wearable_device`, `algorithm`                        | `resolution`, `conversion`, `comments`        |


These reflect typical usage in constructors. For full context or updates, refer to the class definitions in
[`src/pynwb/ndx_wearables`](src/pynwb/ndx_wearables) and usage examples in [`examples`](examples).


## Developing the extension

We use a gitflow model for collaborative development. Each feature should be created on a branch that branches off of 
the `develop` branch. Draft PRs back into develop should be open for each WIP feature, and marked as ready for review 
once the feature is complete. Periodic releases will be made from develop into `main`.

### Installing in editable mode
Navigate to the project root `cd path/to/ndx-wearables`, then install the required dependencies. For developers, use:

```terminal
pip install -r requirements-dev.txt
```
Custom extensions are added to the extension spec YAML file by running:

```terminal
python src/spec/create_extension_spec.py
```

After running this script, you can verify that the extensions are correctly added to `spec/ndx-wearables.extensions.yaml`.

Running test code may be done with PyTest using the test files located in `src/pynwb/tests`.

To use custom extensions outside a PyTest setting, they must be registered by navigating to the directory root and installing the package:
```terminal
cd path/to/ndx-wearables
pip install -e .
```

---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
