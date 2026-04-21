# CUNA: Curated Compendium of image datasets

A catalog of public biological imaging and omics datasets in `sources.json`.

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
