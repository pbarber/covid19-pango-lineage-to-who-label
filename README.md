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
# Get the mapping from the raw Github URL
resp = requests.get('https://github.com/pbarber/covid19-pango-lineage-to-who-label/raw/main/mapping.json')
# Make sure that the request was successful
resp.raise_for_status()
# Convert the request data to a Python dictionary
mapping = resp.json()
# Expand the Pango column
mapping = pandas.DataFrame(mapping).explode('Pango lineages').reset_index(drop=True)
# Filter out old designations
mapping_current = mapping[mapping['Designation'] != 'Former Variant of Interest']
# Simple example dataframe
test_data = pandas.DataFrame({'Lineage': ['P.2','AY.3.1','P1.7'], 'Sample number': [1,2,3]})
# Function to do pango lineage match
def match(lineage, col):
    return (col.str.slice(stop=len(lineage))==lineage)
# The test dataframe with WHO labels, where the lineage matches, NaN otherwise
matches = mapping['Pango lineages'].apply(match, col=test_data['Lineage'])
match_idx = matches.idxmax()
# Filter out indexes where there is no match
match_idx[match_idx==matches.idxmin()] = pandas.NA
test_data['idx'] = match_idx
# Join to the mapping based on indexes
print(test_data.merge(mapping, how='left', left_on='idx', right_index=True).drop(columns=['idx','Pango lineages']))
```
