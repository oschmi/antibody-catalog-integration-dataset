[![CC BY-SA 4.0][cc-by-sa-shield]][cc-by-sa]

# Antibody product dataset

This repository contains a dataset for catalog integration of antibody products. It provides a human-readable version (take care, the uncompressed files have a total size of ~25GB) as well as a binary version in [parquet format](https://parquet.apache.org/). 

## About this dataset

We assembled 19 files from integration jobs for the
dataset, containing a total of ca. 420.000 antibody
products. The number of products contained in a single file ranges from 3 up to ca. 230.000 products of 12 manufacturers and different product types (e.g., ELISA Kits, Primary Antibodies, etc.).
Depending on the product type and manufacturer, the number of attribute names per catalog ranges from 14
to 65 names that were integrated into a target schema with 94 labels.
To keep track of manufacturers, we pseudonymized their name and replaced it with a sha256-hash in the file name. 
For better usability, we split all files into smaller chunks.

## Collection process

We collected this dataset by analyzing two stages of an integration pipeline. Firstly, we gathered product catalog
data after the format unification in the CSV format,
where each file contains original attribute names and
contents of a manufacturer. 

Secondly, we picked the corresponding data after the last human interaction before transferring the product information into a database. These files have a unified tabular target schema. Between these stages, data of some input columns were adopted entirely into the target file. 

These attributes identify by matching strings before and after integration. However, other attribute representatives from the input file have been transformed during the integration process: 

Information was extracted from strings into one or multiple target columns, units of measurement were standardized, synonyms and abbreviations might have been resolved. Nevertheless, we only know for sure the relation among exact string matches, since we have no information about the matching process (input label
to target label). 

Find more information in our publication (tbd).

## How to use

### Example entity:
```json
{
'content': ...
'labels': [
    {'Antigen': 0.0}, 
    {'Application': 0.0}, 
    {'Conjugate': 0.0}, 
    {'Description': 0.32163376050000003}, 
    ...
    {'Reactivity': 0.5773502692}, 
    {'Sensitivity': 0.0}, 
    {'Specificity': 0.0}, 
    {'Storage': 0.0}, 
    {'Type': 0.5773502692},
    ...
    {'productNameOriginal': 1.0}
],
'header_similarity': {
'original_header': 'Product Name',
    'headers': [
        {'Antigen': 0.0}, 
        {'Application': 0.0}, 
        {'Application_Advice': 0.0341394371}, 
        ...,
        {'Principle': 0.19564639520000002}, 
        ...,
        {'productNameOriginal': 0.6326308003000001}
        ...
    ]
}
```

### Read a file:
```python
import pandas as pd

if __name__ == "__main__":
    df = pd.read_parquet("filename", engine="fastparquet")
    print(df.head(5))
```

> Note: You need to use "fastparquet" as engine due to nested structures which are not supported by pyarrow.


### Get labels as list of values:

```python
df["labels"] = df['labels'].apply(lambda x: [list(entry.values())[0] for entry in x])
```

### More concise format

You can also transform the dataset into a more concise format by applying the following snippet to the raw dfs, if you are not interested in label names or header similarities:
```python
df = df[['content', 'labels', 'header_similarity']]
df["original_header"] = df["header_similarity"].apply(lambda x: x["original_header"])
df["labels"] = df['labels'].apply(lambda x: [list(entry.values())[0] for entry in x])
df = df[['content', 'labels', 'original_header']]

```


## How to cite
If you use this dataset for a publication please cite
```
@conference{data20,
author={Schmidts, Oliver and Bodo Kraft. and Marvin Winkens. and Albert Zündorf.},
title={Catalog Integration of Low-quality Product Data
by Attribute Label Ranking},
booktitle={tbd},
year={2020},
pages={},
publisher={SciTePress},
organization={INSTICC},
doi={tbd},
isbn={tbd},
}
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0
International License][cc-by-sa].

[![CC BY-SA 4.0][cc-by-sa-image]][cc-by-sa]

[cc-by-sa]: http://creativecommons.org/licenses/by-sa/4.0/
[cc-by-sa-image]: https://licensebuttons.net/l/by-sa/4.0/88x31.png
[cc-by-sa-shield]: https://img.shields.io/badge/License-CC%20BY--SA%204.0-lightgrey.svg