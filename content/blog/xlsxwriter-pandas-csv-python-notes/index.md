---
title: XLSXWriter Pandas CSV Python Notes
summary: XLSXWriter Pandas CSV Python Notes
date: 2021-01-10

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Arseny Togulev on Unsplash**](https://unsplash.com/photos/white-robot-MECKPoKJYjM)'

authors:
  - me

tags:
  - Tech
  - Python
---

Some useful notes in dealing with xlsxwriter, csv, and pandas thing. Still planning to add sql support for fetching data from remote source, then processing it via pandas, then save as xlsx data.

```python
import csv
import xlsxwriter
import datetime as dt
import pandas as pd

# Create an new Excel file and add a worksheet.
workbook = xlsxwriter.Workbook('sample-xlsx.xlsx')
worksheet = workbook.add_worksheet('GPUH')
date_format = workbook.add_format({'num_format': 'mm/dd/yyyy',
                                  'align': 'left'})

def to_dt(date):
    return dt.datetime.strptime(date, '%m/%d/%Y')

pd_csv = pd.read_csv('sample-data.csv')

print(pd_csv)

pd_csv['Date'] = pd_csv['Date'].apply(to_dt)

print(pd_csv[['Price', 'Piece']].groupby(pd_csv['Date'].dt.isocalendar().week).sum())

with open('sample-data.csv') as csv_file:
    csv_reader = csv.reader(csv_file, delimiter=',')

    # pd_df = pd.DataFrame(ind)
    print(pd_csv)

    header = ('Date', 'Price', 'Pieces')

    # Header

    for idx, row in enumerate(csv_reader):
        if idx == 0:
            for ridx, each in enumerate(row):
                worksheet.write(idx, ridx, each)
        else:
            for ridx, col in enumerate(row):
                if ridx == 0:
                    data = dt.datetime.strptime(col, '%m/%d/%Y')
                    worksheet.write(idx, ridx, data, date_format)
                else:
                    worksheet.write(idx, ridx, int(col))

workbook.close()
```

### Prettytable for commandline.
```python
from prettytable import PrettyTable
x = PrettyTable()

test_data = {
    'a1': {
        'url': 'https://sec.domain.com',
        'preset1': True,
        'preset2': True,
        'preset3': False,
        'preset4': True,
        'preset5': True,
        'preset6': False,
        'preset7': True,
    },
    'a2': {
        'url': 'https://sec.domain.com',
        'preset1': True,
        'preset2': True,
        'preset3': False,
        'preset4': True,
        'preset5': True,
        'preset6': False,
        'preset7': True,
    },
    'a3': {
        'url': 'https://sec.domain.com',
        'preset1': True,
        'preset2': True,
        'preset3': False,
        'preset4': True,
        'preset5': True,
        'preset6': False,
        'preset7': True,
    },
    'a4': {
        'url': 'https://sec.domain.com',
        'preset1': True,
        'preset2': True,
        'preset3': False,
        'preset4': True,
        'preset5': True,
        'preset6': False,
        'preset7': True,
    },
}

x.field_names = test_data['a1'].keys()
for col, cval in test_data.items():
    x.add_row(cval.values())
print(x)
``` 

### Mariadb/sqlalchemy to Pandas
```python
from sqlalchemy import create_engine, text
import pandas as pd

engine = create_engine(
    "mariadb+pymsql://user:pass@some_mariadb/dbname?charset=utf8mb4", echo=True, future=True)

with engine.connect() as conn:
    sql_query = text("select * from table")
    df = pd.read_sql(sql_query, conn, index_col="id")

############################

import pandas as pd
import mariadb

mysql_connection = mariadb.connect(host='localhost',
                    user='root',
                    password='',
                    database='tutorfall2016',)

sql = "SELECT * FROM sign_ins"
pd.read_sql(sql, mysql_connection, index_col='id')
```
