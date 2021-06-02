# 一些总结

[OpenCV踩过的坑](https://blog.csdn.net/weixin_42142612/article/details/100919691)

# Mat类

**Mat 是一个类，由两个数据部分组成：矩阵头（包含矩阵尺寸，存储方法，存储地址等信息）和一个指向存储所有像素值的矩阵（根据所选存储方法的不同矩阵可以是不同的维数）的指针。**

矩阵头的尺寸是常数值，但矩阵本身的尺寸会依图像的不同而不同，通常比矩阵头的尺寸大数个数量级。因此，当在程序中传递图像并创建拷贝时，大的开销是由矩阵造成的，而不是信息头。所以，除非有必要，否则我们不应该拷贝大的图像，因为这会降低程序速度。

为了搞定这个问题，OpenCV使用引用计数机制。其思路是让每个 Mat 对象有自己的信息头，但共享同一个矩阵。这通过让矩阵指针指向同一地址而实现。而拷贝构造函数则 只拷贝信息头和矩阵指针 ，而不拷贝矩阵。但某些时候你仍会想拷贝矩阵本身(不只是信息头和矩阵指针)，这时可以使用函数 clone() 或者 copyTo() 。

从上面的话我们可以看到，copyTo函数、clone函数拷贝的不仅仅是信息头，还有矩阵本身，而“= ”运算符与拷贝构造函数仅仅拷贝了信息头，他们指向的其实是一个矩阵。

## 1.Mat创建

* 默认构造

  ```c++
  cv::Mat A;	//根据赋值自动调整矩阵大小。一般用于存图像或接受计算结果
  ```

* 有参构造

  ```c++
  cv::Mat(int rows, int cols, typename)	
  cv::Mat(Size size, typename)	//注意，size变量中存储rows和cols的形式是(cols,rows)
  ```

* 拷贝构造

  ```c++
  cv::Mat(const Mat& m);
  ```

* 截取子矩阵

  ```c++
  cv::Mat(const Mat& m, Range(subRows,subCol));
  ```

## 2.Mat属性

* 空矩阵：empty()==true;

* data：uchar*类型，指向数据矩阵首地址。

* dims：矩阵维度。

* depth：每个像素的每个通道的精度。depth数值越大精度越高，对应0~6。

  ```c++
  enum{CV_8U=0,CV_8S=1,CV_16U=2,CV_16S=3,CV_32S=4,CV_32F=5,CV_64F=6}  
  ```

* type：数据类型，如CV_8U。

## 3. Mat类运算

```c++
cv::Mat j,m,c,d;
double k;
j=c*d;	//矩阵乘法
k=c.dot(d);		//矩阵点乘
m=c.mul(d);		//矩阵对应元素相乘。0~255，易产生数据溢出，注意。
```

## 4. Mat类在内存中的存储方式

* CV_8U的数据是以uchar类型存储的，输出时记得转成int。

* 每个元素的各个通道的元素在内存中依次存储，因此只要找到每个元素起始的位置，就可以找到这个元素每个通道的位置。

* Mat的一些属性。

  > cols       矩阵列数（列的元素数）
  >
  > rows      矩阵行数（行的元素数）
  >
  > total()     矩阵元素总数
  >
  > channels()     矩阵每个元素的通道数
  >
  > elemSize()     每个元素的字节数
  >
  > elemSize1()	每个元素的每个通道的字节数
>
  > step       矩阵宽度（字节数）/矩阵每行的步长

  ```c++
  total()=cols*rows
  
  step=elemSize()*cols
  
  elemSize()=channels()*sizeof(typename);  //每个元素的字节数=通道数×每个通道的数据类型的字节数
  
  elemSize1()=elemSize/channels()=sizeof(typename);
  ```

## 5. Mat赋值

* Mat.at<CV_8UC1>(row,col)=val;

* 列表

  ```c++
  Mat a = (Mat_<uchar>(2,3) << 1,2,3,4,5,6);
  ```

* 数组

  ```c++
  int arr[] = {1.0,2.0,3.0,4.0,5.0,6.0};
  Mat a = Mat(2,3,CV_32FC1,arr);
  ```



## 6. Mat中元素的访问

* at访问

  **Mat::at<typename>(行，列)**;

  注意：访问的像素点所在行列，对应的坐标是x=列，y=行。所以用坐标访问的话为.at<typename>(y, x)。

  Point构造时以坐标构造，Point(x,y)。

  ```C++
  //单通道，每个元素读取出来就是数据
  cv::Mat A= cv::Mat_<uchar>(3,4);	//3*4单通道，每个元素uchar
  int val = (int)A.at<uchar>(0,1);	//注意加上Mat中存储的数据类型
  A.at<uchar>(3,4)=2;   //将3行4列处的元素设为2
  
  //多通道，元素取出来是Vec类型
  cv::Mat A(3,4,CV_8UC3, Scalar(0,0,1));	//3通道uchar，赋值(0,0,1)
  cv::Vec3b vc3_1 = A.at<CV_8UC3>(1,1);	//取出a在(1,1)位置的Vec 
  int val1 = (int)vc3_1[0]; //从vc3_1中分别取出3个通道的数据
  int val2 = (int)vc3_1[1];	//Vec的访问也可以直接通过[index]
  int val3 = (int)vc3_1[2];
  ```

* 迭代器访问

  Mat是容器，可用迭代器访问元素。

  ```c++
  //迭代器类型
  cv::MatIterator_<>	//类型是cv::MatIterator_
  cv::begin<>()
  cv::end<>()
  ```

  ```c++
  //单通道
  cv::Mat B(3, 4, CV_8UC1, Scalar(2));
  	for (cv::MatIterator_<uchar> it = B.begin<uchar>(); it != B.end<uchar>(); it++) {
  		std::cout << static_cast<int>(*it) << " ";
  		if ((++i%A.cols) == 0) {
  			std::cout << std::endl;
  		}
  	}
  //多通道，注意迭代的数据类型是Vec。
  cv::Mat A(3, 4, CV_8UC3);
  	int i = 0;
  //通过迭代器赋值
  	for (cv::MatIterator_<Vec3b> it = A.begin<Vec3b>(); it != A.end<Vec3b>(); it++) {
  		(*it)[0] = 1;
  		(*it)[1] = 2;
  		(*it)[2] = 3;
  	}
  //输出
  	for (cv::MatIterator_<Vec3b> it = A.begin<Vec3b>(); it != A.end<Vec3b>(); it++) {
  		std::cout << (int)(*it)[2];
  		if ((++i%A.cols) == 0) {
  			std::cout << std::endl;
  		}
  	}
  ```
  
  >向量Vec
  >
  > i表示int
  >
  >f表示float
  >
  >d表示double
  >
  >b表示uchar
  >
  >s表示short
  >
  >w表示ushort
  >
  >
  
* 指针访问

  指针方式访问速度更快些，ptr[index]。

  **Mat类成员函数ptr()** 

  Mat.ptr(row)返回的是第row行的首地址，作为数组访问。
  
  ```c++
  //prt常用形式
  typename val = Mat.prt<typename>(row)[col]	//通过(row,col)直接访问元素
  //单通道遍历
  for(int i=0;i<B.rows;i++){
      typename* p = Mat.prt<typename>(row);	//定义指针p指向行首
      for(int j=0;j<B.cols;j++){
          p[j]=(i+j)%255;	//p[j]或*(p+j)访问行中元素
      }
  }
  //多通道遍历
  for(int i=0;i<A.rows;i++){
      Vec3b* p = Mat.prt<Vec3b>(row);	//定义指针p指向行首
      for(int j=0;j<A.cols;j++){
          p[j][0]=(i+j)%255;	//p[j]是一个Vec3b，依次访问3个通道
          p[j][1]=(i+j)%255;
          p[j][2]=(i+j)%255;
      }
  }
  ```

## 7. 矩阵数据类型

```c++
Mat<uchar>                            CV_8U               0

Mat<char>                             CV_8S               1

Mar<short>                            CV_16S              2

Mat<ushort>                           CV_16U              3

Mat<int>                              CV_32S              4

Mat<float>                            CV_32F              5

Mat<double>                           CV_64F              6
```

## 8. 数据类型转换——convertTo()

```c++
#include <core.hpp>
void convertTo( matOut m, int rtype, double alpha=1, double beta=0 ) const;
```

> rtype：要转换的数据类型/深度
>
> alpha：缩放系数
>
> beta：增量

注意：将CV_8UC1的Mat转换为CV_64FC1时，正确的方法是：

```c++
convertTo(Mat, CV_64FC1, 1/255.0)；
```

OpenCV的convertTo函数如果第三个参数使用默认的话，就会导致数值只是类型的改变，在CV_8U中像素灰度值范围在[0,255]之间，但在浮点数表示的颜色空间中数值范围是[0,1.0]。显然按你这样调用的话，基本上所有像素都转换成大于1的浮点数，截断后当然也就是一片白色区域了。所以缩放系数alpha设置为1/255，将CV_8U中的数据都变换到[0,1.0]之间。

# 基本数据类型

## 1. Point

```c++
typedef Point_<int> Point2i;     //int类型Point_
typedef Point_<float> Point2f;   //flota类型Point_
typedef Point_<double> Point2d;   //double类型Point_
typedef Point2i Point;         //int类型Point,因为int类型使用相对多些，将Point2i取别名Point
```

Point默认是Point2i，Point(int x,int y)

Point2f：2維浮點數點類別，通常用於幾何計算，用法和Point雷同，只是x和y是浮點數。

Point3i、Point3f、Point3d代表3維點x、y、z，而成員型態分別為int、float、double。

## 2. Size

尺寸類別，成員有width和height，分別表示寬和長(int型態)，可用area()函式得到面積，以下為建構式最基本兩種賦值方法：

```c++
Size(int width, int height) //建構式
Size size1(150, 100);
Size size2;
size2.width = 150;
size2.height = 100;
int myArea = size2.area();
```

Size2f：尺寸類別，和Size用法雷同，只是width和height為float型態。

## 3. Vec

Vec3b

数字表示向量长度，字母表示向量存储的数据类型

uchar->b

float->f

int->i

double->d

short->s

ushort->w



## 4. Rect

矩形類別，成員有x、y、width、height，分別表示左上角頂點的x座標，左上角頂點的y座標，矩形寬、矩形高，可用area()函式得到面積，以下為建構式和最基本的兩種賦值方法：

```c++
Rect(int x, int y, int width, int height) //建構式Rect rect1(20,30,150,100);
Rect rect2;
rect2.x = 20;
rect2.y = 30;
rect2.width = 150;
rect2.height = 100;
int myRectArea = rect2.area();
```

## 5. Scalar

代表4元素的向量，一般用於像素顏色，以下設定給灰階圖和彩色圖像素的方式：

- Scalar(a, b, c)：省略透明通道，由於OpenCV的彩色影像通常為BGR的順序，a代表藍色、b代表綠色、c代表紅色。

- Scalar(a)：通常用於灰階圖，像素強度為a。

# 图像读取与显示

## 1.读取——imread

```c++
cv::Mat cv::imread(const string& path, int flags);
```

* imread()当读取图片失败时返回一个**空矩阵** ，矩阵的data属性为空，**empty()为1。** 

* 图像的读取使用编解码器，不同系统的编解码器不同。Win/MacOs使用OpenCV自带编解码器，Linux需要自行安装。

  但BMP和DIB文件在任何系统都始终可以读取。

* 读取图片实质上是根据内容而不是扩展名。

* flag可设置读取方式。

## 2.窗口——namedWindow

```c++
void cv::namedWindow(const string& winname, int flags=WINDOW_AUTOSIZE);
```

* 若不手动创建窗口，显示图片时会自动创建窗口。但有时需要在显示之前，先添加滑动条、状态栏等。就需要提前创建窗口，再载入图片。

* winname唯一标识窗口。若已有同名窗口，则该函数不做操作。

* flag设置窗口属性。默认无法调整窗口大小。

  > WINDOW_AUTOSIZE     根据图像自动调整窗口大小，不允许用户调整
  >
  > WINDOW_NORMAL	用户可随意调整窗口大小
  >
  > WINDOW_KEEPRATIO	保持图像比例
  >
  > WINDOW_GUI_EXPANDED	创建允许添加工具栏和状态栏的窗口

* 销毁窗口

  ```
  cv::destroyWindow()
  cv::destroyAllWindow()	//关闭、释放所有窗口资源，一般用在程序结束
  ```
  
## 3.显示——imshow

```c++
  void cv::imshow(const string& winname，InputArray mat);
```

* InputArray可以接受Mat/vector等。
* imread()执行完后会继续执行后续代码。加上cv::waitKey()。无参/0表示等待用户按下按键。参数是等待ms数。
* 若以winname为名的窗口之前未创建，则会以WINDOW_AUTOSIZE创建一个窗口。

# 视频的读取/调用摄像头

## 1.视频读取

```c++
//第一种读取方法：默认构造+.open()
cv::VideoCapture()::VideoCapture();	//默认构造函数
//示例
cv::VideoCapture video;
video.open(const string& path);

//第二种读取方法：构造函数中声明要读取的文件和读取属性
cv::VideoCapture(const string& path, int apiPreference=ANY_CAP);
```

* 读取图像序列

  图像命名：前缀+数字

  VideoCapture中path，用**前缀+%02d** 形式。如：img_00.jpg,img_01.jpg,...，VideoCapture(img_%02d)。

* VideoCapture：读视频的视频流。

* 读取成功与否用：isOpened()函数是否为ture判断。

* 视频文件加载到VideoCapture后还需要导出至Mat变量中：

  video >> frame;

  video.read(frame);	//从video中读取到frame，读取成功返回true

  ```c++
  cv::VideoCapture video("example.mp4");
  cv::Mat frame;
  if(video.isOpened()){
  	while(1){
          video>>frame;	
      	//当video中图像已经全部导入Mat后，再次导入时，Mat为空矩阵，空矩阵empty()为true。
  		if(frame.empty())
  			break;
          /**
      	或者使用这种方式判断是否可以从视频中读出一帧图像/摄像头断开链接
     		 / if(!video.read(frame)){
      	break;
      	}
      	*/	
          imshow("example",frame);
          waitKey(1000/video.get(CAP_PROP_FPS));
      }
      waitKey();
  }
  video.release();	//关闭视频流
  ```

* VideoCapture类中的get()方法，可获取视频的属性。

  >
  >
  >CAP_PROP_FRAME_WIDTH
  >
  >CAP_PROP_FRAME_HEIGHT
  >
  >CAP_PROP_FRAME_FPS			//FPS，帧数/s
  >
  >CAP_PROP_FRAME_COUNT	//总帧数
  >
  >CAP_PROP_POS_MSEC	//视频位置当前位置，单位：ms

* 读取完毕，关闭视频流。

## 2.调用摄像头

```c++
//类似读取视频，唯一不同的是视频文件路径变成了相机的ID，ID编号从0开始
cv::VideoCapture(int index,int apiPreferen)；
```

# 图像和视频的保存

## 1.写图像

``` c++
imwrite(const string& path, Mat& img, std::vector<int> params);
```

> path：写入路径
>
> img：存储要写入图像的Mat
>
> params：现存参数，再存参数值。如params.push_back(IMWRITER_PNG_COMPRESSION);params.push_back(9);

## 2.写视频的视频流

```c++
//第一种打开方式
cv::VideoWriter::VideoWriter()	//默认构造+open(const string& path, int fourcc, double fps, Size imgSize, bool isColor=true);
cv::VideoWriter writer;
writer.open(path, codec, fps, img.size(), isColor)

//第二种打开方式
cv::VideoWriter videoWriter(const string& path, int fourcc, double fps, Size imgSize, bool isColor=true);
```

> path：要写到视频文件的路径
>
> codec：编解码器
>
> fps：帧率
>
> imgSize：读取的图像的大小
>
> isColor：图像是否为彩色

* VideoWriter读取成功由isOpened()是否为true判断

* 将图像导入视频流：

  wirter << frame；

  writer.write(frame)；//将图像frame写入视频流writer中。写入成功返回true。

* 保存视频文件结束后，关闭视频流

  ```c++
  writer.release();
  ```

## 3. 保存/读取XML文件和YAML文件

* 扩展名".xml"和".yaml"

* 结构化语言

  ```xml
  <color><red>30</red><blue>100</blue></color>
  ```

* 关联类

  FileStorage

  ```c++
  cv::FileStorage(const String& path, int flags, const String& encoding);
  ```

  > path：文件路径
  > flags: FileStorage::READ
  > FileStorage::WRITE
  > FileStorage::APPEND
  > FileStorage::MEMORY
  >
  > encoding：编码格式，一般为UTF-8 XML

关联成功：isOpened()为true

* 写文件

  注意：writer(string& valName, val)

  ```c++
  string filePath = "C:\\Users\\Administrator\\Desktop\\data.xml";
  	//写关联
  	FileStorage fwriter;
  	fwriter.open(filePath, FileStorage::WRITE);
  	
  	Mat A = Mat::ones(3, 4, CV_8U);
  	fwriter.write("A", A);	//write("变量名",变量值)。fwriter<<"A"<<A
  
  	double x = 1.0;
  	fwriter << "x" << x;	//注意，变量名以字符串的形式输入
  
  	fwriter << "arr" << "[" << 1 << 2 << 3 << "]";
  	fwriter << "multi_nodes" << "{" << "year" << 2021 << "month" << 4 << "day" << 19 << "time" << "[" << 10 << 02 << "]" << "}";
  
  	fwriter.release();	//释放
  ```

* 读文件

  ```c++
  //读关联
  	FileStorage freader;
  	freader.open(filePath, FileStorage::READ);
  	if (!freader.isOpened()) {
  		cout << "open file error.." << endl;
  	}
  	//读double	
  	double val = 0.0;
  	freader["x"] >> val;	//通过[""]访问变量
  	cout << val << endl;
  	//读数组
  	FileNode fileNode = freader["arr"];	//注意：定义FileNode不要混淆用<<
  	cout << "[ ";
  	for (auto it = fileNode.begin(); it != fileNode.end(); it++) {
  		//int temp;
  		//*it >> temp;
  		//cout << temp << ", ";
  		cout << (int)(*it) << ", ";	//从文件中取数据要设定数据的类型，用对应类型的变量去接，或者用强转。
  	}
  	cout << "] ";
  	//读含多个子节点的节点数据
  	FileNode fileNode_01 = freader["multi_nodes"];
  	cout << "time=" << (int)fileNode_01["time"][0] << (int)fileNode_01["time"][1];
  ```

  



































































