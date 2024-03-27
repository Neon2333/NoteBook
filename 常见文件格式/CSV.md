# 1. 简介

一种用逗号作为**分割符**存储数据的**纯文本格式**。

一行(row)的字段用逗号隔开，每一行是一条记录。

几乎所有**表格、数据库**都支持该格式。

# 2. Python操作CSV

```python
import csv
with open('.csv') as csvfile:
    csvreader=csv.reader(csvfile)	#读文件建立reader对象
    listcsv=list(csvreader)	#将数据转成list
    print(listcsv)
    print(listcsv[0][1])	#第一行第一例字段值
```

