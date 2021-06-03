# C#使用NPOI库操作Excel

https://blog.csdn.net/love_kevin/article/details/79891512?ops_request_misc=&request_id=&biz_id=102&utm_term=C&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-.first_rank_v2_pc_rank_v29&spm=1018.2226.3001.4187

https://www.cnblogs.com/restran/p/3889479.html

https://www.cnblogs.com/wanzhongjun/p/7151514.html

https://blog.csdn.net/WuLex/article/details/108914907

创建Excel文件并写入：https://www.pianshen.com/article/9409729801/

## 头文件

> ICSharpCode.SharpZipLib.dll
>
> NPOI.dll
>
> NPOI.OOXML.dll
>
> NPOI.OpenXml4Net.dll
>
> NPOI.OpenXmlFormats.dll



## 从Excel中读取

### （1）读取.xlsx



## 日期格式

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

