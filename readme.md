# Awesome Bioimaging Datasets

A catalog of public biological imaging datasets, with some omics and expression resources, in `sources.json`.

**56 datasets** across 12 databases, 4 collections, and 40 individual experiments.

## Databases

| Name | Source |
|------|--------|
| [Allen Brain Cell Atlas (ABC Atlas)](https://alleninstitute.github.io/abc_atlas_access/intro.html) | Allen Institute for Brain Science |
| [BioImage Archive](https://www.ebi.ac.uk/bioimage-archive/) | EMBL-EBI |
| [Broad Bioimage Benchmark Collection (BBBC)](https://broad.io/BBBC) | Broad Institute Imaging Platform |
| [Cell Painting Gallery](https://registry.opendata.aws/cellpainting-gallery/) | Carpenter-Singh Lab, Cimini Lab |
| [CHAMMI-75](https://aws.amazon.com/marketplace/pp/prodview-2iqmvr5xnpstc) | Morgridge Institute for Research |
| [Dryad](https://datadryad.org) | Dryad |
| [E11-PRISM](https://registry.opendata.aws/e11bio-prism/) | E11 Bio |
| [Electron Microscopy Public Image Archive (EMPIAR)](https://www.ebi.ac.uk/empiar/) | EMBL-EBI |
| [Figshare](https://figshare.com) | Figshare |
| [Image Data Resource (IDR)](https://idr.openmicroscopy.org/) | Open Microscopy Environment (OME) |
| [JUMP Cell Painting](https://cellpainting-gallery.s3.amazonaws.com/index.html#cpg0016-jump/) | JUMP Cell Painting Consortium |
| [Systems Science of Biological Dynamics (SSBD)](https://ssbd.riken.jp/repository) | RIKEN |

## Collections

| Name | Source |
|------|--------|
| [Broad Institute Imaging Platform (Zenodo community)](https://zenodo.org/communities/broad-imaging/records) | Broad Institute Imaging Platform |
| [CZI VisCy public datasets](https://public.czbiohub.org/comp.micro/viscy/) | Mehta Lab, CZ Biohub San Francisco |
| [HMBA-BG: Cross-species basal ganglia](https://alleninstitute.github.io/abc_atlas_access/descriptions/HMBA-10XMultiome-BG.html) | Allen Institute for Brain Science |
| [Yeast time series data and fluorescence](https://github.com/afermg/lots_of_images/blob/master/metadata/manifest.csv) | Munoz, Alan F. |

## Standalone experiments

| Name | Source |
|------|--------|
| [Abnormal mitochondrial localization associated with psychoses](https://zenodo.org/records/15390513) | Haghighi, Marzieh |
| [Bacteria single cell tracking](https://zenodo.org/records/7260137) | Seiffarth, Johannes |
| [Valproic acid-induced changes of 4D nuclear morphology in astrocyte cells](https://zenodo.org/records/15594999) | Kalinin, Alexandr A. et al. |

## Schema

Each entry has the following fields:

| Field     | Type     | Description                                                        |
|-----------|----------|--------------------------------------------------------------------|
| `id`      | string   | Unique kebab-case identifier                                       |
| `name`    | string   | Human-readable dataset name                                        |
| `url`     | string   | Link to the dataset or its documentation                           |
| `type`    | string   | One of `experiment`, `experiment collection`, or `large-scale database` |
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

- **`id:<parent_id>`** -- Links a dataset to its parent collection. For example, `id:abc-atlas` links a subdataset back to the entry with `"id": "abc-atlas"`.
- **`data:<type>`** -- Describes the kind of data: `data:images`, `data:expression`, or `data:morphological_profiles`. Entries may have more than one.
- **Category tags** -- `atlas`, `benchmark`, `model` classify the dataset's role.
- **Source tags** -- Tags like `zenodo` indicate the hosting platform when it is not obvious from the URL.

## Types

- **experiment** -- A single study or dataset.
- **experiment collection** -- A group of related experiments (e.g., a Zenodo community, a multi-condition study).
- **large-scale database** -- A major public repository or consortium-scale resource.

## Querying

Find datasets by tag using `jq`:

```bash
# All image datasets
jq -r '.[] | select(.tags[] | contains("data:images")) | "\(.name)\t\(.url)"' sources.json

# All mouse brain expression datasets
jq -r '.[] | select((.tags | any(. == "data:expression")) and (.tags | any(. == "mouse"))) | "\(.name)\t\(.url)"' sources.json

# All datasets in a collection (e.g., ABC Atlas)
jq -r '.[] | select(.tags[] | . == "id:abc-atlas") | "\(.name)\t\(.url)"' sources.json

# List all unique tags
jq -r '[.[].tags[]] | unique[]' sources.json
```
