# `ndx-odor-metadata`

[![pipeline status](https://img.shields.io/gitlab/pipeline-status/fleischmann-lab/ndx/ndx-odor-metadata?branch=main&label=pipeline&style=flat-square)](https://gitlab.com/fleischmann-lab/ndx/ndx-odor-metadata/-/commits/main)
[![license](https://img.shields.io/gitlab/license/fleischmann-lab/ndx/ndx-odor-metadata?color=yellow&label=license&style=flat-square)](LICENSE.txt)

![python version](https://img.shields.io/pypi/pyversions/ndx-odor-metadata?style=flat-square)
[![release](https://img.shields.io/gitlab/v/release/fleischmann-lab/ndx/ndx-odor-metadata?label=release&sort=date&style=flat-square)](https://gitlab.com/fleischmann-lab/ndx/ndx-odor-metadata/-/releases)
[![pypi package](https://img.shields.io/pypi/v/ndx-odor-metadata?label=pypi%20package&style=flat-square&color=blue)](https://pypi.org/pypi/ndx-odor-metadata)
[![conda package](https://img.shields.io/conda/v/fleischmannlab/ndx-odor-metadata?color=green&style=flat-square)](https://anaconda.org/FleischmannLab/ndx-odor-metadata)

NWB extension to store odor stimulus metadata with `DynamicTable` format. Entries that have a PubChem and `stim_types` indicate odor/chemical will also be queried with `pubchempy` for more information.

This is in alpha development stages **WITHOUT** any appropriate tests yet. Please use with discretion.


## Installation

You can install via `pip`:

```bash
pip install ndx-odor-metadata
```

Or `conda`:

```bash
conda install -c fleischmannlab ndx-odor-metadata
```

Or directly from the `git` repository:

```bash
pip install git+https://gitlab.com/fleischmann-lab/ndx/ndx-odor-metadata
```

## Usage

### Main usage

```python
from ndx_odor_metadata import OdorMetaData

odor_table = OdorMetaData(name='odor_table', description='an odor table')

odor_table.add_stimulus(
    pubchem_id = 7662.0,
    stim_name = "3-Phenylpropyl isobutyrate",
    raw_id = 3,
    stim_id = 1,
    stim_types = "odor",
    chemical_dilution_type='vaporized',
    chemical_concentration = 0.01,
    chemical_concentration_unit='%',
    chemical_solvent = "Mineral Oil",
    chemical_provider = "Sigma",
    stim_description = "Legit odor stimulus #1",
)

nwbfile.add_acquisition(odor_table)
```

### Details on arguments

|    | name                        | dtype   | doc                                                                                                                                                                                           | default_value   | quantity   |
|---:|:----------------------------|:--------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------|:-----------|
|  0 | stim_name                   | text    | Stimulus name, e.g. "hexanal" or "sound"                                                                                                                                                      | nan             | nan        |
|  1 | pubchem_id                  | float   | PubChem ID, `NaN` indicates non-(standard)-odor stimulus                                                                                                                                      | nan             | ?          |
|  2 | raw_id                      | text    | Raw acquisition stimulus ID. Will be converted to `str`.                                                                                                                                      | nan             | nan        |
|  3 | raw_id_dtype                | text    | The actual dtype of `raw_id` value. Useful for (re)casting.                                                                                                                                   | N/A             | ?          |
|  4 | stim_id                     | text    | Preferred stimulus ID, which can be used to remap acquisition stimulus id `raw_id`. Will be converted to `str`. If not explicitly given, will copy from `raw_id`                              | nan             | ?          |
|  5 | stim_id_dtype               | text    | The actual dtype of `stim_id` value. Useful for (re)casting.                                                                                                                                  | N/A             | ?          |
|  6 | stim_types                  | text    | Type(s) of stimulus, e.g. 'odor', 'sound', 'control', 'CS', 'US', ...                                                                                                                         | nan             | nan        |
|  7 | stim_types_index            | nan     | Index for `stim_types`                                                                                                                                                                        | nan             | nan        |
|  8 | stim_description            | text    | Human-readable description, notes, comments of each stimulus                                                                                                                                  | N/A             | ?          |
|  9 | chemical_dilution_type      | text    | Type of dilution, e.g. 'volume/volume', 'vaporized'                                                                                                                                           | N/A             | ?          |
| 10 | chemical_concentration      | float   | Concentration of chemical                                                                                                                                                                     | nan             | ?          |
| 11 | chemical_concentration_unit | text    | Unit of concentration, e.g. "%" or "M"                                                                                                                                                        | N/A             | ?          |
| 12 | chemical_solvent            | text    | Solvent to dilute the chemicals in, e.g. 'mineral oil'                                                                                                                                        | N/A             | ?          |
| 13 | chemical_provider           | text    | Provider of the chemicals, e.g. 'Sigma'                                                                                                                                                       | N/A             | ?          |
| 14 | nonchemical_details         | text    | Information about non-chemical/odor stimulus, e.g. 'sound' frequencies                                                                                                                        | N/A             | ?          |
| 15 | is_validated                | bool    | Whether the stimulus, if chemical/odor, is validated against PubChem (or other sources listed in `validation_info`.If does not have a valid PubChem ID, this assumes to default `False` value | False           | ?          |
| 16 | validation_details          | text    | Additional information/details/notes about stimulus validation, e.g. source, software used & version, validation date, ...                                                                    | N/A             | ?          |
| 17 | pubchem_cid                 | float   | PubChem CID, `NaN` indicates non-(standard)-odor stimulus                                                                                                                                     | nan             | ?          |
| 18 | chemical_IUPAC              | text    | Official chemical IUPAC name                                                                                                                                                                  | N/A             | ?          |
| 19 | chemical_SMILES             | text    | Canonical SMILES                                                                                                                                                                              | N/A             | ?          |
| 20 | chemical_synonyms           | text    | List of chemical synonyms                                                                                                                                                                     |                 | ?          |
| 21 | chemical_synonyms_index     | nan     | Index for `chemical_synonyms`                                                                                                                                                                 | nan             | ?          |
| 22 | chemical_molecular_formula  | text    | Molecular formula of chemical used                                                                                                                                                            | N/A             | ?          |
| 23 | chemical_molecular_weight   | float   | Molecular weight of chemical used                                                                                                                                                             | nan             | ?          |

### Demonstration

For more detailed demonstration, please visit the [`demo`](https://gitlab.com/fleischmann-lab/ndx/ndx-odor-metadata/-/tree/main/demo) folder.


---
This extension was created using [ndx-template](https://github.com/nwb-extensions/ndx-template).
