# 颜色空间

## 1. 常见颜色空间

* RGB空间
* YUV空间：Y——亮度，U——红色分量和亮度的差值，V——蓝色和亮度的差值
* HSV空间：H——色度，S——饱和度，V——亮度
* GRAY空间：灰度图像，从0到MAX依次表达黑到白
* Lab空间

## 2. 颜色空间转换——cvtColor

```c++
void cv::cvtColor(Mat& src, Mat& dst, int flag);	//flag为转换标志
```

> flag：标志参数
>
> COLOR_BGR2RGB
>
> COLOR_BGR2YUV
>
> ...

```c++
src.convertTo(Mat& dst, typename)	//typename为CV_32F等
```

* 关于用COLOR_BGR2GRAY和用imread配合IMREAD_GRAYSCALE所得灰度图不同问题

  **一律采用默认参数的imread，然后使用convertTo和COLOR_BGR2GRAY的方法。另一种方法由于库的问题，存在偏差。**

  https://www.cnblogs.com/ybqjymy/p/14648175.html

* 另外，源图为灰度图时使用imread读取到的并不是CV_8UC1，而是CV_8UC3，是三通道的！只不过三通道的像素值相同。

## 3.图像的通道分离——split

将图像的各通道的分量分离出来，分别处理。处理完后再将各通道合并。

```c++
split(Mat& src, Mat* mv)	//mv为Mat[]类型，长度为src.channels()，每个Mat存一个分量
split(Mat& src, vector<Mat>)	//vector不需要知道通道数
```

```c++
vector<typename> v;
typename* p = v.data();
for(int i=0;i<v.size();i++){
	cout<<p[i]<<endl;
}
```

## 4.图像的通道合并——merge

**在用法上来说，不一定是图像的通道合并，本质是Mat的通道合并**

```c++
uchar[256] arrLut1,arrLut2,arrLut3;
//给三个数组赋值省略
Mat lut1(1,256,CV_8UC1,arrLut1);
Mat lut2(1,256,CV_8UC1,arrLut2);
Mat lut3(1,256,CV_8UC1,arrLut3);
vector<Mat> temp;
temp.push_back(lut1);
temp.push_back(lut2);
temp.push_back(lut3);
Mat dst;
merge(temp,dst);	//将三个矩阵合并成3通道的一个矩阵
```

```c++
merge(Mat* mv, int channels, Mat& dst);	//数组需要传入长度channels作为参数	
merge(vector<Mat> mv, Mat& dst)
//mv中存储的图像的通道数可以不同，如C1+C1+C3,5通道。
```

# 同一图像像素操作

## 1. 灰度值

矩阵中每个数值表示相应位置的像素的灰度值，表征像素的亮暗程度。

最大值：表达灰度值最大的元素

平均值：表达图像整体亮暗程度

方差：表达图像的亮暗程度的差距

## 2.最值——minMaxLoc

```c++
//src必须是单通道
void cv::minMaxLoc(Mat src, double* minPtr, double* maxPtr, Point* minLoc, Point* maxLoc, Mat mask);
//若src为多通道，需用Mat::reshape()转化为单通道。
src.reshape(int afterChannels, int afterRows);	//afterChannels是想要的图像的通道数，afterRows是想要的行数
```

```c++
Mat A(3,4,CV_8UC3，Scalar(1,2,3));
double min=0.0;
double max=0.0;
Point minPoint,maxPoint;
minMaxLoc(A,&min,&max,&minPoint,&maxPoint);	
cout<<"min= "<< minPoint << min<<", max= "<< maxPoint << max<<endl;	//重载cout输出Point
```

## 3. 均值——mean

```c++
Scalar mean(Mat& src,Mat& mask);
//mask表示掩膜，src经过mask后保留mask中非0部分，去掉为0部分
//Scalar保存各个通道的mean，Scalar[index]访问某个通道的mean
```

## 4. 标准差—— meanStdDev

```c++
void meanStdDev(Mat& src, Mat& mean, Mat& stddev, Mat& mask);
//注意：返回值为void
```

# 两图像间的像素操作

## 1. 比较运算

对两幅图像的每个像素作比较，保留较大值/较小值，生成新的图像。

```c++
void cv::max(Mat& src1, Mat& src2, Mat& dst);
void cv::min(Mat& src1, Mat& src2, Mat& dst);
```

## 2. 逻辑运算

* 以像素为单位进行运算，二值图像（图像的二维矩阵由0/1构成，像素的数据类型为bit）像素灰度值只有0/1，0代表黑1代表白（灰度图：二值图像的进化版本，彩色图像的退化版本，B=G=R三个分量的值相等，因此只用uchar 8bit就可以存储一个像素。颜色除了黑白之外，还有多层的中间灰色），按位进行运算。

* 非二值图像，需将灰度值转换成二进制数，再按位运算。
* 多通道独立运算。

```c++
bitwise_and(Mat& src1, Mat& src2, Mat& dst, Mat& mask);
bitwise_or(Mat& src1, Mat& src2, Mat& dst, Mat& mask);
bitwise_not(Mat& src1,Mat& dst, Mat& mask);
bitwise_xor(Mat& src1, Mat& src2, Mat& dst, Mat& mask);
```

## 3. 二值化——threshold

* 二值图像，像素灰度值只有最大值和最小值。

* 简单全局阈值，整个图像由该阈值设置二值。

  ```c++
  //简单的全局阈值
  cv::threshold(Mat& src, Mat& dst, double threshold, double maxVal, int type);
  ```
  
> threshold：全局阈值
  >
  > maxVal：灰度最大值
  >
  > type：枚举类型。5种**二值化方法** ，人为设定阈值。2种**阈值获取方法** 。   
  >
  > THRESH_BINARY  黑白二值
  >
  > THRESH_BINARY_INV	黑白二值反转
  >
  > THRESH_OTSU
  >
  > THRESH_TRIANGLE

* 自适应阈值。**动态**的设定某像素的阈值，由其**邻域**内均值/像素加权和等属性获得阈值。

  ```c++
  void cv::adaptiveThreshold();	
  ```

  ## 4. LUT

  显示查找表

  原图像和二值图像的像素的灰度值之间形成映射，设置成一张表，形式上是1×Mat.total()的一个矩阵。

  通过LUT函数生成二值图。
  
  ```c++
  void LUT(Mat& src, Mat& LUT, Mat& dst);
  //LUT为单通道时，src的每个通道都按照LUT的一个通道处理
//LUT为多通道时，src的第i个通道按照LUT的第i个通道处理
  ```
  
  ```c++
  //LUT表使用例程
  uchar lut1[256];
  for(int i=0;i<256;i++){
      if(i<=100)
          lut1[i]=0;
      if(i>100&&i<=150)
          lut1[i]=100;
      if(i>150&&i<=200)
          lut1[i]=150;
      if(i>200)
          lut1[i]=255;
  }
  //以数组初始化Mat
  Mat lutMat(1,256,CV_8UC1,lut1);
  LUT(src, lutMat, dst);
  ```


# 图像操作

## 1.图像拼接——vconcat/hconcat

左右连接：要求高度相同（cols同）

上下连接：要求宽度相同

```c++
void cv::vconcat(Mat& src1, Mat& src2, Mat& dst);	//垂直拼接
void cv::hconcat(Mat& src1, Mat& src2, Mat& dst);	//水平拼接
```

## 2. 尺寸变换——resize

```c++
void cv::resize(Mat& src,Mat& dst,Size size,double fx, double fy, int flag;);
```

> size：输出矩阵的尺寸（优先度高于fx/fy。当两者缩放比例不同时有限size）
>
> fx：水平轴的缩放系数
>
> fy：垂直轴的缩放系数
>
> flag：枚举，插值方法标志
>
> 一般，缩小图像用INTER_AREA，放大图像用INTER_CUBIC/INTER_LINEAR

## 3.图像翻转——flip

```c++
void cv::flip(Mat& src, Mat& dst, int flag);
```

> flag表示翻转方向
>
> flag=0，x轴
>
> flag>0，y轴
>
> flag<0，先x轴后y轴

## 4.仿射变换——warpAffine

* 仿射变换=线性变换+平移变换。又叫3点变换。

  仿射变换矩阵：[*A* ,*B* ]2×3

  其中*A* 是2×2的线性变换矩阵，*B* 是2×1的平移矩阵。

* 由给定中心、角度进行变换

```c++
//先计算仿射变换矩阵
Mat cv::getRotationMatrix2D(Point2f center, double angle, double scale)；
```

> center：旋转中心
>
> angle：逆时针为正，单位为度
>
> scale：水平轴和垂直轴的缩放系数。不缩放设为1

```c++
//再进行仿射变换
void cv::warpAffine(Mat& src, Mat& dst, Mat& transMat, Size size, int, int, const Scalar&)；
```

* 由变换前后的图像，各取3个点，计算得到变换矩阵。再由warpAffine函数计算变换。

``` c++
Mat cv::getAffineTransform(Point2f src[],Point2f dst[]);	//返回值为变换矩阵
//src[] dst[]按照点的对应顺序存储3个点
```



## 5. 透视变换——warpPerspective

* 将相机视场与地面存在倾斜而产生的图像畸变，经过透视变换实现图像校正。
* 3×3的变换矩阵。可由变换前后的各4个点确定。

```c++
Mat cv::getPerspectiveTransform(const Point2f src[], const Point2f dst[], int flag);
void cv::warpPerspective(Mat& src, Mat& dst, Mat& matTrans, Size size, int, int);
```

## 6. 极坐标变换——warpPolar

将圆形图像变换为矩形图像，从而将圆形图案边缘的文字垂直的排列在矩形图像的边缘，便于识别。

``` c++
void cv::warpPolar(Mat& src, Mat& dst, Size size, Point center, double maxRadius, int flag);
```

> size：变换后图像大小
>
> center：极坐标原点在原图中的位置
>
> maxRadius：
>
> flag：
>
> WARP_POLAR_LINEAR  极坐标变换（圆变成矩形）
>
> WARP_POLAR_MAP       逆变换（矩形变成圆）

# 在已有图像上绘制几何图形

## 1. 圆——circle

```c++
void circle(Mat& img, Point& center, double radius, Scalar color, int thickness, int lineType, int shift)
```

> img：图像
>
> center：圆心
>
> radius：半径
>
> color：颜色
>
> thickness：粗细
>
> lineType：线型
>
> shift：小数位数

## 2.直线——line

```c++
void cv::line(Mat& src, Point p1, Point p2, const Scalar& color, int thickness, int lineType, int shift);
```

> p1,p2：两点确定直线

## 3. 椭圆——ellipse

```c++
void cv::ellipse(Mat& src, Point center, Size axes, double angle, double startAngle, double endAngle, const Scalar& color, int thickness, int lineType, int shift);
```

>center：圆心
>
>axes：(长轴/2，短轴/2)
>
>angle：椭圆的旋转角度（x轴为0度，第四象限为正，第一象限为负）
>
>startAngle：圆弧的起始角度
>
>endAngle：圆弧的终止角度

center和axes唯一确定椭圆

angle确定椭圆的旋转

startAngle、endAngle确定圆弧

```c++
void cv::ellipse2Poly(Point center, Size axes, double angle, double startAngle, double endAngle, int delta, std::vector<Point>& pts);
//该函数不直接显示椭圆，将椭圆边缘的点存储在pts中，以供后续使用
```

> pts：椭圆边缘的像素点的集合

## 4.文字——putText

```c++
void cv::putText(Mat& src, const String& text, Point org, int fontFace, double fontScale, Scalar color, int thickness, int lineType, bool bottomLeftOrigin);
```

> text：内容
>
> org：文字的左下角像素的位置
>
> fontFace：字体
>
> fontScale：字体大小
>
> bottomLeftOrigin：默认false，图像原点在左上角。true，原点在左下角。

## 5. 多边形——rectangle

* 矩形

```c++
void cv::rectangle(Mat& img, Point p1, Point p2, const Scalar& color, int, int, int);
void cv::rectangle(Mat& img, Rect rect, const Scalar& color, int, int, int);
```

> p1,p2：矩形对角线的两个点
>
> rect：数据结构Rect同Vec、Point等类型。(矩形左上角点的x/y，矩形在横轴上的长度，矩形在纵轴上的长度)

* 任意多边形

```c++
void cv::fillPoly(Mat& img, const Point** arr1, const int* arr2, int n, const Scalar& color, int, int, Point);
```

> arr1：二维数组，存n个多边形的每个顶点
>
> n：多边形个数
>
> arr2：存n个多边形，各个的顶点个数

# 图像截取

有时，需要关注的只是图片的一部分，整个图片读取消耗资源，所以截取图片的一部分感兴趣区域ROI进行处理。

```c++
img(Rect(double x, double y, double width, double height));	
img(Range(row_start,row_end),Range(row_start,row_end));
```

# 图像深拷贝——copyTo

copyTo和clone都是深拷贝，不但会拷贝矩阵头还会拷贝矩阵本身。

```c++
void cv::Mat::copyTo(Mat& img_copy) const;	//const表明调用该函数的对象为const
//void cv::Mat::copyTo(Mat& img_copy, Mat& mask) const;
Mat imgSrc;
Mat imgCp;
imgSrc.copyTo(imgCp);
```

```c++
void cv::copyTo(Mat& img, Mat& img_copy, Mat& mask);
```

```c++
Mat Mat::clone()
Mat imgSrc;
Mat imgCp = imgSrc.clone();
```

构造函数和=都浅拷贝，只拷贝矩阵头，矩阵部分是共享的。

```c++
Mat imgCp(const Mat& imgSrc);
Mat imgCp = imgSrc;
```



# Gauss/Laplace金字塔



# 窗口交互

## 1.回调函数

https://www.zhihu.com/question/19801131

主程序A调用库函数B，B却需要处于应用层的A传给它一个函数C作为参数，那么这个C就是回调函数，将C传入B的过程叫做登记回调函数。

![](https://www.hualigs.cn/image/60822bea4daac.jpg)

Library function处于底层，Application program处于应用层。调用回调函数的过程就是：高层调用底层，随后底层又回过头调用高层中的函数，所以叫做“回调”。

## 2.滑动条——createTrackBar

https://www.cnblogs.com/geek-hao/p/11668248.html

```c++
//先创建窗口
namedWindow("winName");
//添加滑动条
createTrackBar("barName","winName",int* value, int maxValue, TrackCallback callBack, void* data);
```

> value：滑动条pos初始值
>
> maxValue：滑动条最大值，代表回调函数中的pos的范围是[0,maxValue]
>
> callBack：回调函数。类型是TrackCallback，该类型实质上是typedef (*TrackCallback)(int,void\*)
>
> data：传入回调函数中的数据，类型是void*

```c++
//回调函数
void callback(int pos,void* data)
```

**createTrackBar函数在后台监听滑动条的动作，一旦滑动条被滑动1次就触发一次回调函数callback，每次callback被触发都会将data重新再次传入callback中**

```c++
//滑动条：双边滤波，高斯滤波
//为了用一个data将几个变量同时传到回调中，用结构体
typedef struct DATA
{
	Mat imgCp;
	int d;
	double sigmaColor;
	double sigmaSpace;

}Data;
Mat imgOut;
int n = 0;

void callBack_d(int pos, void* data) {
	n++;
	imgOut = cv::Mat::zeros(imgOut.rows, imgOut.cols, CV_8UC1);//必须要清空imgOut，否则上次回调执行后imgOut的结果会与下次结果叠加
	bilateralFilter(static_cast<Data*>(data)->imgCp, imgOut, pos, static_cast<Data*>(data)->sigmaColor, static_cast<Data*>(data)->sigmaSpace, BORDER_DEFAULT);
	putText(imgOut, to_string(n), Point(50, 50), cv::FONT_HERSHEY_COMPLEX, 2, Scalar(0, 0, 255), 3, 8);
	imshow("双边滤波", imgOut);
}

void callBack_sigmaColor(int pos, void* data) {
	n++;
	imgOut = cv::Mat::zeros(imgOut.rows, imgOut.cols, CV_8UC1);//必须要清空imgOut，否则上次回调执行后imgOut的结果会与下次结果叠加
	//滑动条滑到0时只是看似图片好像恢复到高斯噪声的图片，实际上是因为sigmaColor=0时滤波效果太差
	bilateralFilter(static_cast<Data*>(data)->imgCp, imgOut, static_cast<Data*>(data)->d, (double)pos, static_cast<Data*>(data)->sigmaSpace, BORDER_DEFAULT);
	putText(imgOut, to_string(n), Point(50, 50), cv::FONT_HERSHEY_COMPLEX, 2, Scalar(0, 0, 255), 3, 8);
	imshow("双边滤波", imgOut);
}

void callBack_sigmaSpace(int pos, void* data) {
	n++;
	imgOut = cv::Mat::zeros(imgOut.rows, imgOut.cols, CV_8UC1);//必须要清空imgOut，否则上次回调执行后imgOut的结果会与下次结果叠加
	bilateralFilter(static_cast<Data*>(data)->imgCp, imgOut, static_cast<Data*>(data)->d, static_cast<Data*>(data)->sigmaColor, (double)pos, BORDER_DEFAULT);
	putText(imgOut, to_string(n), Point(50, 50), cv::FONT_HERSHEY_COMPLEX, 2, Scalar(0, 0, 255), 3, 8);
	imshow("双边滤波", imgOut);
}

void callBack_gaussBlur(int pos, void* data) {
	imgOut = cv::Mat::zeros(imgOut.rows, imgOut.cols, CV_8UC1);//必须要清空imgOut，否则上次回调执行后imgOut的结果会与下次结果叠加
	GaussianBlur(*(Mat*)data, imgOut, Size(3, 3), (double)pos, 20, 4);//滑动条滑到0，不会恢复原状，只是在进行均值为0的高斯滤波
	//滑动条滑到0处，图片复原到有高斯滤波前的状态
	/*if (pos == 0) {
		static_cast<Mat*>(data)->copyTo(imgOut);
	}*/
	imshow("高斯滤波", imgOut);
}

void func13() {
	Data data;
	img2.copyTo(data.imgCp);
	if (data.imgCp.empty()) {
		cout << "wrong file.." << endl;
	}
	gaussNoise(data.imgCp, data.imgCp, 10, 20);//给原图添加高斯噪声
	//saltAndPepper(data.imgCp, 10000);//添加椒盐噪声
	imshow("高斯噪声img", data.imgCp);
	int n = 11;
	//高斯滤波
	double sigmaX = 10.0;//高斯滤波标准差，XY方向用同一个
	double sigmaxMax = 50.0;//标准差最大值
	namedWindow("高斯滤波");
	imshow("高斯滤波", data.imgCp);
	createTrackbar("高斯滤波均值", "高斯滤波", (int*)&sigmaX, sigmaxMax, callBack_gaussBlur, &data.imgCp);//sigmaX作为初值，每次触发回调都将imgCp传入回调。
	//双边滤波
	int d = 5;//初值
	double sigmaColor = 20.0;
	double sigmaSpace = 20.0;
	int dMax = 10;//最大值
	double sigmaColorMax = 150.0;
	double sigmaSpaceMax = 150.0;
	data.d = d;
	data.sigmaColor = sigmaColor;
	data.sigmaSpace = sigmaSpace;
	namedWindow("双边滤波");
	imshow("双边滤波", data.imgCp);
	createTrackbar("d", "双边滤波", &d, dMax, callBack_d, &data);
	createTrackbar("sigmaColor", "双边滤波", (int*)&sigmaColor, sigmaColorMax, callBack_sigmaColor, &data);
	createTrackbar("sigmaSpace", "双边滤波", (int*)&sigmaSpace, sigmaSpaceMax, callBack_sigmaSpace, &data);
	waitKey();
}
```



## 3.鼠标事件 ——setMouseCallback

```c++
void cv::setMouseCallback(const string& winName, MouseCallback onMouse, void* userdata);
//winName窗口名
//MouseCallback回调函数/鼠标事件响应函数
//userdata传递给回调的外部参数
```

```c++ 

typedef void(*cv::MouseCallback)(int event, int x, int y, int flags , void* userdata);
//event：单击/双击 左键/右键/中键
//x：鼠标触发指定事件时的x坐标
//y：y坐标
//flags：左/右/中键拖曳，Ctrl、Shift、Alt键等
//userdata：接受来自setMouseCallback的外部参数
```

https://blog.csdn.net/qq_29540745/article/details/52562101





























