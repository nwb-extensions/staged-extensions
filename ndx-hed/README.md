[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.13142816.svg)](https://doi.org/10.5281/zenodo.13142816)
[![PyPI version](https://badge.fury.io/py/ndx-hed.svg)](https://badge.fury.io/py/ndx-hed)
[![codecov](https://codecov.io/gh/hed-standard/ndx-hed/branch/main/graph/badge.svg)](https://codecov.io/gh/hed-standard/ndx-hed)

# ndx-hed extension for NWB

[**Neurodata Without Borders (NWB)**](https://www.nwb.org/) is a data standard for organizing neurophysiology data.
NWB is used extensively as the data representation for single cell and animal recordings as well as
human neuroimaging modalities such as IEEG.
NWB organizes all of the data from one recording session into a single file.

[**HED (Hierarchical Event Descriptors)**](https://www.hedtags.org) is a system of
standardized vocabularies and supporting tools that allows fine-grained annotation of data.
HED annotations can now be used in NWB to provide HED annotations for any NWB
dynamic table. 

The [**HED Integration for NWB**](https://www.hedtags.org/ndx-hed/) gives a quickstart, examples and th API reference.
The [**HED annotation in NWB**](https://www.hed-resources.org/en/latest/HedAnnotationInNWB.html)
user guide explains in more detail how to use this extension for HED. 

## Installation

**Python:**
```bash
pip install -U ndx-hed
```

**Matlab:**  The Matlab extension is under development.

## Quick start

```python
from pynwb import NWBFile
from ndx_hed import HedLabMetaData, HedTags
from datetime import datetime

# Create NWB file with HED metadata
nwbfile = NWBFile(
    session_description="Example session with HED annotations",
    identifier="example_session_001",
    session_start_time=datetime.now()
)

# Add HED schema metadata (required)
hed_metadata = HedLabMetaData(hed_schema_version="8.4.0")
nwbfile.add_lab_meta_data(hed_metadata)

# Add HED column to trials table
nwbfile.add_trial_column(
    name="HED",
    col_cls=HedTags,
    data=[],
    description="HED annotations for trials"
)

# Add trials with HED annotations
nwbfile.add_trial(
    start_time=0.0,
    stop_time=1.0,
    HED="Experimental-trial, (Sensory-event, Visual-presentation)"
)
```

## Main classes

The ndx-hed extension provides three main classes:

| Class | Purpose | Use Cases |
|-------|---------|-----------|
| **HedLabMetaData** | HED schema specification and lab-specific definitions | Required for all HED validation |
| **HedTags** | Row-specific HED annotations | Row-specific tags in any DynamicTable |
| **HedValueVector** | Column-wide HED templates | Shared annotations with value placeholders (#) |

## Examples

The `examples/` directory contains comprehensive runnable examples:

- **[01_basic_hed_classes.py](examples/01_basic_hed_classes.py)** - Introduction to the three main classes
- **[02_trials_with_hed.py](examples/02_trials_with_hed.py)** - Adding HED to trials table
- **[03_events_table_integration.py](examples/03_events_table_integration.py)** - Integration with ndx-events EventsTable
- **[04_bids_conversion.py](examples/04_bids_conversion.py)** - Converting BIDS events to NWB with HED
- **[05_hed_validation.py](examples/05_hed_validation.py)** - Comprehensive validation examples
- **[06_complete_workflow.py](examples/06_complete_workflow.py)** - End-to-end workflow demonstration
- **[07_hed_definitions.py](examples/07_hed_definitions.py)** - Custom HED definitions usage


Run any example:
```bash
cd examples
python 01_basic_hed_classes.py
```

Or run all examples:
```bash
cd examples
python run_all_examples.py
```

## Integration with NWB events

The ndx-hed extension works seamlessly with the [ndx-events extension](https://github.com/rly/ndx-events) to provide comprehensive event annotation capabilities. HED annotations can be incorporated in three ways:

1. **Direct HED column** - Event-specific annotations
2. **HedValueVector columns** - Shared annotations with value placeholders  
3. **Categorical columns with MeaningsTable** - Category-based annotations

See [examples/03_events_table_integration.py](examples/03_events_table_integration.py) for detailed demonstrations.

## BIDS compatibility

[**BIDS (Brain Imaging Data Structure)**](https://bids.neuroimaging.io/index.html) is a data standard
for organizing neuroimaging and behavioral data from an entire experiment.
The standard uses JSON files called "sidecars" to store metadata associated with its tabular files.

The ndx-hed extension provides utilities to convert between BIDS events files and NWB `EventsTable` format:

```python
from ndx_hed.utils.bids2nwb import extract_meanings, get_events_table, get_bids_events
import pandas as pd
import json

# Convert BIDS to EventsTable
bids_events_file_path = "Your_events_path_here.tsv"
bids_sidecar_file_path = "Your_json_sidecar_path_here.json"
events_df = pd.read_csv(bids, sep="\t")
json_data = json.load(bids_sidecar_file_path)
meanings = extract_meanings(bids_sidecar_file_path)
events_table = get_events_table("task_events", "Task events", events_df, meanings)

# Convert EventsTable to BIDS
bids_df, sidecar_dict = get_bids_events(events_table)
```

See [examples/04_bids_conversion.py](examples/04_bids_conversion.py) for complete examples.

## HED validation

Creating HED annotations for NWB data and saving these
annotations as part of an `NWBFile` does not mean the annotations are valid.
HED validation is performed  to ensure they conform to the HED schema:

```python
from ndx_hed.utils.hed_nwb_validator import HedNWBValidator

# Create validator and validate entire file
hed_metadata = HedLabMetadata(hed_schema_version= '8.4.0')
validator = HedNWBValidator(hed_metadata)

# Assume nwbfile has already been created
issues = validator.validate_file(nwbfile)

if not issues:
    print("All HED annotations are valid!")
```

See [examples/05_hed_validation.py](examples/05_hed_validation.py) for comprehensive validation examples.

## Additional Resources

- [HED specification](https://www.hedtags.org/)
- [HED python tools](https://github.com/hed-standard/hed-python)
- [HED annotation in NWB user guide](https://www.hed-resources.org/en/latest/HedAnnotationInNWB.html)
- [NWB documentation](https://pynwb.readthedocs.io/)
- [ndx-events extension](https://github.com/rly/ndx-events)

## Contributing

Contributions are welcome! Feel free to submit issues or pull requests.
