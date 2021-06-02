# 直方图

## 1. 概念

* 直方图：统计每个灰度值的个数/百分比，灰度值/灰度区间作为横轴，以个数/百分比作为纵轴画出的统计图。灰度表示亮暗，直方图可以直观表示出图像的亮暗分布情况。
* 一维直方图： 之所以称为一维，是因为我们仅考虑一个特征，即像素的灰度强度值（ 对于一维直方图，我们从BGR转换为**灰度图像**）。 但是在二维直方图中，您要考虑两个特征。 
* 二维直方图： 二维直方图中，您要考虑两个特征。 通常，它用于查找颜色直方图，其中两个特征是每个像素的色相和饱和度值。 颜色直方图，我们需要将图像从BGR转换为**HSV**。

## 2. 统计灰度值个数函数——calcHist

```C++
void cv::calcHist(const Mat* srcImg, int numImg, const int* channels, Mat mask, Mat hist, int dims, int* histSize, float** ranges, bool, bool)
```

>**srcImg**：输入图片的数组
>
>**numImg**：输入图片总数
>
>**channels：**图片中需要统计的那若干通道的索引的列表。第一个图像的通道索引为{0,1,2,...,img[0].channels()-1}，第二个图像的通道索引为{img[0].channels(),img[0].channels()+1,...,img[0].channels()+img[1].channels()-1}。一般都是一张图片操作，所以只需要索引都是从0开始到channels()-1结束。如果是灰度图像则channels[1]={0};如果是彩色图像则channels[3]={0,1,2}；如果只是求彩色图像第2个通道的直方图则channels[1]={1}。列表取值永远都是0,1,2,3,...。
>
>**mask**：掩膜矩阵
>
>**hist**：输出的直方图数组（维度为dims，与原始图片的通道数channels()相同）
>
>```c++
>hist[histSize][dims]//每列存一个通道的256个像素的个数
>```
>
>**dims**：hist的维度。灰度图像为1维，彩色图像为3维。就是通道数channels。
>
>**histSize**：直方图中每个dims维度需要分成多少个区间（如果把直方图看作一个一个竖条的话，就是竖条bin的个数bins）。若每个灰度都要统计，一般取256。bins[dims]
>
>**ranges**：二维数组。指明原始图片每个通道中的像素的灰度值的取值范围，{grayValue1,grayValue2}
>
>```c++
>ranges[dims][grayValue]
>//如对于一个BGR三通道的图片
>const float inRanges[2]={0.0f,255.0f};
>const float* ranges[3]={range,range,range};//三通道取值都是0~255
>```
>

**该函数参数列表过于复杂，使用时最好先将所有图片用split()函数将通道分开，只处理单通道**

只处理一张图片的单通道时，numImg=1，channels={0}，hist[256]，dims=1，histSize=256，ranges={inRanges},inRanges={0,256}

```c++
//显示灰度值绝对数量时，往往会因为像素点个数>直方图的高度。因此可统一将所有数量除以20/30/50.
const string filePath = "C:\\Users\\25224\\Desktop\\img01.jpg";
const Mat img = imread(filePath);
Mat gray;
Mat hist;
	if (img.empty()) {
		cout << "wrong file.." << endl;
	}
	cvtColor(img, gray, COLOR_BGR2GRAY);
	//imshow("gray", gray);
	const int channels[1] = { 0 };
	const float inRanges[2] = { 0.0f,255.0f };
	const float* ranges[1] = { inRanges };
	const int bins[1] = { 256 };
	calcHist(&gray, 1, channels, Mat(), hist, 1, bins, ranges);
	
	int hist_w = 512;//hist_w=width*256,hist.cols
	int hist_h = 500;//直方图的高度,hist.rows
	int width = 2;
	Mat histImg = Mat::zeros(hist_h, hist_w, CV_8UC1);

	//注意i的初始值从1开始
	//hist.rows=256(hist[rows][channels])
	for (int i = 1; i <= hist.rows; i++) {	
		//除以100是因为像素数量过大为2000左右，而histImg.rows=500
		rectangle(histImg,
			Point(width * (i - 1), hist_h - 1),
			Point(width * (i - 1) + 1, hist_h - 1 - cvRound(hist.at<float>(i - 1, 0) / 100)),//cvRound去四舍五入整数。根据histImg的大小调整，除以100使得直方图可以被图片容下
			Scalar(255, 255, 255), -1);//-1表示实心线,两点是矩形的对角线上的两个点
	}
	imshow("hist", histImg);
```

## 3.归一化——normalize

* **归一化**就是要把需要处理的数据经过处理后（通过某种算法）限制在你需要的一定范围内。首先归一化是为了后面数据处理的方便，其次是保证程序运行时收敛加快。归一化的具体作用是归纳统一样本的统计分布性。归一化在0-1之间是统计的概率分布，归一化在某个区间上是统计的坐标分布。归一化有同一、统一和合一的意思。

    **归一化的目的**简而言之，是使得没有可比性的数据变得具有可比性，同时又保持相比较的两个数据之间的相对关系，如大小关系；或是为了作图，原来很难在一张图上作出来，归一化后就可以很方便的给出图上的相对位置等。

  ```c++
  void cv::normalize(Mat src, Mat output, double alpha=1, double beta=0, int norm_type=NORM_L2, int dtype=-1, Mat mask);
  ```

  > src: 源直方图矩阵
  >
  > output：输出直方图矩阵
  >
  > alpha：
  >
  > beta：
  >
  > norm_type：归一化选择的范数
  >
  > dtype：负数时表示output和src具有相同的数据类型和通道数。否则只有相同的通道数。
  >
  > mask：掩膜

```c++
//L2归一化
	Mat histL2;
	int histL2_w = 512;
	int histL2_h = 500;
	Mat histL2Img = Mat::zeros(histL2_h, histL2_w, CV_8UC1);
	normalize(hist, histL2, 1, 0, NORM_L2, -1, Mat());

	for (int i = 1; i <= histL2.rows; i++) {
		rectangle(histL2Img,
			Point(width * (i - 1), histL2Img.rows - 1),
			Point(width * (i - 1) + 1, histL2Img.rows - 1 - cvRound(histL2Img.rows*histL2.at<float>(i - 1, 0))),//因为归一化后的直方图的值肯定<1，所以乘以图片的高度
			Scalar(255, 255, 255), -1);
	}
	imshow("histL2", histL2Img);

	//INF归一化
	Mat histINF;
	int histINF_w = 512;
	int histINF_h = 500;
	Mat histINFImg = Mat::zeros(histINF_h, histINF_w, CV_8UC1);
	normalize(hist, histINF, 1, 0, NORM_INF, -1, Mat());

	for (int i = 1; i <= histINF.rows; i++) {
		rectangle(histINFImg,
			Point(width * (i - 1), histINFImg.rows - 1),
			Point(width * (i - 1) + 1, histINFImg.rows - 1 - cvRound(histINFImg.rows * histINF.at<float>(i - 1, 0))),//cvRound去四舍五入整数
			Scalar(255, 255, 255), -1);
	}
	imshow("histINF", histINFImg);
```

```c++
自定义——归一化并绘图函数
void drawHist(Mat& hist, int type, string windowName);
//输入直方图矩阵hist
//归一化方法type
//直方图窗口名windowName
drawHist(Mat hist, int type, string windowName){
    if(hist.empty()){
        cout<<"hist is empty.."<<endl;
    }
    int histImg_w=512;
    int histImg_h=500;
    int width=2;
    Mat histImg=Mat::zeros(histImg_h,histImg_w,CV_8UC1);
    normalize(hist,hist,1,0,type,-1,Mat());//type归一化方法
    for(int i=1;i<=hist.rows;i++){
        rectangle(
        		histImg,
            	Point(width*(i-1),histImg_h-1),
            	Point(width*(i-1) + 1,histImg_h-1-hist.at<float>(i-1)),
            	Scalar(255,255,255),
            	-1
        );
    }
    imshow(windowName,histImg);
}
```

## 4.直方图比较——compareHist

同一幅图放缩后，相关系数仍然近似为1，不同的图片相关系数很小。

```c++
compareHist(Mat src, Mat output, int method)
```

> src：源直方图矩阵
>
> output：输出直方图矩阵
>
> method：求直方图相关系数的方法

## 5. 直方图均衡化——equalizeHist

**输入图片必须是灰度图像**

若直方图集中在一个区域内，就表明像素的灰度值集中在某个区域内，图像的对比度就较小，不便于图像的识别。

通过映射，将灰度值的分布范围扩大（将直方图横向拉开），提高灰度值之间的差值，**增强图像对比度，从而凸显图像的纹理**。扩大灰度值分布范围的这个过程叫做直方图均衡化。

```c++
void cv::equalizeHist(Mat src, Mat output);//src和output分别是图像矩阵
```

## 6.直方图匹配/直方图规定化

类似于直方图均衡化，只不过匹配后的直方图分布形式是已知的，通过已知源、目标从而得知变换函数（灰度级映射表）。

**目的：为了令源图片的直方图分布方式和目标图片相同，希望处理后的图像具有规定的直方图形状**（图片经过匹配后，直方图分布方式与目标图片相同。）

理想情况下，从源图片像素n到输出图片像素r的映射应满足n->r=arg min{V<sub>S</sub>(n)-V<sub>Z</sub>(r)}，按照该规则构建灰度级映射表lut，将原图片映射到result图片。

#### lut：1*src.total的矩阵，源图像每个像素点的灰度值到输出图像每个像素点的灰度值的映射。

```c++
自定义直方图匹配函数
//histSrc源图片(灰度图)
//histTarget目标图片(灰度图)
//resultImg源图片经过直方图匹配后的图片
void histMatch(Mat& src, Mat& target, Mat& resultImg){
   if(src.empty()||target.empty()){
       cout<<"wrong file.."<<endl;
   }
   Mat histSrc,histTarget;//源图片和目标图片的直方图矩阵
   const int channels[1] = { 0 };
   const float inRanges[2] = { 0.0f,255.0f };
   const float* ranges[1] = { inRanges };
   const int bins[1] = { 256 };
   calcHist(&src, 1, channels, Mat(), histSrc, 1, bins, ranges);//计算源图片的直方图,mask用Mat()
   calcHist(&target, 1, channels, Mat(), histTarget, 1, bins, ranges);//计算目标图片的直方图
    
   float histSrc_accu[256]={0};//分别定义累计概率数组
   float histTarget_accu[256]={0};
   histSrc_accu[0]=histSrc.at<float>(0);
   histTarget_accu[0]=histTarget_accu[0]; 
   for(int i=1;i<256;i++){
       histSrc_accu[i]=histSrc_accu[i-1]+histSrc.at<float>(i);//给累计概率数组赋值
       histTarget_accu[i]=histTarget_accu[i-1]+histTarget.at<float>(i);
   }
    
   float accuDiff[256][256];//像素累计概率差值数组，srcImg的任一像素都和TargetImg的每一个像素求累计概率的差
   for(int i=0;i<256;i++){
       for(int j=0;j<256;j++){
           accuDiff[i][j]=histSrc_accu[i]-histTarget_accu[j];
       }
   }
    
   Mat lut(1,256,CV_8UC1);//灰度值映射表
   //找i=0:255时，令accuDiff最小的j的256个取值
   for(int i=0;i<256;i++){
       int minIndex=0;
       for(int j=1;j<255;j++){
           if(accuDiff[i][j]<accuDiff[i][minIndex]){
               minIndex=j;//索引值就是灰度值
           }
       }
       lut.at<uchar>(i)=static_cast<uchar>(minIndex);
   }
   LUT(src,lut,resultImg);//由于i和minIndex的取值为0~255，索引值实际上就是灰度值,lut的index是源图像的灰度值，value是输出图像的灰度值
}
```

## 7.直方图反向投影——calcBackProject

https://www.cnblogs.com/shuaishuaidefeizhu/p/7155414.html

https://blog.csdn.net/qq_33221533/article/details/81148673?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242

http://blog.sina.com.cn/s/blog_691ebcfc0101h4o2.html

*  backproject过程是：由源图像 ——> 直方图 ——> backproject图像 

   反向投影矩阵中某点的值就是它对应的原图像中的点所在区间的灰度直方图值。  具体有几个区间，要看把0~255划分为多少个区间。 源图片中像素在一个区间范围中的点越多，该像素所在位置在反向投影矩阵中就**越亮**。实际上通过亮度反映了，该处像素点在整个图片中的数量。

   那么怎么理解反向投影矩阵中的“反向”二字呢？从这个过程可以看出，我们是先求出原图像的直方图，再由直方图得到反向投影矩阵，由直方图到反向投影矩阵实际上就是一个反向的过程，所以叫反向。 

   通过图像的反向投影矩阵，我们实际上把原图像简单化了，简单化的过程实际上就是提取出图像的某个特征。所以以后我们可以用这个特征来对比两幅图，如果两幅图的反向投影矩阵相似或相同，那么我们就可以判定这两幅图这个特征是相同的。

* 查找指定的模板特征

   整个是基于块的形式，利用直方图做匹配，类似于模板匹配，只不过这些模板转换为直方图，而原图中以某点为基准，抠出来作对比的部分也转换为直方图，两个直方图作匹配，匹配的结果作为此点的值。 结果会是一张概率图，概率越大的地方，代表此区域与模板的相似度越高。 

  (1) 拿到 特征图像 （或模板图像）

  (2) 得到 特征图像的直方图

  (3) 拿到源图像，依据源图像的每个像素的值，在特征图像的直方图中找到对应的值，然后将直方图的值赋给新的图像。

  ```c++
  void cv::calcBackProject()
  ```


## 8.图像的模板匹配——matchTemplate

通过比较图像的像素的灰度值，来寻找相同内容。

```c++
void cv::matchTemplate(Mat src, Mat templ, Mat similar, int method, Mat mask);
```

> src：源图片，灰度图像或彩色图像均可
>
> templ：匹配模板矩阵
>
> similar：相似性矩阵
>
> method：匹配方法

输出为similar矩阵，存储相似性系数（最大为255），所以不论是灰度图像还是彩色图像的输出结果都是单通道。

矩形区域的相似性系数存储在左上角像素所在位置对应的similar处。在similar矩阵中找最大值所在位置，在源图像中对应位置以该像素为矩形区域左上角，画一个和模板templ相同大小的矩形区域。

```c++
Mat similar;
matchTemplate(srcImg, templ, similar, TM_CCOEFF_NORMED);//获得相似系数矩阵similar
double mimVal,maxVal;
Point minP,maxP;
minMaxLoc(similar,&min,&max,&minP,&maxP);//找相似度系数最高的像素点所在位置
//像素点是矩形区域的左上角
rectangle(srcImg,Rect(maxP.x,maxP.y,templ.cols,templ.rows),Scalar(255,255,255),2);//绘制矩形
imshow("img",srcImg);
imshow("templ",templ);
```































