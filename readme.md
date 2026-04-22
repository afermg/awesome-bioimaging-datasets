# Awesome Bioimaging Datasets

A curated catalog of public biological imaging datasets, with some omics and expression resources, in `datasets.json`. Models for bioimaging are cataloged separately in `models.json`.

**69 datasets** in `datasets.json` (15 databases, 4 collections, 50 experiments) and **47 models** in `models.json`.

## Datasets (`datasets.json`)

### Databases

| Name | Source |
|------|--------|
| [Allen Brain Cell Atlas (ABC Atlas)](https://alleninstitute.github.io/abc_atlas_access/intro.html) | Allen Institute for Brain Science |
| [BioImage Archive](https://www.ebi.ac.uk/bioimage-archive/) | EMBL-EBI |
| [Broad Bioimage Benchmark Collection (BBBC)](https://broad.io/BBBC) | Broad Institute Imaging Platform |
| [Cell Painting Gallery](https://registry.opendata.aws/cellpainting-gallery/) | Carpenter-Singh Lab, Cimini Lab |
| [CHAMMI-75](https://aws.amazon.com/marketplace/pp/prodview-2iqmvr5xnpstc) | Morgridge Institute for Research |
| [CZ Virtual Cells Platform](https://virtualcellmodels.cziscience.com/data?modality=microscopy) | Chan Zuckerberg Initiative |
| [Dryad](https://datadryad.org) | Dryad |
| [E11-PRISM](https://registry.opendata.aws/e11bio-prism/) | E11 Bio |
| [Electron Microscopy Public Image Archive (EMPIAR)](https://www.ebi.ac.uk/empiar/) | EMBL-EBI |
| [Figshare](https://figshare.com) | Figshare |
| [Image Data Resource (IDR)](https://idr.openmicroscopy.org/) | Open Microscopy Environment (OME) |
| [JUMP Cell Painting](https://cellpainting-gallery.s3.amazonaws.com/index.html#cpg0016-jump/) | JUMP Cell Painting Consortium |
| [OpenCell](https://opencell.sf.czbiohub.org/) | CZ Biohub San Francisco |
| [Systems Science of Biological Dynamics (SSBD)](https://ssbd.riken.jp/repository) | RIKEN |
| [Zebrahub](https://zebrahub.sf.czbiohub.org/imaging) | Royer Lab, CZ Biohub San Francisco |

### Collections

| Name | Source |
|------|--------|
| [Broad Institute Imaging Platform (Zenodo community)](https://zenodo.org/communities/broad-imaging/records) | Broad Institute Imaging Platform |
| [HMBA-BG: Cross-species basal ganglia](https://alleninstitute.github.io/abc_atlas_access/descriptions/HMBA-10XMultiome-BG.html) | Allen Institute for Brain Science |
| [Yeast time series data and fluorescence](https://github.com/afermg/lots_of_images/blob/master/metadata/manifest.csv) | Munoz, Alan F. |

### Standalone experiments

| Name | Source |
|------|--------|
| [Abnormal mitochondrial localization associated with psychoses](https://zenodo.org/records/15390513) | Haghighi, Marzieh |
| [Bacteria single cell tracking](https://zenodo.org/records/7260137) | Seiffarth, Johannes |
| [Valproic acid-induced changes of 4D nuclear morphology in astrocyte cells](https://zenodo.org/records/15594999) | Kalinin, Alexandr A. et al. |

## Models (`models.json`)

| Name | Source |
|------|--------|
| [BioImage.IO Model Zoo](https://bioimage.io/) | BioImage.IO Community (44 models) |
| [CZI VisCy](https://public.czbiohub.org/comp.micro/viscy/) | Mehta Lab, CZ Biohub San Francisco |
| [Cellpose brightfield models](https://zenodo.org/records/15602078) | Liu, Le |

## Schema

Both `datasets.json` and `models.json` share the same schema. Each entry has the following fields:

| Field     | Type     | Description                                                        |
|-----------|----------|--------------------------------------------------------------------|
| `id`      | string   | Unique kebab-case identifier                                       |
| `name`    | string   | Human-readable name                                                |
| `url`     | string   | Link to the resource or its documentation                          |
| `type`    | string   | See [Types](#types) below                                          |
| `authors` | string[] | Author names or institutions                                       |
| `tags`    | string[] | Descriptive tags (organism, modality, scale, etc.)                 |
| `metadata`| object[] | Associated references (see below)                                  |

Each metadata entry is an object with:

| Field     | Type   | Description                                                              |
|-----------|--------|--------------------------------------------------------------------------|
| `type`    | string | One of `article`, `dataset`, or `url`                                    |
| `url`     | string | Link to the resource (DOI URL, dataset URL, etc.)                        |

Entries are sorted alphabetically by `id`.

## Tags

Tags are free-form strings with two special conventions:

- **`dataset_id:<parent_id>`** -- Links an entry to its parent collection. For example, `dataset_id:abc-atlas` links a subdataset back to the entry with `"id": "abc-atlas"`.
- **`model_id:<model_id>`** -- Links a dataset to a model trained on it. For example, `model_id:bioimage-io-nuclei-segmentation` on a dataset means that model was trained on this data. A dataset may have multiple `model_id:` tags.
- **`data:<type>`** -- Describes the kind of data: `data:images`, `data:expression`, or `data:morphological_profiles`. Entries may have more than one.
- **Category tags** -- `atlas`, `benchmark` classify the dataset's role.
- **Source tags** -- Tags like `zenodo` indicate the hosting platform when it is not obvious from the URL.

## Types

### datasets.json

- **experiment** -- A single study or dataset.
- **experiment collection** -- A group of related experiments (e.g., a Zenodo community, a multi-condition study).
- **large-scale database** -- A major public repository or consortium-scale resource.

### models.json

- **model** -- A single trained model.
- **model collection** -- A group of related models.
- **large-scale database** -- A model zoo or repository.

## Querying

No clone needed -- pipe directly into `jq`:

```bash
SOURCES="https://raw.githubusercontent.com/afermg/cuna/master/datasets.json"
MODELS="https://raw.githubusercontent.com/afermg/cuna/master/models.json"

# All image datasets
curl -s "$SOURCES" | jq -r '.[] | select(.tags[] | contains("data:images")) | "\(.name)\t\(.url)"'

# All mouse brain expression datasets
curl -s "$SOURCES" | jq -r '.[] | select((.tags | any(. == "data:expression")) and (.tags | any(. == "mouse"))) | "\(.name)\t\(.url)"'

# All datasets in a collection (e.g., ABC Atlas)
curl -s "$SOURCES" | jq -r '.[] | select(.tags[] | . == "dataset_id:abc-atlas") | "\(.name)\t\(.url)"'

# List all unique tags
curl -s "$SOURCES" | jq -r '[.[].tags[]] | unique[]'

# All segmentation models
curl -s "$MODELS" | jq -r '.[] | select(.tags | any(contains("segmentation"))) | "\(.name)\t\(.url)"'

# All models from bioimage.io
curl -s "$MODELS" | jq -r '.[] | select(.tags | any(. == "dataset_id:bioimage-io")) | "\(.name)\t\(.url)"'
```
