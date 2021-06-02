# 图像形态学操作

## 1. 概念

voronoi：https://zhuanlan.zhihu.com/p/144477570

https://zhuanlan.zhihu.com/p/101616805

https://www.cnblogs.com/luo-peng/p/5612449.html

https://blog.csdn.net/keen_zuxwang/article/details/72768092

* 图像形态学操作

  忽略图像纹理信息，关注区域信息，为了加快形态学处理速度，通常将图像转换为**二值图**后再进行形态学操作。

* 算法原理

  两个滤波器的作用范围是由结构元素定义的像素集，在某个像素上应用结构元素时，结构元素的锚点与该像素对齐，所有与结构元素相交的像素就包含在当前集合中。腐蚀就是把当前的像素替换成所定义像素集合中的最小像素值（物体尺寸会缩小）；膨胀是腐蚀的反运算（物体会扩大）。由于输入的二值图像只包含黑色（0）和白色（255）像素，因此每个像素都会替换成白色或黑色像素。 

* 连通域

  https://www.cnblogs.com/ronny/p/img_aly_01.html

  二值图像，顾名思义就是图像的亮度值只有两个状态：黑(0)和白(255)。二值图像在图像分析与识别中有着举足轻重的地位，因为其模式简单，对像素在空间上的关系有着极强的表现力。在实际应用中，很多图像的分析最终都转换为二值图像的分析，比如：医学图像分析、前景检测、字符识别，形状识别。二值化+数学形态学能解决很多计算机识别工程中目标提取的问题。

  二值图像分析最重要的方法就是连通区域标记，它是所有二值图像分析的基础，它通过对二值图像中白色像素（目标）的标记，让每个单独的连通区域形成一个被标识的块，进一步的我们就可以获取这些块的轮廓、外接矩形、质心、不变矩等几何参数。

   在我们讨论连通区域标记的算法之前，我们先要明确什么是连通区域，怎样的像素邻接关系构成连通。在图像中，最小的单位是像素，每个像素周围有8个邻接像素，常见的邻接关系有2种：4邻接与8邻接。4邻接一共4个点，即上下左右，如下左图所示。8邻接的点一共有8个，包括了对角线位置的点，如下右图所示。 

 ![image](https://images0.cnblogs.com/blog/378920/201403/132319422711680.png)  ![image](https://images0.cnblogs.com/blog/378920/201403/132319430916793.png) 

如果像素点A与B邻接，我们称A与B连通，于是我们不加证明的有如下的结论：

如果A与B连通，B与C连通，则A与C连通。

在视觉上看来，彼此连通的点形成了一个区域，而不连通的点形成了不同的区域。这样的一个所有的点彼此连通点构成的集合，我们称为一个连通区域。

 下面这符图中，如果考虑4邻接，则有3个连通区域；如果考虑8邻接，则有2个连通区域。（注：图像是被放大的效果，图像正方形实际只有4个像素）。 

 ![image](https://images0.cnblogs.com/blog/378920/201403/132319436732152.png) 

**4邻接和8邻接的区别就是是否承认处于对角线的点是否是邻接。**

## 2. 连通域中心/框出连通域——connectedComponents

https://blog.csdn.net/qingzhuyuxian/article/details/92775728

```c++
int cv::connectedComponents(Mat imgSrc, Mat imgOut, int connectType=8, int ltype=CV_32S)
```

> 返回值表示连通域的数量
>
> connectType：连通域像素邻接方式
>
> ltype：输出图像的数据类型，默认CV_32S，还可是CV_16U

```c++
int cv:::connectedComponents(Mat imgSrc, Mat imgOut, int connectType, int ltype, int ccltype)
```

> imgOut：标记不同连通域后的输出图片
>
> ltype：输出图像的类型，CV_32S或CV_16U
>
> ccltype：算法类型

```c++
int num = cv::connectedComponentsWithStats(Mat imgSrc, Mat imgOut, Mat stats, Mat centroids, int connectType=8, int ltype=CV_32S);
```

> labels：标记连通域后的输出图像
>
> stats：连通域统计信息矩阵stats[num\][5]，个连通域的外接矩形和面积，类型CV_32S，按列分别是x/y/width/height/area
>
> centroids：centroids[num\][2]，各连通域的质心，类型CV_64F
>
> connectType：像素邻接方式，4-邻域，8-邻域
>
> ltype：输出图像数据类型

```c++
//中心坐标
int center_x=centroids.at<double>(i,0);//64位，Typename设为double，其实设为int也可，会在取数据时发生强转
int center_y=centroids.at<double>(i,1);

//矩形框左上角	
int x=stats.at<int>(i,CC_STAT_LEFT)
int y=stats.at<int>(i,CC_STAT_TOP)
int width=stats.at<int>(i,CC_STAT_WIDTH)//矩形框宽
int height=stats.at<int>(i,CC_STAT_HEIGHT)
    
//输出数据类型
#include<typeinfo>
cout<<typeid(centroids.at<double>(1.0))<<endl;//double
cout<<typeid(centroids.at<int>(1,0))<<endl;//int
```

```c++
	Mat imgCp;
	img.copyTo(imgCp);
	Mat imgGray;
	cvtColor(imgCp, imgGray,COLOR_BGR2GRAY,0);//灰度化
	Mat imgBW;
	threshold(imgGray, imgBW, 50, 255, THRESH_BINARY);//二值化

	Mat labels;
	Mat stats;
	Mat centroids;
	int total = connectedComponentsWithStats(imgBW,labels,stats,centroids,8,4);

	//画图
	RNG rng((unsigned)time(NULL));
	vector<Scalar> colors;
	colors.reserve(total);
	for (int i = 0; i < total;i++) {
		colors.push_back(Scalar(rng.uniform(0, 255), rng.uniform(0, 255), rng.uniform(0, 255)));
	}

	int x, y;
	for (int i = 0; i < total; i++) {
		x = centroids.at<double>(i, 0);
		y = centroids.at<double>(i, 1);
		circle(imgCp, Point(x, y), 2, Scalar(0, 255, 2), 1,8,0);//标记中心
		rectangle(imgCp, Rect(stats.at<int>(i, CC_STAT_LEFT), 
			stats.at<int>(i, CC_STAT_TOP), stats.at<int>(i, CC_STAT_WIDTH), stats.at<int>(i, CC_STAT_HEIGHT)),colors[i],2,8,0);//矩形框标记
	}
	imshow("img", imgCp);
```

## 3. 距离变换——distanceTransform

http://t.zoukankan.com/mtcnn-p-9411967.html

http://www.360doc.com/content/15/0401/10/9509713_459747734.shtml

距离变换是针对二值图像的一种变换，最终将二值图变换为灰度图。二值图像可看作仅仅包含背景(灰度值0)和目标(灰度值1)两种像素，变换结果是灰度图，即距离图像，是二值图的目标上每个像素距其最近的背景像素的距离。距离测度有：欧氏距离、街区距离（d=|x<sub>1</sub>-x<sub>2</sub>|+|y<sub>1</sub>-y<sub>2</sub>|）、棋盘距离（d=max{|x<sub>1</sub>-x<sub>2</sub>|,|y<sub>1</sub>-y<sub>2</sub>|}）

 第二个Mat矩阵参数dst保存了每一个点与最近的零点的距离信息，图像上越亮的点，代表了离零点的距离越远。 可以根据距离变换的这个性质，经过简单的运算，用于**细化字符的轮廓和查找物体中心 **

```c++
void cv::distanceTransform(Mat imgSrc, Mat imgOut, Mat labels, int distanceType, int maskSize, int labelType=DIST_LABEL_CCOMP)
```

> labels：标签矩阵，属于同一个连通域的像素点的标签值相等，离散voronoi图

```c++
void cv::distanceTransform(Mat imgSrc, Mat imgOut, int distanceType, int maskSize)
```

> distanceType：计算距离方法。DIST_L1街区距离，DIST_L2欧氏距离，DIST_L3棋盘距离
>
> maskSize：掩膜大小。欧氏距离选择5*5，另2种选择3\*3

```c++
/*
*轮廓细化
*/
	float maxValue = 0;  //定义距离变换矩阵中的最大值
	Mat imgGray;
	cvtColor(img, imgGray, CV_BGR2GRAY);//变换为灰度图
	imageGray = ~imageGray;  //按位2进制取反
	GaussianBlur(imgGray, imgGray, Size(5, 5), 2); //滤波
	threshold(imgGray, imgGray, 70, 200, CV_THRESH_BINARY); //变换为二值图
	imshow("s", imgGray);
	Mat imageThin(imgGray.size(), CV_32FC1); //定义保存距离变换结果的Mat矩阵
	distanceTransform(imgGray, imgThin, CV_DIST_L2, 3);  //距离变换
	Mat distShow;
	distShow = Mat::zeros(imgGray.size(), CV_8UC1); //定义细化后的字符轮廓
	for (int i = 0; i<imgThin.rows; i++)
	{
		for (int j = 0; j<imgThin.cols; j++)
		{
			if (imgThin.at<float>(i, j) > maxValue)
			{
				maxValue = imgThin.at<float>(i, j);  //获取距离变换的极大值
			}
		}
	}
	for (int i = 0; i<imgThin.rows; i++)
	{
		for (int j = 0; j<imgThin.cols; j++)
		{
             //maxValue与大于1的数的比值，该比值越小则符合的像素点越多，轮廓越粗；比值越大，则轮廓越细
			if (imgThin.at<float>(i, j) > maxValue / 1.9)
			{
				distShow.at<uchar>(i, j) = 255;   //符合距离大于最大值一定比例条件的点设为255
			}
		}
	}
	imshow("Source Image", img);
	imshow("Thin Image", distShow);
```

```c++
//查找中心。中心即图形中距离背景的最小距离最大的点
	float maxValue=0;  //定义距离变换矩阵中的最大值
	Point Pt(0,0);//记录中心点
	Mat image=imread(argv[1]);
	Mat imageGray;
	cvtColor(image,imageGray,CV_RGB2GRAY);
	//imageGray=~imageGray;  //取反
	bitwise_not(imageGray, imageGray);
	GaussianBlur(imageGray,imageGray,Size(5,5),2); //滤波
	threshold(imageGray,imageGray,20,200,CV_THRESH_BINARY); //阈值化	
	Mat imageThin(imageGray.size(),CV_32FC1); //定义保存距离变换结果的Mat矩阵
	distanceTransform(imageGray,imageThin,CV_DIST_L2,3);  //距离变换
	Mat distShow;
	distShow=Mat::zeros(imageGray.size(),CV_8UC1); //定义细化后的字符轮廓
	for(int i=0;i<imageThin.rows;i++)
	{
		for(int j=0;j<imageThin.cols;j++)
		{
			distShow.at<uchar>(i,j)=imageThin.at<float>(i,j);
			if(imageThin.at<float>(i,j)>maxValue)
			{
				maxValue=imageThin.at<float>(i,j);  //获取距离变换的极大值
				Pt=Point(j,i);  //坐标
			}
		}
	}
	normalize(distShow,distShow,0,255,CV_MINMAX); //为了显示清晰，做了0~255归一化
	circle(image,Pt,maxValue,Scalar(0,0,255),3);//以中心点距背景的距离为半径的圆
	circle(image,Pt,3,Scalar(0,255,0),3);//显示中心点
	imshow("Source Image",image);
	imshow("Thin Image",distShow);
```

## 4. 腐蚀——erode

https://www.jianshu.com/p/70fc1d894db2

https://blog.csdn.net/qq_40020632/article/details/88880670

https://www.itdaan.com/keywords/OpenCV%E6%8E%A2%E7%B4%A2%E4%B9%8B%E8%B7%AF+%E5%9B%9B+%E8%86%A8%E8%83%80+%E8%85%90%E8%9A%80+%E5%BC%80%E9%97%AD%E8%BF%90%E7%AE%97.html

形态学的基本运算之一。对二值图像的连通域的计数，可得知图形的个数，但是往往噪声导致的单独像素点也会被看作是连通域，从而影响计数结果。对于这种面积较小的连通域可通过腐蚀消除。

腐蚀——针对img用滤波器邻域中的最小像素值替代锚点对应的目标像素值。

用结构元素的中心点对准当前正在遍历的这个像素，然后取当前结构元素所覆盖下的原图对应区域内的所有像素的最小值，用这个最小值替换当前像素值，删除对象边界的某些像素，使二值图像减小一圈。删除对象边界的某些像素。**全1才为1。**

腐蚀用于分割(isolate)独立的图像元素 

* 结构元（类似于kernel）

  锚点对齐某个像素点后，若二值图像对应腐蚀滤波器中其他像素点都非0，则保留该像素点（图像中像素为1），若存在0像素点（背景）则删除（背景中像素为0）。即：找到所有能够完全覆盖结构元的像素点。

  因此，结构元尺寸越大，越难覆盖，腐蚀效果越明显。

```c++
//生成结构元素函数
Mat cv::getStructuringElement(int type, Size ksize, Point anchor=Point(-1,-1))
```

> type：MORPH_RECT矩形结构元素、MORPH_CROSS十字结构元素、MORPH_ELLIPSE椭圆结构元素
>
> ksize：尺寸越大，腐蚀效果越明显
>
> anchor：只有十字结构元素的锚点位置会影响腐蚀后的轮廓形状

* 腐蚀

```c++
void cv::erode(Mat imgSrc, Mat imgOut, Mat kernel, Point anchor=Point(-1,-1), int iterations=1, int, const Scalar&)
```

> iterations：腐蚀的次数，默认1次
>
> 后面2个参数默认值即可，对图像的主要部分的腐蚀没有影响

腐蚀只针对非0像素。所以，若背景为黑色，白色的图形经过腐蚀会变小。若背景为白色，黑色的图形经过腐蚀，反而背景会变小、图形会变大。

## 5. 膨胀

 膨胀是腐蚀的反运算（物体会扩大） 

膨胀——针对img用滤波器邻域中的最大像素值替代锚点对应的目标像素值。

 膨胀用于连接(join)相邻的元素 

锚点对齐某个像素点后，若二值图像对应腐蚀滤波器中的像素点中有1（白色）则保留目标像素，若全部是0像素点（背景）则删除（设为0，成为黑色）。 用结构元的中心点对准当前正在遍历的这个像素，然后取当前结构元素所覆盖下的原图对应区域内的所有像素的最大值，用这个最大值替换当前像素值，给图像中的对象边界添加像素，使二值图像扩大一圈。 给图像中的对象边界添加像素。**有1就为1。**

 腐蚀、膨胀可用于去噪（低尺寸结构元素的腐蚀操作很容易去掉分散的椒盐噪声点），图像轮廓提取、图像分割、寻找图像中的明显的极大值区域或极小值区域等 

```c++
void cv::dilate(Mat imgSrc, Mat imgOut, Mat kernel, Point anchor=Point(-1,-1), int iterations=1, int, const Scalar&)
```

## 6. 开运算

https://blog.csdn.net/qinchang1/article/details/86760571

多数情况腐蚀和膨胀不会单独使用，都是组合着使用，这就涉及到开运算和闭运算 

**针对白色高亮区域，闭合是对图像先膨胀后腐蚀，开启是先腐蚀后膨胀。**这些滤波器通常用于目标检测，闭合滤波器可以把错误分裂成小碎片的物体连接起来，而开启滤波器可以移除因图像噪声产生的斑点。  **若图像是黑色的部分**，则开闭运算中，腐蚀和膨胀的顺序是相反的。

 开运算可以去除目标上**突起的毛刺和连接的窄桥**

```c++
void morphologyEx( InputArray imgSrc, OutputArray imgDst,
                                int op, InputArray kernel,
                                Point anchor = Point(-1,-1), int iterations = 1,
                                int borderType = BORDER_CONSTANT,
                                const Scalar& borderValue = morphologyDefaultBorderValue() )
```

> op：操作类型。
>
> ```c++
> enum MorphTypes{
>     MORPH_ERODE    = 0, //腐蚀
>     MORPH_DILATE   = 1, //膨胀
>     MORPH_OPEN     = 2, //开操作
>     MORPH_CLOSE    = 3, //闭操作
>     MORPH_GRADIENT = 4, //梯度操作
>     MORPH_TOPHAT   = 5, //顶帽操作
>     MORPH_BLACKHAT = 6, //黑帽操作
>     MORPH_HITMISS  = 7  
> };
> ```

```c++
  	Mat kernel = getStructuringElement(MORPH_RECT,Size(3,3));//创建结构元素大小为3*3
    morphologyEx(src, dst,MORPH_OPEN, kernel);//开操作
```

```c++
//开操作（腐蚀+膨胀）提取水平与垂直直线
#include <opencv2\opencv.hpp>
#include <iostream>
 
using namespace std;
using namespace cv;
 
const char * input = "input image";
const char *output = "output image";
 
void main()
{
	Mat srcImg, grayImg, binImg, tempImg,resImg;
	srcImg = imread("chars.png");
	if (!srcImg.data)
	{
		cout << "error image data" << endl;
		return;
	}
	namedWindow(input, WINDOW_AUTOSIZE);
	imshow(input, srcImg);
 
	//convert to grayImg
	if (srcImg.channels() == 3)
	{
		cvtColor(srcImg, grayImg, CV_BGR2GRAY);
	}
	else
	{
		grayImg = srcImg;
	}
 
	//convert to  binaryImg
	adaptiveThreshold(~grayImg, binImg, 255, ADAPTIVE_THRESH_MEAN_C, THRESH_BINARY, 15, -2);
 
 
	//create struct element
	int xSize =binImg.cols/180 ;
	int ySzie = binImg.rows/180;
	Mat horizonLine = getStructuringElement(MORPH_RECT, Size(xSize, 1), Point(-1, -1));
	Mat verticalLine = getStructuringElement(MORPH_RECT, Size(1, ySzie), Point(-1, -1));
 
	//open operation, 提取水平直线
	//erode(binImg, tempImg, horizonLine);
	//dilate(tempImg, resImg, horizonLine);
	//morphologyEx(binImg, resImg, CV_MOP_OPEN, horizonLine); //morphologyEx==erode+dilate
 
	/*提取字符*/
	Mat kernel = getStructuringElement(MORPH_RECT, Size(3, 3), Point(-1, -1));
	morphologyEx(binImg, resImg, CV_MOP_OPEN, kernel);
	bitwise_not(resImg, resImg);
	blur(resImg, resImg, Size(3, 3), Point(-1, -1));
	imshow(output, resImg);
 
	waitKey(0);
	return;
}
```

## 7. 闭运算

 闭运算可以去除**目标内的孔洞**，填补沟壑。 

 形态学中还有很多其他的组合方式，例如：将膨胀之后的目标与原目标进行一个减运算，便可以得到目标的轮廓信息。 

## 8. 顶帽TopHat

https://blog.csdn.net/qq_36387683/article/details/80489631

https://blog.csdn.net/qq_30263737/article/details/94625246

源图像与开操作之间的差值图像。**src-open**。得到图像边缘的一些毛刺。

因为开运算到来的结果是放大了裂痕或者局部低亮度的区域，因此，从原图中减去运算后的图，得到的效果图突出了比原图轮廓周围的区域更明亮的区域，且这一操作和选择的核的大小相关。
**顶帽运算往往用来分离比邻近点亮一些的斑块。**当一幅图像具有大幅的背景的时候，而微小物品比较有规律的情况下，可以使用顶帽运算进行背景提取

## 9. 黑帽BlackHat

闭操作图像与源图像的差值图像。**close-src**。得到图像内部的小孔或前景色的小黑点。

 黑帽运算之后的效果图突出了与原图像轮廓周围的区域更暗的区域，且这一操作和选择的核大小相关。所以黑帽运算用来**分离比邻近点暗一些的斑块。 **

## 10. 形态学梯度

https://zhuanlan.zhihu.com/p/270394873

就是膨胀图与俯视图之差，用于保留物体的边缘轮廓。 

## 11. 击中击不中hit-miss

 HMT变换 

https://www.zhihu.com/question/23669012

https://cloud.tencent.com/developer/article/1589572