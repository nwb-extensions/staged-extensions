# ndx-cell-culture

`ndx-cell-culture` is a Neurodata Without Borders (NWB) extension for describing
cultured neural preparations, including organoids, assembloids,
directoids/connectoids, slices, explants, spheroids, and dissociated cultures.
It records biological identity and provenance alongside the metadata needed to
interpret experiments on those preparations.

## What it models

- `CellCultureSubject` extends core NWB `Subject` and links the recorded or
  described `CellCulture` to its source lines and parent cultures.
- `CellLine` and `CellCulture` describe preparation identity and provenance,
  with optional `GeneticVariant`, `ConstructApplication`, and `CultureProtocol`
  metadata. Referenced biological objects are collected when the subject is
  constructed.
- `ExperimentContext` extends core NWB `LabMetaData` and stores recording
  conditions and optional `Pharmacology` records.
- Core NWB types continue to represent devices, acquisition, stimuli, imaging,
  and processing data.

## Installation

Python 3.8 or newer is required. Install version 1.0.0 from PyPI:

```bash
python -m pip install ndx-cell-culture==1.0.0
```

## Quickstart

This example writes and reads a metadata-only synthetic NWB file. All identifiers
and donor ages are illustrative; use the actual source metadata for real data.
Core subject age describes the donor/source at collection, while culture age is
stored separately on the culture and recording context.

```python
from datetime import datetime, timezone

from pynwb import NWBHDF5IO, NWBFile
from ndx_cell_culture import CellCulture, CellCultureSubject, ExperimentContext

culture = CellCulture(
    name="CULT-SYN-ORG-001",
    culture_id="CULT-SYN-ORG-001",
    culture_type="organoid",
    sample_label="Synthetic cortical organoid",
    species="Homo sapiens",
    culture_subtype="cortical",
    age="P120D",
    age_reference="days_post_induction",
)

nwbfile = NWBFile(
    session_description="Synthetic organoid metadata example",
    identifier="NWB-SYN-ORG-001",
    session_start_time=datetime(2026, 1, 1, tzinfo=timezone.utc),
)
nwbfile.subject = CellCultureSubject(
    subject_id="SUBJ-SYN-ORG-001",
    species="Homo sapiens",
    sex="U",
    age="P30Y",  # Synthetic donor age at collection, separate from culture age.
    description="Synthetic organoid; synthetic donor age at collection: 30 years",
    culture=culture,
)
nwbfile.add_lab_meta_data(ExperimentContext(
    name="recording",
    experiment_id="EXP-SYN-ORG-001",
    age_at_recording="P120D",
    age_reference="days_post_induction",
    recording_platform="MEA",
))

with NWBHDF5IO("synthetic_organoid.nwb", "w") as io:
    io.write(nwbfile)

with NWBHDF5IO("synthetic_organoid.nwb", "r", load_namespaces=True) as io:
    restored = io.read()
    assert restored.subject.culture.culture_id == "CULT-SYN-ORG-001"
    assert restored.lab_meta_data["recording"].culture is restored.subject.culture
```

For DANDI deposition, provide the inherited subject identifier, species, sex,
and donor/source age or birth date. Consult the
[validation and DANDI guidance](https://ndx-cell-culture.readthedocs.io/en/latest/validation_dandi.html)
for the additional checks needed before deposition.

## Documentation and resources

- [User documentation](https://ndx-cell-culture.readthedocs.io/en/latest/)
- [Installation and quickstart](https://ndx-cell-culture.readthedocs.io/en/latest/usage.html)
- [Modeling guide](https://ndx-cell-culture.readthedocs.io/en/latest/modeling_guide.html)
- [Field reference](https://ndx-cell-culture.readthedocs.io/en/latest/field_reference.html)
- [Examples and recipes](https://ndx-cell-culture.readthedocs.io/en/latest/examples.html)
- [Release notes and compatibility guidance](https://ndx-cell-culture.readthedocs.io/en/latest/release_notes.html)
- [Source code and issue tracker](https://github.com/braingeneers/ndx-cell-culture)
- [Version 1.0.0 release](https://github.com/braingeneers/ndx-cell-culture/releases/tag/v1.0.0)
- [PyPI package](https://pypi.org/project/ndx-cell-culture/)

The extension is maintained by [Braingeneers](https://github.com/braingeneers)
under the [BSD 3-Clause license](https://github.com/braingeneers/ndx-cell-culture/blob/v1.0.0/LICENSE.txt).
Catalog maintainer: [davidparks21](https://github.com/davidparks21).
