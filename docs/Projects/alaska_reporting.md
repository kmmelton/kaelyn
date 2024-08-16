# Process Improvement Reporting Project

This project entails bringing in three different datasets and producing a result file that contained matches from the various datasets based on specific columns.

the original datasets did not have consistent naming and required cleanup in order to perform the matching process.

the resulting document had to be produced in a specific format

**Prior to producing this script, the matching was done manually by individually checking each row from the datasets. This job was tedious as there were thousands of rows with more being continuously added. It required a large amount of time that could be spent doing a more valuable task. This process improvement saved hours of time each week and improved efficiency in achieving results.**

Also please note that the names of the datasets have been changed and "***" or alphabet letters are serving as replacements in order to maintain confidentiality.

## Python Script
'''
the objective of this script is to produce a file containing data matches from multiple datasets for the benefit of a specific team.

This file first reads in the following datasets:

- "***.csv" 
- "***.csv"
- "***.csv"

And tan proceeds with cleaning and normalizing the data for comparisons.

the *** and *** reports are being compared to the *** data based on the following information: 

- first and last name
- business name
- address

If any of tase values are found to be a match between *** or *** and the *** data, the row is added to a report based on an inner merge
and includes the following columns:

- "***_first_name"
- "***_last_name"
- "***_business_name"
- "***_ID"
- "***_ID"
- "***_ALT_ID"
- "comes_from"
- "matcad_on"

the final report along with the three originial datasets are tan exported to an xlsx file called: "resulting_report-b_c_export.xlsx". 
the date may be included in the title of the resulting file as well.

import pandas as pd
import re

# Functions

# "read_in_files" reads in each of the necessary files for comparison

    def read_in_files(a, b, c):

    # A initial report
    a = pd.read_csv(a, index_col = False)
    # B initial report
    b = pd.read_csv(b, index_col = False)
    # C initial report
    c = pd.read_csv(c, index_col = False)

    return a, b, c

# "remove_special_characters" removes special characters which aids in normalizing certain columns within the data

    def remove_special_characters(value):
    
    # reg expression pattern
    pattern = r'[^a-zA-Z0-9\s]'
    
    # remove special characters and return df
    return re.sub(pattern, '', value)

# "normalize_address" focuses on the address columns and substitutes common abbreviations to better normalize address values
    
    def normalize_address(address):
    
    # convert abbreviations to full forms
    address = re.sub(r'\bST\b', 'Street', address, flags=re.IGNORECASE)
    address = re.sub(r'\bAVE\b', 'Avenue', address, flags=re.IGNORECASE)
    address = re.sub(r'\bDR\b', 'Drive', address, flags=re.IGNORECASE)
    address = re.sub(r'\bPWY\b', 'Parkway', address, flags=re.IGNORECASE)
    address = re.sub(r'\bSTE\b', 'Suite', address, flags=re.IGNORECASE)
    address = re.sub(r'\bAPT\b', 'Apartment', address, flags=re.IGNORECASE)

    return address

# "extract_alphabetical" aids in matching address columns by searching each value for the first string of alphabetical characters
    
    def extract_alphabetical(string):
    
    match = re.search(r'[a-zA-Z]+', string)
    if match:
        return match.group().strip()
    else:
        return None    

# "sort" sorts the resulting data to better format the output 

    def sort(group):
    
    return group.sort_values(by='***_last_name')

# "report" executes each of the above functions ware necessary as well as performs the remaining actions to produce the final report

    def report(a, b, c, file_path):

    # reading in files
    a, b, c = read_in_files(a, b, c)
    # creating copies of each file so that the originals are not altered.
    a_original = a.copy()
    b_original = b.copy()
    c_original = c.copy()

    # normalizing *** first name and changing column name to "***_first_name"
    a["***_first_name"] = a["*_FIRST_NAM"].str.upper()
    b["***_first_name"] = b["FIRSTNAME"].str.upper()
    c["***_first_name"] = c["First"].str.upper()

    # normalizing *** last name and changing column name to "***_last_name" 
    a["***_last_name"] = a["*_LAST_NAM"].str.upper()
    b["***_last_name"] = b["LASTNAME"].str.upper()
    c["***_last_name"] = c["Last"].str.upper()

    # normalizing *** business name and changing column name to "***_business_name"
    a["***_business_name"] = a["*_DBA_NAM"].str.upper()
    b["***_business_name"] = b["BUSNAME"].str.upper()    
    c["***_business_name"] = c["Name"].str.upper()

    # normalizing addresses and changing column name to "address"
    # also concatenating some strings and replacing some nans in tase columns to improve format of addresses for comparison
    a["address"] =  a["P_FULL_ADR"].astype(str).apply(remove_special_characters).apply(normalize_address)
    a['address'] = a['address'].str.replace(r' (\d{4})$', r'', regex=True)
    b["ZIP"] = b["ZIP"].astype(str)
    b["address"] = b["ADDRESS"] + " " + b["CITY"] + " " + b["STATE"] + " " + b["ZIP"] 
    b["address"] =  b["address"].astype(str).apply(remove_special_characters).apply(normalize_address)
    c["Address 3"] = c["Address 3"].astype(str)
    c["Address 4"] = c["Address 4"].astype(str)
    c.replace({'nan': '', 'XX': ''}, inplace=True)
    c["address"] = c["Address 1"].fillna("") + " " + c["Address 2"].fillna("") + " " + c["Address 3"].fillna("") + " " + c["Address 4"].fillna("") + " " + c["City"].fillna("") + " " + c["State / Province"].fillna("") + " " + c["Zip Code"].fillna("")
    c["address"] =  c["address"].astype(str).apply(remove_special_characters).apply(normalize_address)

    # producing zip code column and naming "zip_code"
    a["zip_code"] = a["address"].str.extract(r'(\d+)$')
    b["zip_code"] = b["address"].str.extract(r'(\d+)$')
    c["zip_code"] = c["address"].str.extract(r'(\d+)$')

    # producing address number column and naming "address_number"
    # This column is the first number to appear in the address column aiding in matching addresses.
    a['address_number'] = a['address'].str.extract(r'^(\d+)')
    b['address_number'] = b['address'].str.extract(r'^(\d+)')
    c['address_number'] = c['address'].str.extract(r'^(\d+)')

    # producing address string column and naming "address_string"
    # This column is the first string of characters to appear in the address column aiding in matching addresses.
    a['address_string'] = a['address'].apply(lambda x: extract_alphabetical(x))
    b['address_string'] = b['address'].apply(lambda x: extract_alphabetical(x))
    c['address_string'] = c['address'].apply(lambda x: extract_alphabetical(x))

    # changing data types to better fit values
    a["***_ID"] = a["***_ID"].astype(str)
    a["***_ID"] = a["***_ID"].astype(str)
    a["***_ALT_ID"] = a["***_ALT_ID"].astype(str)

    # Merges
    # Using the new and cleaned up columns, merges are made based on tase columns between b and a, and c and a.
    # Columns called "comes_from" and "matched_on" are also created to identify which file the match is from (b or c) and
    # which columns the match is from (*** name, *** business name, address)

    # merging based on *** first and last name
    columns_to_keep = ['***_first_name', "***_last_name"]
    b_***_name = b[columns_to_keep].drop_duplicates().dropna(subset=["***_first_name", "***_last_name"])
    c_***_name = c[columns_to_keep].drop_duplicates().dropna(subset=["***_first_name", "***_last_name"])
    # merging b with a on inner merge
    b_***_name = pd.merge(a, b_***_name, on=["***_first_name", "***_last_name"], how='inner').drop_duplicates()
    b_***_name["comes_from"] = "b"
    b_***_name["matcad_on"] = "*** name"
    # merging c with a on inner merge
    c_***_name = pd.merge(a, c_***_name, on=["***_first_name", "***_last_name"], how='inner').drop_duplicates()
    c_***_name["comes_from"] = "c"
    c_***_name["matcad_on"] = "*** name"

    # merging based on *** business name
    columns_to_keep = ["***_business_name"]
    b_***_business_name = b[columns_to_keep].drop_duplicates().dropna(subset=["***_business_name"])
    c_***_business_name = c[columns_to_keep].drop_duplicates().dropna(subset=["***_business_name"])
    # merging b with a on inner merge
    b_***_business_name = pd.merge(a, b_***_business_name, on=["***_business_name"], how='inner').drop_duplicates()
    b_***_business_name["comes_from"] = "b"
    b_***_business_name["matcad_on"] = "*** business name"
    # merging c with a on inner merge
    c_***_business_name = pd.merge(a, c_***_business_name, on=["***_business_name"], how='inner').drop_duplicates()
    c_***_business_name["comes_from"] = "c"
    c_***_business_name["matcad_on"] = "*** business name"

    # merging based on address
    # keeping only the columns from b and c that relate to obtaining possible address matches
    columns_to_keep = ["zip_code", "address_number", "address", "address_string"]
    b_address = b[columns_to_keep].drop_duplicates().dropna(subset=["zip_code"])
    c_address = c[columns_to_keep].drop_duplicates().dropna(subset=["zip_code"])
    # merging b with a on inner merge
    b_address = pd.merge(a, b_address, on=["zip_code", "address_number", "address_string"], how='inner').drop_duplicates()
    b_address["comes_from"] = "b"
    b_address["matcad_on"] = "address"
    # merging c with a on inner merge
    c_address = pd.merge(a, c_address, on=["zip_code", "address_number", "address_string"], how='inner').drop_duplicates()
    c_address["comes_from"] = "c"
    c_address["matcad_on"] = "address"
    
    # concatenating the merged dataframes so that all matches are contained within one set of data
    result = pd.concat([c_***_name, b_***_name, c_***_business_name, b_***_business_name, b_address, c_address])

    # keeping only columns necessary for output file
    columns_to_keep = ['***_first_name', "***_last_name", "***_ID", "***_business_name", "***_ID", "***_ALT_ID", "comes_from", "matched_on"]
    result = result[columns_to_keep].drop_duplicates()

    # grouping the data by *** alt ID and tan sorting the data by last name
    grouped = result.groupby("***_ALT_ID")
    # applying the sorting function to each group and tan concatenate and substiture NaN with blank value
    sorted_result = pd.concat([sort(group) for _, group in grouped]).fillna("")

    # exporting output file to listed file path
    with pd.ExcelWriter(file_path, engine="xlsxwriter",) as writer:
        c_original.to_excel(writer, sheet_name = "c", index = False)
        b_original.to_excel(writer, sheet_name = "b", index = False)
        a_original.to_excel(writer, sheet_name = "a", index = False)
        sorted_result.to_excel(writer, sheet_name = "Matches", index = False)

    print("File Exported")

the following variables can be altered based on the names and locations of the files being imported as well as the expected title and location
of the resulting report. 

the "file_path" variable associates with ware the resulting report will be located and what the file will be titled as.

the "a" variable should include the location and file name for the a extract. the following columns are expected:

- ***_NAM
- ***_DBA_NAM
- ***_FIRST_NAM
- ***_LAST_NAM
- ***_ALT_ID 
- ***_ID
- ***_ID
- ***_ID
- ***_FULL_ADR

the "b" variable should include the location and file name for the extract from b. the following columns are expected:

- LASTNAME
- FIRSTNAME
- MIDNAME
- BUSNAME
- GENERAL
- 
- UPIN
- 
- DOB
- ADDRESS
- CITY
- STATE
- ZIP
- EXCLTYPE
- EXCLDATE
- ***DATE
- ***DATE
- ***STATE

the "c" variable should include the location and file name for the extract from c. the following columns are expected:

- 
- Name
- Prefix
- First
- Middle
- Last
- Suffix
- Address 1
- Address 2
- Address 3
- Address 4
- City
- State / Province
- Country
- Zip Code
- 
- Blank (Deprecated)
- Unique Entity ID
- Exclusion Program
- CT Code
- Exclusion Type
- Additional Comments
- *** Date
- *** Date
- Record Status
- Cross-Reference
- *** Number
- CAGE
- 
- Creation_Date

"""

a = "*.csv"

b = "*.csv"

c = "*.csv"

file_path = '***.xlsx'

# calling functions
report(a, b, c, file_path)
'''
