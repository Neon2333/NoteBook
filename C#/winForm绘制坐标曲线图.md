1.通过一系列的点，绘制出曲线面积图或者曲线图。

​     2.能够实现实时界面刷新。

​     3.曲线图里面的很多属性都能自定义。



# 需求分析

###     功能实现分析

​     1.GDI+绘图可以实现点与点之间的连接，而且能够实现路径（GraphicsPath）相连和路径填充。将一系列的点连接起来就是曲线图了，如果要将一系列点围城的图形填充起来就是面积曲线图了。

​     2.GDI+能够将图片（Image）直接绘制到控件（Control）上，所以将1中得到的曲线图转换成Image所能识别的图像，实时返回给GDI+，然后GDI+就直接将图片实时绘制到控件上，就能实现实时刷新。

​     3.在绘制曲线图时，通过类来对绘制的图片进行封装，将曲线图进行抽象，剥离相关属性使外界可以随时访问。

如果对GDI+不太熟悉的小伙伴请先去看看GDI+哟，对于读懂下面的代码有帮助。

话不多说了，来看看实现吧！

# 具体实现

###     曲线图类

​     为了节省空间我在这儿就不在累述了，希望读者仔细研究代码。注意：这里使用的数据接受类型为List<Point>。

```csharp
 public class GraphEdit



    {



        /// <summary>



        /// 画板宽度



        /// </summary>



        public int BoardWidth { get; set; }



        /// <summary>



        /// 画板高度



        /// </summary>



        public int BoardHeight { get; set; }



        /// <summary>



        /// 画板背景颜色



        /// </summary>



        public Color BoardColor { get; set; }



        /// <summary>



        /// 画图区域颜色



        /// </summary>



        public Color AreasColor { get; set; }



        /// <summary>



        /// 曲线图颜色



        /// </summary>



        public Color GraphColor { get; set; }



        /// <summary>



        /// 坐标轴颜色



        /// </summary>



        public Color AxisColor { get; set; }



        /// <summary>



        /// 刻度线颜色



        /// </summary>



        public Color ScaleColor { get; set; }



 



        /// <summary>



        /// 当前绘制的图



        /// </summary>



        public Bitmap CurrentImage { get; set; }



 



        /// <summary>



        /// 垂直（纵向）边距（画图区域距离左右两边长度）



        /// </summary>



        public int VerticalMargin { get; set; }



        /// <summary>



        /// 平行（横向）边距（画图区域距离左右两边长度）



        /// </summary>



        public int HorizontalMargin { get; set; }



        /// <summary>



        /// X轴刻度线数量



        /// </summary>



        public int XScaleCount { get; set; }



        /// <summary>



        /// Y轴刻度线数量



        /// </summary>



        public int YScaleCount { get; set; }



 



        public GraphEdit(int width, int height, Color boradColor)



        {



            this.BoardWidth = width;



            this.BoardHeight = height;



            this.BoardColor = boradColor;



 



            //默认值



            this.XScaleCount = 12;



            this.YScaleCount = 5;



        }



 



        /// <summary>



        /// 获得当前数据画出的曲线面积图



        /// </summary>



        /// <param name="data">需要绘制的数据</param>



        /// <param name="xRange">X轴范围（data数据里面的实际范围）</param>



        /// <param name="yRange">Y轴范围（data数据里面的实际范围）</param>



        /// <param name="isFill">是否需要面积填充</param>



        /// <returns>当前的曲线面积图</returns>



        public Image GetCurrentGraph(List<Point> data, float xRange, float yRange, bool isFill)



        {



            CurrentImage = new Bitmap(BoardWidth, BoardHeight);



            Graphics g = Graphics.FromImage(CurrentImage);



            g.SmoothingMode = SmoothingMode.AntiAlias;   //反锯齿



            g.Clear(BoardColor);



 



            //1.确定曲线图区域



            int iAreaWidth = BoardWidth - 2 * HorizontalMargin;              //画图区域宽度



            int iAreaHeight = BoardHeight - 2 * VerticalMargin;              //画图区域高度



            Point pAreaStart = new Point(HorizontalMargin, VerticalMargin);  //画图区域起点



            Point pAreaEnd = new Point(BoardWidth - HorizontalMargin, BoardHeight - VerticalMargin);             //画图区域终点



            Point pOrigin = new Point(HorizontalMargin, BoardHeight - VerticalMargin);  //原点



            Rectangle rectArea = new Rectangle(pAreaStart, new Size(iAreaWidth, iAreaHeight));



            SolidBrush sbAreaBG = new SolidBrush(AreasColor);



            g.FillRectangle(sbAreaBG, rectArea);



 



            sbAreaBG.Dispose();



 



            //2.确定坐标轴



            Pen penAxis = new Pen(AxisColor, 5);



            penAxis.EndCap = LineCap.ArrowAnchor;



            g.DrawLine(penAxis, pOrigin, new Point(pAreaStart.X, pAreaStart.Y - VerticalMargin / 2));



            g.DrawLine(penAxis, pOrigin, new Point(pAreaEnd.X + HorizontalMargin / 2, pAreaEnd.Y));



 



            penAxis.Dispose();



 



            //3.确定刻度线和标签



            Pen penScale = new Pen(ScaleColor, 1);



            int fontSize = 8;



            for (int i = 0; i <= XScaleCount; i++)



            {



                int x = i * (iAreaWidth / XScaleCount) + pAreaStart.X;



                g.DrawLine(penScale, x, pAreaStart.Y, x, pAreaEnd.Y);



                string lbl = (i * (xRange / XScaleCount)).ToString();



                if (xRange == 1440)   //如果按照一天分钟时间显示



                    lbl = (i * (xRange / XScaleCount) / 60).ToString();



                if (i != 0)



                { g.DrawString(lbl, new Font("微软雅黑", fontSize, FontStyle.Regular), new SolidBrush(AxisColor), new Point(x - fontSize, pAreaEnd.Y + VerticalMargin / 9)); }



            }



            for (int i = 0; i <= YScaleCount; i++)



            {



                int y = pAreaEnd.Y - (i * (iAreaHeight / YScaleCount));



                g.DrawLine(penScale, pAreaStart.X, y, pAreaEnd.X, y);



                string lbl = (i * (yRange / YScaleCount)).ToString();



                g.DrawString(lbl, new Font("微软雅黑", fontSize, FontStyle.Regular), new SolidBrush(AxisColor), new Point(pAreaStart.X - (fontSize * lbl.Length) - HorizontalMargin / 9, y - fontSize / 2));



            }



 



            //4.画曲线面积



            //4.1得到数据



            //4.2数据排序 ：为了能顺序画出图，需要对X轴上的数据进行排序  冒泡排序



            List<Point> listPointData = SortingData(data);



 



            //4.3.数据转换：将实际的数据转换到图上的点



            List<Point> listPointGraphics = new List<Point>();//图上的点



            foreach (Point point in listPointData)



            {



                Point p = new Point();



                p.X = pAreaStart.X + Convert.ToInt32((iAreaWidth / xRange) * point.X);     //120为实际值的取值范围0-120



                p.Y = pAreaStart.Y + (iAreaHeight - Convert.ToInt32((iAreaHeight / yRange) * point.Y)); //1000为实际值取值范围0-1000



                listPointGraphics.Add(p);



            }



 



            //4.3将点的集合加入到画曲线图的路径中



            GraphicsPath gpArea = new GraphicsPath();



 



            //第一个点  //起点要从X轴上开始画 结束点也要画回X轴：即在开始点和结束点要多画一次原点的Y



            gpArea.AddLine(new Point(listPointGraphics[0].X, pOrigin.Y), listPointGraphics[0]);



            //中间点



            for (int i = 0; i < listPointGraphics.Count - 1; i++) //注意：超出数组界限时，编译器不会出错



            {



                gpArea.AddLine(listPointGraphics[i], listPointGraphics[i + 1]);



            }



            //最后一个点



            gpArea.AddLine(listPointGraphics[listPointGraphics.Count - 1], new Point(listPointGraphics[listPointGraphics.Count - 1].X, pOrigin.Y));



 



            SolidBrush brush = new SolidBrush(GraphColor);//定义单色画刷 



 



 



            if (isFill)



            {



                g.FillPath(brush, gpArea);   //填充



            }



            else



            {



                g.DrawPath(new Pen(GraphColor, 5), gpArea);  //边缘 



            }



 



            gpArea.CloseFigure();  //是否封闭



 



            return CurrentImage;



        }



 



        /// <summary>



        /// 数据排序



        /// </summary>



        /// <param name="lp"></param>



        /// <returns></returns>



        private List<Point> SortingData(List<Point> lp)



        {



            for (int i = 0; i < lp.Count - 1; i++)



            {



                for (int j = 0; j < lp.Count - 1 - i; j++)// j开始等于0，  



                {



                    if (lp[j].X > lp[j + 1].X)



                    {



                        Point temp = lp[j];



                        lp[j] = lp[j + 1];



                        lp[j + 1] = temp;



                    }



                }



            }



            return lp;



        }



    }
```



###      在FORM1中进行调用演示

​      再演示中都是使用随机数生成的一些数据。直接画在Form1的窗体上。

​      1.单纯曲线图

​      

```csharp
    public partial class Form1 : Form



    {                     



        public Form1()



        {



            InitializeComponent();



        }



 



        private void Form1_Load(object sender, EventArgs e)



        {



            LoadingUI();



        }



 



        GraphEdit graphEdit;



        Color boardColor = Color.FromArgb(17, 81, 138);//指定绘制图的背景色  



        Thread toUpdate;                               //刷新线程



        private void LoadingUI()



        {



            graphEdit = new GraphEdit(640,350 , boardColor);



            graphEdit.HorizontalMargin = 50;                                   //横水平边距



            graphEdit.VerticalMargin = 80;                                     //竖垂直边距



            graphEdit.AreasColor = Color.FromArgb(100, 0, 0, 0);         //画图区域颜色



            graphEdit.GraphColor = Color.FromArgb(255, 110, 176);        //曲线面积颜色



            graphEdit.AxisColor = Color.FromArgb(255, 255, 255);         //坐标轴颜色



            graphEdit.ScaleColor = Color.FromArgb(20, 255, 255, 255);          //刻度线颜色



 



            graphEdit.XScaleCount = 24;          //X轴刻度线数量



            graphEdit.YScaleCount = 10;          //Y轴刻度线数量



            toUpdate = new Thread(new ThreadStart(Run));



            toUpdate.Start();



        }



 



        private void Run()



        {



            while (true)



            {



                Image image = graphEdit.GetCurrentGraph(this.GetBaseData(), XRange, YRange,false);  //如果是面积曲线图将最后一个参数设为true



                Graphics g = this.CreateGraphics();  //指定使用那个控件来接受曲线图



 



                g.DrawImage(image, 0, 0);



                g.Dispose();



                Thread.Sleep(500);                 //每2秒钟刷新一次  



            }



        }



 



        float XRange = 1440;   //X轴最大范围（0-1440）



        float YRange = 500;    //Y轴最大范围（0-500）



 



        /// <summary>



        /// 得到（数据库）数据



        /// </summary>



        /// <returns></returns>



        private List<Point> GetBaseData()



        {



            Random r = new Random();



            List<Point> result = new List<Point>();  //数据



            for (int i = 0; i < XRange-200; i+=30)



            {



                Point p;



                if(i<100)



                p = new Point(i, r.Next(180, 200));



                else



                    p = new Point(i, r.Next(200, 220));



                result.Add(p);



            }



            return result;



        }



 



        private void Form1_FormClosing(object sender, FormClosingEventArgs e)



        {



            try



            {



                toUpdate.Abort();



            }



            catch (Exception)



            {



                Environment.Exit(0);



            }



        }



    }
```

 效果图

![img](https://www.freesion.com/images/872/087de7696f0f49e7e4a55359f23e7ac0.png)

2.面积曲线图

代码只需要修改下面这行代码

```csharp
  Image image = graphEdit.GetCurrentGraph(this.GetBaseData(), XRange, YRange,true);  //如果是面积曲线图将最后一个参数设为true
```

效果图：

![img](https://s2.loli.net/2022/07/09/wxOWmkvKtdufr3E.png)