# Adverse drug reaction (ADR) - Target association prediction
Machine learning guided association of adverse drug reactions with in vitro off-target pharmacology 

## Overview
Adverse drug reactions (ADRs) are one of the leading causes of morbidity and mortality in health care. Understanding which drug targets are linked to ADRs can lead to the development of safer medicines. 

Here, we analyze in vitro secondary pharmacology of common (off) targets for 2134 marketed drugs. To associate these drugs with human ADRs, we utilized FDA Adverse Event Reports and developed random forest models that predict ADR occurrences from in vitro pharmacological profiles. 

By evaluating Gini importance scores of model features, we identify 221 target-ADR associations, which co-occur in PubMed abstracts to a greater extent than expected by chance. Among these are established relations, such as the association of in vitro hERG binding with cardiac arrhythmias, which further validate our machine learning approach. 

Evidence on bile acid metabolism supports our identification of associations between the Bile Salt Export Pump and renal, thyroid, lipid metabolism, respiratory tract and central nervous system disorders. Unexpectedly, our model suggests PDE3 is associated with 40 ADRs. These associations provide a comprehensive resource to support drug development and human biology studies.

## System requirements: 

### All software dependencies and operating systems (including version numbers):
OS: MacOS 10.13 or higher  (Linux OS and Windows 10 work as well after necessary configurations)
Python version 3.7
R version 3.5.0
RStudio version 1.1.453

### Versions the software has been tested on:
Python v3.6 and 3.7
R versions 3.4.1 and 3.5.0
RStudio version 1.1.453

### Any required non-standard hardware:
computer with sufficient RAM: 8Gb or more

## Installation Guide

### Instructions:

- Download/install python 3.7 from python.org
- In shell:
```shell
pip install virtualenv
cd ~/
virtualenv -p /usr/local/bin/python3 py3 #creates virtual environment named py3 using installed python3.7 version
source py3/bin/activate #activate virtual environment
pip install jupyter
pip install pandas
pip install --upgrade numpy
pip install --upgrade scipy
pip install requests
pip install statsmodels
pip install jellyfish
```
R/Rstudio:
The model is generated using utiml version 0.1.4
```R
Install.packages (c (“randomForest”, “mldr”, “utiml”, “mltools”), dependencies = T) 
library (randomForest) # Load libraries for these packages
library (mldr)
library (utiml)
options (utiml.empty.prediction = T)
library (mltools)
```

Install the ADRtarget repository
```
git clone https://github.com/samanfrm/ADRtarget
```

## Typical install time: 
less than 1h


### OpenFDA Adverse Event Reports query:
Argument 1: start number of queried compounds from `data/compounds.csv`.  
Argument 2: end number of queried compounds from `data/compounds.csv`.  
Argument 3: open FDA api key (to ensure fast run time speed): can be requested at https://open.fda.gov/apis/authentication/ .    
Argument 4: path to this local git ADRtarget repository.  
Run in shell:
```shell
python openFDAapi.py 0 2134 [openFDA api key] [local path]
```
0 and 2134 indicate the begin and end number of compounds (see data/compounds.csv) that are used for querying FDA. The script can be run in smaller chunks of number of compounds, for instance 0 300, 300 1000, 1000 1500 and 1500 2134.
Only if run in smaller chunks: merge the results:
```shell
python merge_fda.py
```

Expected output:
`v1_compounds_FDA.csv`

### Determine ADR occurrences and associations for all compounds 

Run  jupyter notebook:

OpenFDAoutput2modelformat_demo.ipynb

Expected output:

v1_compounds_FDA_model_format_SOC_ocr_prob_demo.csv  
v1_compounds_FDA_model_format_SOC_ocr_bool_demo.csv  
v1_compounds_FDA_model_format_HLGT_ocr_prob_demo.csv  
v1_compounds_FDA_model_format_HLGT_ocr_bool_demo.csv  

### Run random Forest models:
Run the R script, R_code_modeling.R in RStudio.

Please make sure that SOC/ and HLGT/ folders are in the same folder with this R code (R_code_modeling.R)
Follow the instructions in the R script (e.g. please specify/change the working directory/folder on your local computer in scripts where indicated).

The 2 files provided, baseMatrix and v1_compounds_FDA_model_format_*_ocr_bool.csv, where * is either SOC or HLGT, are the inputs for the R script.

With the latest version of utiml package, their accuracy formula was changed and so the summary metrics results were not consistent. If you use the latest utiml version as well, please use the custom code to use the standard accuracy formula (defined in our methods) starting on line 303.

Expected output:

Importance_Gini/
Features_ADRs_predictions.csv
summ_metrics.csv
summ_metrics_V2.csv (if the code starting at line 296 is run)
summ_metrics_ADRs.csv (for all models except in SOC)

### Target-ADR associations
Run ADR_target_demo.ipynb

Expected output:
v1_HLGT_ADR_target_assoc_seed49_demo.csv
v1_HLGT_ADR_target_assoc_significant_demo.csv

The latter file should exactly match Supplementary Information Table 7, our final output of significant target-ADR predictions.

### Expected run time for demo on a "normal" desktop computer
OpenFDA query: with our API-key (provided in scripts) for OpenFDA: up to 2 hours. Without API-key the query speed is capped and takes longer.  
Random Forest training and cross validation: between 1 hour to 24 hours (depending on SOC and HLGT with 1 core, i.e. no parallel computing)
Target-ADR associations: less than 1h

## Instructions for use
### How to run the software on your data / Reproduction instructions
A list of unique identifiers and generic compound names in csv/tsv format (like file compounds.csv) is required to run the openFDA query. For the Random forest model, discretize and one hot encoded the pharmacology data as described in the manuscript methods/ R_code_modeling.R script.
For reproduction, data provided above in our demo reproduce our main results. 



