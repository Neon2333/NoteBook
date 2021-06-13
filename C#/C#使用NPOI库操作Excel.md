# C#使用NPOI库操作Excel

https://www.cnblogs.com/restran/p/3889479.html

https://www.cnblogs.com/wanzhongjun/p/7151514.html

https://blog.csdn.net/WuLex/article/details/108914907

https://www.pianshen.com/article/9409729801/

## 1. 引用动态库

> ICSharpCode.SharpZipLib.dll
>
> NPOI.dll
>
> NPOI.OOXML.dll
>
> NPOI.OpenXml4Net.dll
>
> NPOI.OpenXmlFormats.dll

## 2. 头文件

> using NPOI;
> using NPOI.SS.UserModel;
> using NPOI.XSSF.UserModel;
> using NPOI.HSSF.UserModel;
> using System.Diagnostics;

## 3. 从Excel中读取

excel表格有两种后缀名 .xls 和 .xlsx。.xls是office2007以前版本的excel表的后缀名，而.xlsx是office2007以后的excel后缀。

一个excel文件表里有多个工作簿sheet，每一个工作簿中都可以存数据。

### （1）打开.xlsx文件

```C#
//打开或创建excel文件并向里添加数据
//new HSSFWorkbook();	//这是用于后缀名是.xls的excel文件的操作
FileStream fs = File.OpenRead("C:\\Users\\Administrator\\Desktop\\table1.xlsx");	//关联流打开文件
IWorkbook workbook = null;	
workbook = new XSSFWorkbook(fs);    //XSSF打开xlsx
```

### （2）读取sheet

```C#
ISheet sheet = null;
sheet = workbook.GetSheetAt(i);	//获取第i个sheet
```

### （3）读取行

```C#
IRow row = null;
row = sheet.GetRow(i);	//获取第i行
```

```C#
//行标从0开始计数
int  numsOfRows = sheet.LastRowNum + 1;	//sheet.LastRowNum为最后一行的index
```

### （4）读取某行的单元格内的数据（单元格内可存不同数据类型）

```C#
ICell cell = row.GetCell(i);	//获取row行的第i列的数据
```

```C#
//单元格存储的数据类型
public enum CellType
    {
        Unknown = -1,
        Numeric = 0,
        String = 1,
        Formula = 2,
        Blank = 3,
        Boolean = 4,
        Error = 5
    }

//NPOI库中ICell定义
public interface ICell
    {
        ISheet Sheet { get; }
        CellRangeAddress ArrayFormulaRange { get; }
        IHyperlink Hyperlink { get; set; }
        IComment CellComment { get; set; }
        ICellStyle CellStyle { get; set; }
        bool BooleanCellValue { get; }	
        string StringCellValue { get; }	//若存储的是string，可通过该属性获取string值
        byte ErrorCellValue { get; }
        IRichTextString RichStringCellValue { get; }
        DateTime DateCellValue { get; }	//获取DateTime
        double NumericCellValue { get; }	
        string CellFormula { get; set; }
        CellType CachedFormulaResultType { get; }
        CellType CellType { get; }	//通过该属性获取存储的数据的类型
        IRow Row { get; }
        bool IsMergedCell { get; }
        int RowIndex { get; }
        int ColumnIndex { get; }
        bool IsPartOfArrayFormulaGroup { get; }

        ICell CopyCellTo(int targetIndex);
        CellType GetCachedFormulaResultTypeEnum();
        void RemoveCellComment();
        void RemoveHyperlink();
        void SetAsActiveCell();
        void SetCellErrorValue(byte value);
        void SetCellFormula(string formula);
        void SetCellType(CellType cellType);
        void SetCellValue(bool value);
        void SetCellValue(string value);
        void SetCellValue(IRichTextString value);
        void SetCellValue(DateTime value);
        void SetCellValue(double value);
    }
```

* 据此，可自定义一个根据数据类型获取数据的函数：

```c#
//返回值是object，具体用变量来接的时候，要先强转一下
/**
   在读取文件时大都会判断单元格类型，方式大同小异，只有日期类型不同。

　　默认日期类型的单元格在NPOI都认为是数值类型（CellType.Numeric）

　　在高版本中用HSSFDateUtil.IsCellDateFormatted(cell)  来判断

　　但在低版中没有这个类，网上找到有NPOI.SS.UserModel.DateUtil.IsCellDateFormatted(cell)，同样的作用。
*/
public object GetCellValue(ICell cell)
        {
            object value = null;
            try
            {
                if (cell.CellType != CellType.Blank)
                {
                    switch (cell.CellType)
                    {
                        case CellType.Numeric:
                            //判断单元格内数据是否是DateTime
                            if (DateUtil.IsCellDateFormatted(cell))
                            {
                                value = cell.DateCellValue;	//若是日期格式，则用DateCellValue获取DateTime
                            }
                            else
                            {
                                // Numeric type
                                value = cell.NumericCellValue;
                            }
                            break;
                        case CellType.Boolean:
                            // Boolean type
                            value = cell.BooleanCellValue;
                            break;
                        case CellType.Formula:
                            value = cell.CellFormula;
                            break;
                        default:
                            // String type
                            value = cell.StringCellValue;
                            break;
                    }
                }
            }
            catch (Exception)
            {
                value = "";
            }

            return value;
        }
```



## 5. 写Excel

### （1）用流创建或读取.xlsx文件（同时流关联了文件）

```C#
FileStream filestream = new FileStream(@"C:\Users\25224\Desktop\table2.xlsx", FileMode.OpenOrCreate);	
```

```C#
//Excel表打开方式
public enum FileMode
    {
        //
        // 摘要:
        //     指定操作系统应创建新文件。这需要 System.Security.Permissions.FileIOPermissionAccess.Write 权限。如果文件已存在，则将引发
        //     System.IO.IOException异常。
        CreateNew = 1,
        //
        // 摘要:
        //     指定操作系统应创建新文件。如果文件已存在，它将被覆盖。
    	//这需要 System.Security.Permissions.FileIOPermissionAccess.Write
        //     权限。FileMode.Create 等效于这样的请求：如果文件不存在，则使用 System.IO.FileMode.CreateNew；否则使用 System.IO.FileMode.Truncate。如果该文件已存在但为隐藏文件，则将引发
        //     System.UnauthorizedAccessException异常。
        Create = 2,
        //
        // 摘要:
        //     指定操作系统应打开现有文件。
	    //打开文件的能力取决于 System.IO.FileAccess 枚举所指定的值。如果文件不存在，引发一个 System.IO.FileNotFoundException
        //     异常。
        Open = 3,
        //
        // 摘要:
        //     指定操作系统应打开文件（如果文件存在）；否则，应创建新文件。
    	//如果用 FileAccess.Read 打开文件，则需要 System.Security.Permissions.FileIOPermissionAccess.Read权限。如果文件访问为
        //     FileAccess.Write，则需要 System.Security.Permissions.FileIOPermissionAccess.Write权限。如果用
        //     FileAccess.ReadWrite 打开文件，则同时需要 System.Security.Permissions.FileIOPermissionAccess.Read
        //     和 System.Security.Permissions.FileIOPermissionAccess.Write权限。
        OpenOrCreate = 4,
        //
        // 摘要:
        //     指定操作系统应打开现有文件。该文件被打开时，将被截断为零字节大小。这需要 System.Security.Permissions.FileIOPermissionAccess.Write
        //     权限。尝试从使用 FileMode.Truncate 打开的文件中进行读取将导致 System.ArgumentException 异常。
        Truncate = 5,
        //
        // 摘要:
        //     若存在文件，则打开该文件并查找到文件尾，或者创建一个新文件。这需要 System.Security.Permissions.FileIOPermissionAccess.Append
        //     权限。FileMode.Append 只能与 FileAccess.Write 一起使用。试图查找文件尾之前的位置时会引发 System.IO.IOException
        //     异常，并且任何试图读取的操作都会失败并引发 System.NotSupportedException 异常。
        Append = 6
    }
```

### （2）创建表和sheet

```C#
XSSFWorkbook wk = new XSSFWorkbook();	//创建表对象wk
ISheet isheet = wk.CreateSheet("Sheet1");	//在wk中创建sheet1
```

### （3）向单元格中写数据

```C#
 IRow row2 =null;
 ICell cell = null;
 row2 = isheet.CreateRow(j);	//创建index=j的行
 cell = row2.CreateCell(0);		//在index=j的行中创建index=0的单元格
 cell.SetCellValue("哈哈");		//给创建的单元格赋值string
```

### （4）通过流将表中写入的数据一次性写入文件中

```C#
wk.Write(filestream);	//通过流filestream将表wk写入文件
filestream.Close();	//关闭文件流filestream
wk.Close();	//关闭Excel表对象wk
```

## 6. 日期格式

**在Excel表中写入时间数据时，设置单元格格式，自定义格式yyyy-MM-dd HH:mm:ss**

### （1）DateTime

```C#
.toString("yyyy-MM-dd HH:mm:ss.fff");	//mm-dd才显示08-01，否则显示8-1
//fff个数表示小数点后显示的位数，这里精确到小数点后3位
```

**注意：一定要注意大小写！！！M是月份，写成了m就是分钟数了！！**

### （2）DateTime转String

DateTime转string：https://www.cnblogs.com/JiYF/p/7831547.html

### （3）String转DateTime

string转DateTime：https://www.cnblogs.com/Pickuper/articles/2058880.html

