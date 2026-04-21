---
name: awesome-bioimaging-datasets-add
description: Add new biological imaging or omics datasets to the awesome-bioimaging-datasets catalog (sources.json). Use this skill whenever the user wants to add a dataset, URL, Zenodo record, or data repository to their sources.json catalog, or asks to check for new datasets to add. Also trigger when the user pastes a URL to a dataset (Zenodo, AWS, GitHub, etc.) and wants it cataloged, or when they say things like "add this dataset", "catalog this", "include this source", or "check for new entries".
---

# Adding datasets to awesome-bioimaging-datasets

awesome-bioimaging-datasets is a curated catalog of public biological imaging and omics datasets stored in `sources.json` at the project root. This skill handles adding new entries, including fetching metadata from URLs, classifying datasets, and maintaining consistency with the existing schema.

## Where to find the catalog

The catalog lives at the root of the awesome-bioimaging-datasets project. Read `readme.md` for the current schema and `sources.json` for existing entries. Always read both before making changes -- the schema may have evolved.

## Workflow for adding a dataset

### 1. Fetch metadata from the source URL

Use WebFetch to extract from the dataset's page:
- Title / name
- Authors (all listed, as "Last, First" format)
- Description of what the data contains
- Associated paper DOIs (check Zenodo API at `/api/records/<id>` for `related_identifiers`)
- Data type (images, expression, morphological profiles)
- Organism, imaging modality, scale

For Zenodo records, the API at `https://zenodo.org/api/records/<id>` is more reliable than scraping the HTML page.

### 2. Construct the entry

Each entry follows this schema (read `readme.md` for the latest):

```json
{
    "id": "kebab-case-unique-id",
    "name": "Human-readable dataset name",
    "url": "https://...",
    "type": "experiment | experiment collection | large-scale database",
    "authors": ["Last, First", "Institution Name"],
    "tags": ["organism", "modality", "data:images", "id:parent-collection"],
    "metadata": [
        {"type": "article", "url": "https://doi.org/..."}
    ]
}
```

Key conventions for the `id` field:
- Use kebab-case
- For datasets from a known lab, prefix with `labname-author-topic` (e.g., `carpentersinghlab-haghighi-mitochondria-psychosis`)
- For subdatasets of a collection, prefix with the parent's id prefix (e.g., `abc-wmb-10xv2` for ABC Atlas)

### 3. Classify with tags

Required tag types:
- **`data:<type>`**: At least one of `data:images`, `data:expression`, `data:morphological_profiles`
- **`id:<parent>`**: If the dataset belongs to an existing collection or large-scale database
- **Category**: Add `atlas`, `benchmark`, or `model` if applicable
- **Source**: Add `zenodo` if hosted on Zenodo (when not obvious from URL)

Also add descriptive tags for: organism, imaging modality, cell type, scale (e.g., "4M cells"), and any domain-specific terms.

### 4. Add metadata references

If the dataset page or API links to associated publications:
- Add each as `{"type": "article", "url": "https://doi.org/..."}` 
- For companion datasets with annotations, use `{"type": "dataset", "url": "..."}`
- For other documentation, use `{"type": "url", "url": "..."}`

If no paper is found, leave `metadata` as an empty list `[]`.

### 5. Insert and sort

After adding the entry to `sources.json`, sort the entire array alphabetically by `id`. Use a script:

```bash
python3 -c "
import json
with open('sources.json') as f:
    data = json.load(f)
data.sort(key=lambda x: x['id'])
with open('sources.json', 'w') as f:
    json.dump(data, f, indent=4, ensure_ascii=False)
    f.write('\n')
"
```

### 6. Check for duplicates

Before adding, verify the dataset isn't already in the catalog by checking:
- Exact URL match
- Similar name
- Same Zenodo record ID

## Adding datasets from a collection

When adding a parent collection with subdatasets (e.g., an atlas with multiple experiments):
1. Add the parent as its own entry (type: `large-scale database` or `experiment collection`)
2. Add each subdataset as a separate flat entry with `id:<parent-id>` in tags
3. Each subdataset needs its own URL pointing to its specific page/documentation

## Checking for new datasets

When asked to check for updates, these are useful sources to scan:

- **Broad Institute bioimage repositories list**: https://github.com/broadinstitute/bioimage-repositories/blob/main/data/repositories.yaml -- a curated list of bioimage repositories that may have new entries
- **Zenodo Broad Imaging community**: https://zenodo.org/api/communities/broad-imaging/records?size=50 -- check for new records not yet in the catalog
- **Cell Painting Gallery**: https://registry.opendata.aws/cellpainting-gallery/ -- check for newly added datasets

Compare fetched entries against existing `sources.json` IDs and URLs to identify what's new.
