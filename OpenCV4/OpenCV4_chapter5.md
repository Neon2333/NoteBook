# 图像滤波

## 1. 图像卷积——filter2D

https://www.zhihu.com/question/22298352/answer/228543288

* 高频： 其实就是目标像素跟周边像素相差比较大 

* 卷积核kernel

  一般是奇数大小的矩阵，也叫邻域矩阵

  通过对目标像素的邻域进行加权得到新的目标像素的值，其中这个**权重矩阵**我们叫做邻域算子 

* 卷积过程，滑窗实现（kernel从左到右从上到下在源图片上移动）。中心点的灰度值=周边其他像素求积求和

* 为了避免求积求和后的值超出255，可对模板进行归一化处理

  kernel[i]=kernel[i]/Σkernel[i]

* 对于源图片边缘的像素点，模板中心若移动到上面会没有对应像素点，此时就需要对源图片的周围的像素外推，补充灰度为0的像素点

* 输入图片可以为多通道，多个通道与同一个模板执行卷积。若希望各通道与不同模板执行卷积，则需要split将各个通道分离

* 卷积利用了像素点周边的像素点的信息，具有模糊图片的作用

  ```c++
  void cv::filter2D(Mat imgSrc, Mat imgOut, int type, Mat kernel, Point anchor=Point(-1,-1), double delta=0, int borderType=BORDER_DEFAULT)
  ```

  > type：输出矩阵的数据类型。赋值-1表示自动判断
  >
  > anchor：内核锚点。（-1，-1）表示是内核的中心点
  >
  > delta：求和求积后再加上delta，output处理后的每个像素点的灰度值都偏移delta。
  >
  > borderType：源图片像素外推方法
  >
  > kernel：自定义卷积核（滤波时称滤波器）

## 2. 噪声

* 噪点：属于高频信号， 高频信号，就好像平地耸立的山峰，看起来很显眼。平滑这座山峰的办法之一就是，把山峰刨掉一些土，填到山峰周围去。用数学的话来说，就是把山峰周围的高度平均一下。**所以去噪也叫平滑**。

* 噪点一般是高频，低通滤波器（邻域矩阵）可以过滤噪声，但是平滑过程会导致图像模糊和图像边缘的模糊，所以去噪=模糊。
*  图像细节一般在高频，所以高通滤波器可以让图像变的更加清晰、图像锐化、图像边缘提取/增强。

## 3. 椒盐噪声

* 椒盐噪声就是在随机位置生成黑或白的像素点（两个随机：噪声点的位置、噪声点黑色或者白色）

* OpenCV4中也有随机数函数。

  cvflann::rand_int()：生成0到RAND_MAX之间的整数
  
  cvflann::rand_double()：生成0到1.0之间的double 
  
  另外c++库stdlib.h中有随机数函数rand()
  
  ```c++
  //给图片添加椒盐噪声函数
  //src源图片，n添加的噪声点的数量
  //imgOut传引用，传值只是对临时变量操作，或者传指针
  void saltAndPepper(Mat& imgSrc, Mat& imgOut, int n){
     	if (imgSrc.empty() || n <= 0) {
  		cout << "wrong.." << endl;
  	}
      imgSrc.copyTo(imgOut);
      for(int i=0;i<n;i++){
          int posX,posY;//定义噪音点的坐标
          posX=cvflann::rand_int()%imgOut.cols;
          posY=cvflann::rand_int()%imgOut.rows;
          int write_black=cvflann::rand()%2;
          if(imgOut.type()==CV_8UC1){
              if(write_black==1){
                  imgOut.at<uchar>(posY,posX)=0;//posY行posX列
              }else{
                  imgOut.at<uchar>(posY,posX)=255;
              }
          }else if(imgOut.type()==CV_8UC3){
              if (write_black == 1) {
  				imgOut.at<Vec3b>(posY, posX)[0] = 0;//Mat.at访问用的是行数和列数
  				imgOut.at<Vec3b>(posY, posX)[1] = 0;
  				imgOut.at<Vec3b>(posY, posX)[2] = 0;
  			}
  			else {
  				imgOut.at<Vec3b>(posY, posX)[0] = 255;
  				imgOut.at<Vec3b>(posY, posX)[1] = 255;
  				imgOut.at<Vec3b>(posY, posX)[2] = 255;
  			}
          }
      }
  }
  ```

## 4. 高斯噪声

* 噪声点的概率分布服从高斯/正态分布

* 服从高斯分布的随机数生成

  RNG类是opencv里C++的随机数产生器。它可产生一个64位的int随机数。目前可按均匀分布和高斯分布产生随机数 

  RNG::uniform(a,b)  返回一个[a,b)范围的均匀分布的随机数，a,b的数据类型要一致，而且必须是int、float、double中的一种，默认是int。

  RNG::gaussian(σ)  返回一个均值为0，标准差为σ的随机数。

  如果要产生均值为λ，标准差为σ的随机数，可以λ+ RNG::gaussian(σ)

  ```c++
  //创建RNG对象，使用默认种子“-1”  
  RNG rng;      
  //产生64位整数  
  int N1 = rng;  
  /*-------------产生均匀分布的随机数uniform和高斯分布的随机数gaussian---------*/  
  //总是得到double类型数据0.000000，因为会调用uniform(int,int),只会取整数，所以只产生0   
  double N1a = rng.uniform(0,1);  
        
  //产生[0,1)范围内均匀分布的double类型数据  
  double N1b = rng.uniform((double)0,(double)1);  
    
  //产生[0,1)范围内均匀分布的float类型数据，注意被自动转换为double了。  
  double N1c = rng.uniform(0.f,1.f);  
    
  //产生[0,1)范围内均匀分布的double类型数据。  
  double N1d = rng.uniform(0.,1.);  
    
  //可能会因为重载导致编译不通过(确实没通过。。)     
  //double N1e = rng.uniform(0,0.999999);  
    
  //产生符合均值为0，标准差为2的高斯分布的随机数  
  double N1g = rng.gaussian(2);     
  ```
  
  ```c++
  void cv::RNG::fill(Mat output, int type, a, b, bool)
//该函数是RNG的非静态成员函数，需要创建rng对象调用
  ```
  
  > output：随机数矩阵
  >
> type：UNIFORM——均匀分布，NORMAL高斯分布
  >
  > a：均值（均匀分布时是下界）
  >
  > b：标准差（UNIFORM时是上界）

```c++
//给图片添加高斯噪声函数
//imgSrc待处理图像
//avg均值，stdDev标准差
void gaussNoise(Mat& imgSrc, Mat& imgOut, int avg, int stdDev){
    if(imgSrc.empty()){
        cout<<"wrong file.."<<endl;
    }
    Mat noise = Mat::zeros(imgSrc.rows,imgSrc.cols,imgSrc.type());//原图没有噪音的点不变
    RNG rng;
    rng.fill(noise, RNG::NORMAL, avg, stdDev);
    imgOut=imgSrc+noise;
}
```

# 线性滤波

## 5. 均值滤波——blur

https://zhuanlan.zhihu.com/p/76188487

### typora输入数学公式：

https://blog.csdn.net/qq_38228254/article/details/79469727?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs

https://blog.csdn.net/mingzhuo_126/article/details/82722455

https://blog.csdn.net/Hey___Man/article/details/79272137

* 滤波=平滑=模糊（将噪点以邻域像素点的信息平滑）

用**邻域滤波**的方法，卷积核/邻域kernel=(1/ksize.width*ksize.height)\*ones(ksize.height,ksize.width,CV_8UC1)
$$
\frac{1}{ksize.width*ksize.height}
\left[
\begin{matrix}
1 & 1 & ... 1 \\
1 & 1 & ... 1 \\
...\\
1 & 1 & ... 1
\end{matrix}
\right]
$$

* 实质是对源图像，某个像素的邻域内的所有像素的灰度值求均值，以该均值作为该像素点的灰度值。

```c++
void cv::blur(Mat imgSrc, Mat imgOut, Size ksize, Point anchor=Point(-1,-1), int borderType=BORDER_DEFAULT);
```

> imgOut：均值滤波后的输出图像
>
> ksize：卷积核大小

## 6. 高斯滤波——GaussianBlur

https://blog.csdn.net/godadream/article/details/81568844

https://www.jianshu.com/p/73e6ccbd8f3f

* 高斯滤波器中心像素灰度值为均值，根据高斯分布和距中心像素的距离求得每个像素的灰度值， **高斯滤波器是根据高斯函数的形状来选择权值的线性平滑滤波器** ， **适用于消除高斯噪声。**

* 均值滤波器的模板系数都是相同的为1；而高斯滤波器的模板系数，则随着距离模板中心的增大而系数减小。所以，高斯滤波器相比于均值滤波器对图像个模糊程度较小。 

* 高斯核

  理论上，高斯分布在所有定义域上都有非负值，这就需要一个无限大的卷积核。实际上，仅需要取均值周围3倍标准差内的值，以外部份直接去掉即可。**高斯滤波的重要两步就是先找到高斯模板然后再进行卷积**，  模板（mask在查阅中有的地方也称作掩膜或者是高斯核）。 假定中心点的坐标是（0,0），那么取距离它最近的8个点坐标，为了计算，需要设定σ的值。假定σ=1.5，则模糊半径为1的高斯模板就算如下，还要确保这九个点加起来为1（这个是高斯模板的特性）。高斯核越大，像素点收到邻域影响的这个邻域越大，图像就越模糊。

```c++
void cv::GaussianBlur(Mat imgSrc, Mat imgOut, Size ksize, double sigmaX, double sigmaY, int borderType=BORDER_DEFAULT);
```

> imgSrc：源图像
>
> imgOut：高斯滤波后的图像
>
> ksize：滤波器/卷积核尺寸。必须是**正奇数**
>
> sigmaX：x轴的高斯滤波器标准偏差
>
> sigmaY
>
> 若sigmaY设置为0，则自动将sigmaY设置等于sigmaX。若sigmaX=sigmaY=0，则自动按照ksize设置XY轴的标准偏差

常用的高斯滤波器为：
$$
\frac{1}{16}*
\left[
\begin{matrix}
1&2&1\\
2&4&2\\
1&2&1
\end{matrix}
\right]
$$

$$
\frac{1}{273}*
\left[
\begin{matrix}
1&4&7&4&1\\
4&16&26&16&4\\
7&26&41&26&7\\
4&16&26&16&4\\
1&4&7&4&1\\
\end{matrix}
\right]
$$

* 单一方向高斯滤波器生成

  ```c++
  Mat cv::getGaussianKernel(Size ksize,double sigma, int ktype=CV_64F)
  ```

  > ksize：高斯滤波器尺寸
  >
  > sigma：标准差。若赋负数，则自动根据ksize生成。

  **返回值是一个ksize*1矩阵**。

## 7. 方框滤波——boxFilter

* 对滤波器内所有像素的灰度值**求和**，然后作为中心像素的灰度值。

* 若对滤波器内数值进行归一化处理，方框滤波同均值滤波。

  ```c++
  void cv::boxFilter(Mat imgSrc, Mat imgOut, int depth, Size ksize, Point anchor=Point(-1,-1),bool normalize=true, int borderType);
  ```

  > depth：源图像深度。CV_8UC1，深度8位。
  >
  > normalize：默认是true，滤波器数值进行归一化，k=ones/k.width*k.height，此时就是均值滤波。若设置为false，滤波器是一个全1矩阵。

$$
\alpha=
\begin{cases}
1 & normalize=false \\
\frac{1}{k.width*k.height} & normalize=true
\end{cases}
$$

$$
kernel=\alpha*
\left[
\begin{matrix}
1 & 1 & ... & 1 \\
1 & 1 & ... & 1 \\
... \\
1 & 1 & ... & 1 
\end{matrix}
\right]
$$

* 对滤波器内所有像素灰度值求**平方和**

```c++
void cv::sqrBoxFilter()//参数同上
```

## 8.可分离滤波——sepFilter2D

* 自定义滤波器：

  filter2D

* 图像滤波的滑窗滑动过程是一个并行计算过程，下一个像素的计算不依赖于上一个像素的计算，所以可以使用并行计算处理。

* 图像滤波具有可分离性

  先对X方向再对Y方向滤波，先对Y方向再对X方向滤波，XY方向滤波器联合后滤波，三者效果相同。

  X方向滤波器：[x<sub>1</sub>,x<sub>2</sub>,...,x<sub>n</sub>]

  Y方向滤波器：[y<sub>1</sub>,y<sub>2</sub>,...,y<sub>n</sub>]<sup>T</sup>

  联合滤波器：Y*X

```c++
Mat kernelY=(Mat_<float>(3,1)<<-1,3,-1);//Y方向滤波器
Mat kernelX=kernelY.reshape(1,1);//X方向滤波器
Mat kernelXY=kernelY*kernelX;//联合滤波器

Mat imgY,imgX,imgYX;
Mat imgXY;
filter2D(img,imgY,-1,kernelY,Point(-1,-1),0,BORDER_DEFAULT);//img对Y方向滤波
filter2D(imgY,imgYX,-1,kernelX,Point(-1,-1),0,BORDER_DEFAULT);//对imgY再在X方向滤波
filter2D(img,imgXY,-1,kernelXY,Point(-1,-1),0,BORDER_DEFAULT);//对img联合滤波

imshow("img",img);
imshow("先Y再X滤波"imgYX);
imshow("联合滤波",imgXY);
```

* 先X再Y、先Y再X需要调用2次filter2D。

  sepFilter2D可以将分离的XY方向滤波器一次执行

  ```c++
  void cv::sepFilter(Mat imgSrc, Mat imgOut, int depth=-1, Mat kernelX, Mat kernelY, Point anchor=Point(-1,-1), double delta, int borderType)
  ```

  > depth：输出图像的数据类型，-1表示自动选择
  >
  > kernelX：X方向滤波器
  >
  > delta：滤波后每个像素值偏移（滤波后每个灰度值再加上delta）

```c++
//分别生成Y和X方向的高斯滤波器
Mat kernelX=getGaussianKernel(Size(3,3),1);//标准差设为1
Mat imgYX;
Mat imgXY;
GaussianBlur(img,imgYX,Size(3,3),1,1,BORDER_DEFAULT);//直接高斯滤波
sepFilter2D(img,imgXY,kernelX,kernelX,Point(-1,-1),0,BORDER_DEFAULT);//XY方向上都是相同的单方向高斯滤波
```

# 非线性滤波

线性滤波的计算方法，噪点也会参加线性计算，所以噪点不会被完全消除，只会以更柔和形式存在。

## 9. 中值滤波——medianBlur

将滤波器内像素的灰度值升序排序，取中位数作为中心像素的灰度值。

对椒盐噪声的滤波效果较好。可避免图像的模糊，但是**滤波器尺寸过大后仍然会模糊。**

```c++
void cv::medianBlur(Mat imgSrc, Mat imgOut, Size ksize)
```

> imgSrc：只能是单通道、三通道、四通道
>
> ksize：滤波器尺寸。3/5/7，尺寸过大以后，源图像imgSrc的类型只能是CV_8U。

## 10.双边滤波——bilateralFilter

前面几种滤波都会导致图像模糊。双边滤波可以实现去噪、局部平滑，且令距离边缘较远的像素对边缘上的像素的影响

较小，从而保留边缘的清晰。

```c++
void cv::bilateralFilter(Mat imgSrc, Mat imgOut, int d, double)
```

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

# 边缘检测

## 11. 原理

https://blog.csdn.net/qq_37749442/article/details/102757901

http://www.voidcn.com/article/p-fxlrsxdr-cr.html

https://www.cnblogs.com/xinxue/p/5348743.html

https://blog.csdn.net/qq_38622495/article/details/82621792

图像的边缘是**灰度值变化特别剧烈的像素点**的集合。变化剧烈反映在灰度值变化函数的陡峭程度上，也就是一阶导数的局部极值点，也是二阶导数的零点。

图像的每个像素点的灰度值可看成二元函数f(x,y)，在X和Y方向上分别求偏导
$$
\frac{\partial f}{\partial x}、\frac{\partial f}{\partial y}
$$
因为图像是离散的，所以微分变为差分，X方向：f(x+1,y)-f(x,y)即-f(x,y)+f(x+1,y)，Y方向：f(x,y+1)-f(x,y)即-f(x,y)+f(x,y+1)。
$$
X方向上梯度：Grad(x)=\frac{\partial f(x,y)}{\partial x}=f(x+1,y)-f(x,y)\\
Y方向上梯度：Grad(y)=\frac{\partial f(x,y)}{\partial y}=f(x,y+1)-f(x,y)\\
梯度为矢量，幅值和角度分别为：\\
Magnitude(x,y)=\sqrt([Grad(x)]^2+[Grad(y)]^2)\\
\theta(x,y)=arctan\frac{Grad(x)}{Grad(y)}
$$


在图像上就是相邻两点作差，这样所得的卷积核为[-1,1]和[-1,1]<sup>T</sup>，对X方向即后一列像素减去前一列像素，对Y方向即后一行像素减去后一行像素。

但是，卷积核不要为偶数尺寸。 因为模板是偶数的话，卷积出来的结果应该是放在中间的，不方便表示。
例如：图像 [10, 20, 30] 跟 [-1, 1] 卷积后的值，应该放在图像 10 跟 20 中间的位置，就是应该放在 0 和 1 号位置的中间，也就是 0.5 号位，但是图像是离散的，中间没得放，只能放在 0 号位，也就是 10 的位置，就偏差了 0.5 的位置，为了方便处理，滤波模板一般都是奇数个的，3，5，7 个的 。

所以将差分该为要求像素的后一个与前一个求差，即-f(x-1,y)+f(x+1,y)和-f(x,y-1)+f(x,y+1)，对应的卷积核为[-1,0,1]和[-1,0,1]<sup>T</sup>

```css
排好序：[-1 * f(x-1)，0 * f(x)，1 * f(x+1)]
提出系数：[-1, 0, 1]
```

求像素的差值，边缘是由差值变化巨大的点组成，梯度的大小代表边缘的强度，选择合适阈值对卷积后产生的由梯度值组成的图像进行二值化，可得边缘的二值化图像。

## 12. Sobel算子

一阶偏导算子

```c++
void cv::Sobel(Mat imgSrc, Mat imgOut, int depth, int dx, int dy, int ksize=3, double scale=1,double delta=0, int borderType=BORDER_FAULT)
```

> depth：imgOut的数据类型，因为Sobel卷积后产生的imgOut中可能出现负数，所以depth不要设为CV_8U
>
> dx、dy：X和Y方向上的差分阶数。取0或1，一般不超过2
>
> ksize：卷积核的尺寸：3、5、7，当max{dx,dy}=1时ksize=3，max{dx,dy}=2时ksize=5，max{dx,dy}=3时ksize=7
>
> scale：计算结果缩放系数
>
> delta：计算结果增量

分别求出X方向和Y方向的imgOut后，有两种方式合成：

使用addWeighted加权合成X和Y方向上的各自的一阶导数
$$
Gx=kernelx*I,Gy=kernely*I\\
输出图像为：\\
G=\sqrt(Gx^2+Gy^2)\\
或简化为G=|Gx|+|Gy|
$$

```c++
Sobel(img, resultX, CV_16S，1, 0, 3);
convertScaleAbs(resultX,resultX);//对resultX的每个像素求绝对值
Sobel(img, resultY, CV_16S，0, 1, 3);
convertScaleAbs(resultY,resultY);//对resultY的每个像素求绝对值
//result=resultX+resultY;
addWeighted(const Mat imgSrc1, double alpha, const Mat imgSrc2, double beta ,double gamma, Mat imgOut)
imshow("resultX",resultX);
imshow("resultY",resultY);
imshow("result",result);
```

> addWeighted将两幅图像合并
>
> alpha：imgSrc1的权重
>
> beta：imgSrc2的权重
>
> gamma：两幅图像合并后的增量，不要太大，和超过255就是全白

## 13. Scharr算子

Sobel算子对于图像中较弱的边缘的提取能力较弱，为了提取较弱的边缘，需要将像素值间的差异增大，为此将卷积核中的权重加大来增大像素点间的差值。Scharr算子只有2个3*3的卷积核：
$$
Gx=\left
[\begin{matrix}
-3&0&3\\
-10&0&10\\
-3&0&3
\end{matrix}
\right]

Gy=\left
[\begin{matrix}
-3&-10&-3\\
0&0&0\\
3&10&3
\end{matrix}
\right]
$$

```c++
void cv::Scharr(Mat imgSrc, Mat imgOut, int depth, int dx, int dy, double scale=1, double delta=0,int borderType=4)
```

> depth：CV_16S
>
> dx、dy：X和Y方向的差分阶数，**只能有一个为1且不能同时为0**（一个1一个0）

## 14. 边缘检测卷积核生成函数——getDerivKernels

依照原理计算出Sobel算子的计算量很大

提供了计算Sobel算子和Scharr算子的函数

```c++
void cv::getDerivKernels(Mat kx, Mat ky, int dx, int dy, int ksize, bool normalize=false, int ktype=CV_32F)
```

> kx：行滤波器的输出矩阵
>
> ky：列滤波器的输出矩阵
>
> dx/dy：边缘检测效果的控制参数。
>
> ksize：滤波器大小。1/3/5/7（表示生成的是Sobel算子）或FILTER_SCHARR（表示生成scharr算子），为1时dx/dy最大值要小于3，为FILTER_SCHARR时一个取0一个取1。
>
> normalize：是否对生成的滤波器归一化处理

## 15. Laplacian算子

无方向性，只需要一次边缘检测，不需要分别计算X和Y。

二阶偏导算子

利用高斯滤波先降低一下噪声

```c++
void cv::Laplacian(Mat imgSrc, Mat imgOut, int depth ,int ksize=1, double scale=1, double delta=0, int borderType=4)
```

> ksize：滤波器的大小，必须是正奇数
>
> depth：CV_16S

## 16. Canny算子

不容易收到噪声影响，能够识别强边缘和弱边缘，是目前最优边缘检测算法之一。

算法原理流程复杂

增加了非最大值抑制和双阈值检测，是边缘检测算子中最常用的一种，常被其它算子作为标准算子来进行优劣比较。

对图像的梯度强度进行非极大抑制，可看做边缘细化：只有候选边缘点被保留，其余的点被移除

利用双阈值检测和连接边缘，若候选边缘点大于上阈值，则被保留；小于下阈值，则被舍弃；处于二者之间，须视其所连接的像素点，大于上阈值则被保留，反之舍弃



```c++
void cv::Canny(Mat imgSrc, Mat imgOut, double threshold1, double threshold2, int dSobel=3, bool flag=false)
```

> threshold1：下阈值
>
> threshold2：上阈值
>
> dSobel：Sobel算子的直径
>
> flag：计算梯度幅值的方法，false表示计算强度采用近似形式，true表示采用更精确的形式