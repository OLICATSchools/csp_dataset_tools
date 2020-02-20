""" 
File: prep_dataset.py
Contact: data@olicatschools.org

Description:
Extract data for specific schools and national groupings, from a KS2, KS4 or KS5 datafile downloaded from the Compare School Performance (CSP) website.
"""

import os
import csv
import pandas as pd 
import numpy as np 

# Enter URNs here in form 'XXXXXX', 'XXXXXXX'
keep_urns = [] 

# Use values included in the description of RECTYPE, from the metadata document for the associated dataset
# To keep more than one national grouping enter values in form 'X', 'X'
# If no national data is required leave [] empty
keep_national = []

# Create a file called 'keep_columns.csv' with the field names to keep and save it in the same location as this script
# The field names must be in the first column of the file, all other columns will be ignored by the script
# Field names can be found in the metadata document for the associated dataset 
keep_columns = []

# This block reads the csv line by line and creates a python list containing the names of the columns to be kept
with open('keep_columns.csv') as csv_file:
  read_csv = csv.reader(csv_file, delimiter = ',')

  # Comment this line out if your keep_columns.csv file does not contain column headers
  next(read_csv)

  for row in read_csv:
    keep_columns.append(row[0])
  
# This will be our contain our cleaned and fully merged dataset
dataset = pd.DataFrame()

# Datafiles downloaded from CSP website must have had the academic year added as a prefix to the filename, for example '1819_ks4final.xlsx'
for file in os.listdir():
  if file.endswith('.xlsx') & file[0].isdigit():
    
    # Read the contents of the Excel file into a dataframe. Make all values strings - important for handling errors.
    temp = pd.read_excel(file).astype(str)
    
    # Create a new column in the dataset called year and fill each row with the first four characters of the Excel filename
    temp['YEAR'] = file[:4]
    
    # Drop columns not included in the keep_columns.csv file
    temp = temp[keep_columns]

    # Drop rows which don't represent the schools or national datasets identified in keep_urns or keep_national
    temp = temp[temp['RECTYPE'].isin(keep_national) | temp['URN'].isin(keep_urns)]

    # Helper function for converting string type cells containing the % character to numeric percentage decimals
    def pct_to_float(cell):
      if '%' in cell:
        cell = float(cell.replace('%', '')) / 100
      return cell
    
    
    for col in temp.columns:
      
      # Convert any cells with a % sign in them to floats with the percentage expressed as a decimal
      temp[col] = temp[col].apply(pct_to_float)
      
      # Convert all cells in a column to numeric datatype. If cell value can't be converted to number, i.e. it contains text characters, replace it with NaN (null equivalent)
      temp[col] = pd.to_numeric(temp[col], errors = 'coerce')
    
    
    # Add clean data to master dataset
    dataset = dataset.append(temp)


# Export dataset to Excel
dataset.to_excel('dataset.xlsx')
