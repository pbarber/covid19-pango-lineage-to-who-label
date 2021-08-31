# Pango to WHO mappings for COVID-19

This repository aims to provide an up-to-date reference that can be used to translate between [Pango lineages](https://cov-lineages.org/) (like `B.1.617.2` and `AY.2`) [WHO labels](https://www.who.int/en/activities/tracking-SARS-CoV-2-variants/) (such as *Delta*).

## Structure

The dataset is stored [in JSON](mapping.json) and has three data fields:

* WHO label (e.g. Delta)
* WHO designation (e.g. Variant of Concern)
* Pango lineage (e.g. `P.1`)

## Usage

To load the dataset in Python/pandas use the [requests](https://docs.python-requests.org/en/master/) library:

```python
import requests
resp = requests.get('') # Get the mapping from the static Github URL
resp.raise_for_status() # Make sure that the request was successful
mapping = resp.json()
```