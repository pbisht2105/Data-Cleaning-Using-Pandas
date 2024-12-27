# Data Cleaning in Pandas

## Goal of the Code

The goal of the code is to clean up a customer data file (an Excel file with a list of customers) to make the data more consistent, usable, and ready for analysis.

## Steps Involved

### 1. Importing Libraries
```python
import pandas as pd
import re
```
- pandas (pd): A powerful library in Python used for data manipulation and analysis. It's often used to work with tables of data, like the one in your Excel file.
- re: A library for working with regular expressions (used to match or search patterns in text).

### 2. Loading the Data
```python
df = pd.read_excel(r'C:\Users\user\Downloads\My Projects\Python\Customer Call List.xlsx')
df
```
- This line reads the Excel file located at the specified path (Customer Call List.xlsx) and stores the data in a DataFrame (which is like a table or a spreadsheet in Python).
- df is the variable that holds this data.

### 3. Removing Duplicates
```python
df = df.drop_duplicates()
-- This removes any duplicate rows in the data. If two rows have the exact same information, one of them will be deleted.
```
### 4. Dropping a Column
```python
df = df.drop(columns='Not_Useful_Column')
This line removes a column called 'Not_Useful_Column' from the data because it is not needed for further analysis.
```
### 5. Cleaning up the 'Last_Name' Column
```python
df['Last_Name'] = df['Last_Name'].str.strip('/_.')
```
- This removes extra characters (like slashes /, underscores _, and periods .) from the beginning and end of the 'Last_Name' column.
- For example, "Smith/_" will be cleaned to "Smith".

### 6. Cleaning the 'Phone_Number' Column
```python
df['Phone_Number'] = df['Phone_Number'].str.replace(r'[-/|_\.]', '', regex=True)
```
-- This removes any hyphens (-), slashes (/), pipes (|), underscores (_), or periods (.) from the 'Phone_Number' column.
-- So a phone number like "123-456_7890" becomes "1234567890".
```python
df['Phone_Number'] = df['Phone_Number'].apply(lambda x: str(x) if pd.notnull(x) else '')
```
-- This ensures that if a phone number is missing or null, it will be replaced with an empty string ('').

```python
df['Phone_Number'] = df['Phone_Number'].apply(lambda x: x[0:3] + '-' + x[3:6] + '-' + x[6:10] if len(x) == 10 else '')
```
-- This part formats phone numbers into the "xxx-xxx-xxxx" format if the phone number has exactly 10 digits.
-- If the number doesn’t have 10 digits, it leaves it as an empty string.
```python
df['Phone_Number'] = df['Phone_Number'].replace('nan--','')
df['Phone_Number'] = df['Phone_Number'].replace('Na--','')
```
-- This replaces any phone number that looks like 'nan--' or 'Na--' (which could be errors) with an empty string.
### 7. Splitting the 'Address' Column
```python
df[['Street Address', 'State', 'Zipcode']] = df['Address'].str.split(',', n=2, expand=True)
```
-- This line splits the 'Address' column into three new columns: 'Street Address', 'State', and 'Zipcode', based on commas in the address.
-- For example, an address like "123 Main St, Springfield, 12345" will be split into:
-- Street Address = "123 Main St"
-- State = "Springfield"
-- Zipcode = "12345"
### 8. Replacing Values in the 'Paying Customer' and 'Do_Not_Contact' Columns
```python
df['Paying Customer'] = df['Paying Customer'].replace({'N': 'No', 'Y': 'Yes'})
df['Do_Not_Contact'] = df['Do_Not_Contact'].replace({'N': 'No', 'Y': 'Yes'})
```
-- This changes values in the 'Paying Customer' and 'Do_Not_Contact' columns:
-- Replaces 'N' with 'No' and 'Y' with 'Yes'.
-- For example, a 'Y' in the 'Paying Customer' column means the customer is a paying customer, and 'N' means they are not.
### 9. Filling Missing Values and Replacing 'NaN' or 'N/a' with Empty Strings
```python
df = df.fillna('')
df = df.replace({'N/a': '', 'NaN': ''})
```
-- The first line replaces any missing (NaN) values with empty strings.
-- The second line replaces any "N/a" or "NaN" text with an empty string.

### 10. Dropping Rows Based on Conditions
```python
for x in df.index:
    if df.loc[x, "Do_Not_Contact"] == 'Yes':
        df.drop(x, inplace=True)
```
-- This loop goes through each row in the DataFrame. If the 'Do_Not_Contact' column is marked as 'Yes', that row is dropped (removed) from the data.
```python
for x in df.index:
    if df.loc[x, "Phone_Number"] == '':
        df.drop(x, inplace=True)
```
Similarly, this loop removes any rows where the 'Phone_Number' is blank (empty string).
### 11. Removing the 'Zipcode' Column
```python
df = df.drop(columns='Zipcode')
```
This removes the 'Zipcode' column from the data because it’s no longer needed after splitting the address into separate columns.
### 12. Resetting the Index
```python
df = df.reset_index(drop=True)
```
This resets the row numbers (index) in the DataFrame. After removing rows, the index might have gaps, so this makes sure the rows are numbered consecutively from 0.


## Final Output
The cleaned data is now ready for analysis. The following changes have been made:

- Duplicate rows removed.
- Irrelevant columns removed.
- Phone numbers cleaned and formatted.
- Address split into street, state, and zipcode.
- Replaced 'Yes'/'No' with 'Y'/'N' for easy understanding.
- Rows with missing phone numbers or marked as "Do Not Contact" are deleted.
- Filled in missing values and replaced unnecessary placeholders like "NaN" or "N/a" with empty strings.
- The DataFrame is reset to ensure row numbers are in order.
