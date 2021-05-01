


```python
import math
import pandas as pd
```


```python
VZP = pd.read_csv('C:/Users/Alema/Desktop/FinTechClass/VZP.csv', delimiter=',')
request_num = pd.read_csv('C:/Users/Alema/Desktop/FinTechClass/VZP_POs.csv', delimiter=',')


```


```python
#This code is used to obtain the columns that I want in my report

report = VZP[['ENGAGEMENT_NUMBER','TRANSACTION_NUMBER', 'BILLING_DESCRIPTION', 'TIME_TYPE','QUANTITY', 'RATE', 'AMOUNT_TIME_PRETAX', 'INVOICE_T0TAL_AMOUNT', 'EXPENSES_TOTAL_PRETAX', 'INVOICE_PRE_TAX_AMOUNT', 'INVOICE_SALES_TAX',]]
```


```python
#this code is used to concatenate my columns to prepare for the groupby function.

col1 = ['ENGAGEMENT_NUMBER', 'TRANSACTION_NUMBER', 'BILLING_DESCRIPTION', 'TIME_TYPE','INVOICE_SALES_TAX', 'RATE', 'INVOICE_PRE_TAX_AMOUNT']
col2 = ['ENGAGEMENT_NUMBER', 'TRANSACTION_NUMBER', 'BILLING_DESCRIPTION', 'TIME_TYPE','QUANTITY', 'AMOUNT_TIME_PRETAX', 'EXPENSES_TOTAL_PRETAX', 'INVOICE_T0TAL_AMOUNT']
report1 = report.reset_index()[col1]
report2 = report.reset_index()[col2]
```


```python
#code is used to prepare the report to give the max and sum of the columns by removing duplicates and formating it to a one line if possible

vzpa = report1.groupby(['ENGAGEMENT_NUMBER', 'TRANSACTION_NUMBER', 'TIME_TYPE', 'BILLING_DESCRIPTION']).max()
vzpb = report2.groupby(['ENGAGEMENT_NUMBER', 'TRANSACTION_NUMBER', 'TIME_TYPE', 'BILLING_DESCRIPTION']).sum()
```


```python
#with the previous (.groupby) code, this allows for the columns to merge and reset my index ( main column to reference)

vzp_result = pd.concat([vzpa, vzpb], axis=1, join='inner').reset_index()


```


```python
#now that my main report is complete, I can now merge the VZP report from Oracle to the PO file that is given from Verizon, I have to make sure that the Engagement_Number is used to reference. 

VZP_Report = pd.merge(vzp_result, request_num, left_on='ENGAGEMENT_NUMBER', right_on='ENGAGEMENT_NUMBER', how='left')
```


```python
# columns are changed to be formated correctly and easy to read

VZP_Report.rename(columns ={'ENGAGEMENT_NUMBER': 'Engagement Number', 'TRANSACTION_NUMBER': 'Invoice', 'BILLING_DESCRIPTION': 'Guard Type','TIME_TYPE': 'Hour Type', 'QUANTITY': 'Total Hours', 'RATE': 'Rate', 'EXPENSES_TOTAL_PRETAX': 'Expenses', 'INVOICE_SALES_TAX': 'Sales Tax', 'AMOUNT_TIME_PRETAX': 'Hour Subtotal', 'INVOICE_PRE_TAX_AMOUNT': 'Pre-Tax Subtotal' , 'INVOICE_T0TAL_AMOUNT': 'Total'}, inplace=True)
```


```python
#  Here I set up the Headers for my report and reformate them to standard of Verizon since they use the file to upload to their database

Headers = list(VZP_Report.columns)
Headers
```




    ['Engagement Number',
     'Invoice',
     'Hour Type',
     'Guard Type',
     'Sales Tax',
     'Rate',
     'Pre-Tax Subtotal',
     'Total Hours',
     'Hour Subtotal',
     'Expenses',
     'Total',
     'Request Number',
     'Street Address',
     'City',
     'State',
     'Zip Code',
     'GBU',
     'Market',
     'Account',
     'Cost Center',
     'Product',
     'Location']




```python
new_columns = ['Engagement Number',
 'Request Number',
 'Invoice', 
 'Street Address',
 'City',
 'State',
 'Zip Code',
 'Guard Type',
 'Hour Type',
 'Rate',
 'Total Hours',
 'Hour Subtotal',
 'Expenses',
 'Pre-Tax Subtotal',
 'Sales Tax',
 'Total', 
 'GBU',
 'Market',
 'Account',
 'Cost Center',
 'Product',
 'Location']
```


```python
VZP_Report =VZP_Report[new_columns]

```


```python
#final product in a csv, then i can input on the template in my excel file

VZP_Report.to_csv('VZP_FINAL.csv', index=False)
```


```python

```
