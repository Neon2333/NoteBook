# 0. 前言

## 1.GDI+和GDI

GDI/GDI+是对DX的封装。

GDI：如果不考虑绘图的效果，使用Win32 GDI函数直接绘图的效率大约是同样的GDI+的10倍以上。属于操作系统核心层，效率方面不用担心。

GDI+：GDI+效果好于GDI，性能弱于GDI，使用的是CPU，不使用GPU硬件加速。

DX对等OpenGL。

Direct2d使用GPU性能好，能够60PFS。

GDI+循环绘制一定会跑满CPU的一个线程占用，**适合绘制GUI，不适合高频刷新绘制**。

**WPF是使用GPU加速的。**



## 2. GDI+效率问题

https://blog.csdn.net/jiqc1211/article/details/19768835

https://blog.51cto.com/zjyzjy/67370

GDI+的效率其实没有GDI的效率高的, 一般的提高效率的方法也就那么多:

* 减少不必要的绘制, 区域刷新效果比全部绘制效率高很多, 比如只是一个控件需要重绘,我们就不应该让整个窗口或者说大于次区域的界面无效
* 不在显示区域的绘制工作能省就省, 主要绘制可视界面, 就是平时说的 [脏矩形技术](http://www.360doc.com/content/12/0217/17/110467_187409816.shtml)
* 使用内存绘制, 然后直接贴图(或者截取贴图), 另外如果某些工作是不变的,比如说背景, 那么就在第一次的时候绘制到内存dc, 以后直接取用
* 如果有可能可以使用Directx或者openGL

### （1）双缓冲

基本原理就是：先在内存中开辟一块虚拟画布，然后将所有需要画的图形先画在这块“虚拟画布”上，最后在一次性将整块画布画到真正的窗体上。因为所有的单个图形的绘制都不是真正的调用显示系统来“画”，所以不会占用显示系统的开销，极大的提高的绘图效率。

```c#
实现双缓冲的具体步骤
我再来详细解释一下刚才实现双缓冲的具体步骤：

1、在内存中建立一块“虚拟画布”：
Bitmap bmp = new Bitmap(600, 600);
2、获取这块内存画布的Graphics引用：
Graphics g = Graphics.FromImage(bmp);
3、在这块内存画布上绘图：
g.FillEllipse(brush, i * 10, j * 10, 10, 10);
4、将内存画布画到窗口中
this.CreateGraphics().DrawImage(bmp, 0, 0);
```



---

# 1. GDI绘图基础

https://www.bilibili.com/video/BV1gV411K7ej/?spm_id_from=333.337.search-card.all.click&vd_source=3b08e97e50222fa2ec22737f6dcb2202

https://www.bilibili.com/video/BV1PL411h7UF/?spm_id_from=333.337.search-card.all.click&vd_source=3b08e97e50222fa2ec22737f6dcb2202

DrawCurve的参数tension（张力）：https://www.cnblogs.com/zhongxinWang/archive/2012/10/26/2740765.html

## （1）函数



| 函数                                              | 参数说明                                  | 函数描述 |
| ------------------------------------------------- | ----------------------------------------- | -------- |
| Graphics CreateGraphics()                         |                                           | 创建画布 |
| DrawLine()                                        |                                           |          |
| DrawCurve(Pen pen, Point[] points, float tension) | tension在0~1.0，表示曲线光滑度，0就是直线 | 绘制曲线 |



## （2）重绘

Invidate()







---

# 2. 绘图方法

## 坐标轴绘制

```c#

```



## 鼠标自由绘制曲线

不需要设置帧率刷新画布，鼠标Move时就会触发事件调用曲线绘制函数。

**为了减少毛刺、锯齿，设置pen的`StartCap`/`EndCap`/`LineJoin`属性为Round，可使得曲线变得平滑。**

```c#
pen.StartCap = System.Drawing.Drawing2D.LineCap.Round;
pen.EndCap = System.Drawing.Drawing2D.LineCap.Round;
pen.LineJoin = System.Drawing.Drawing2D.LineJoin.Round;

private void FreeDraw(Pen pen, Graphics graphics)
{
	if (flagLeftDown && (pXFormer != pX || pYFormer != pY))
	{
		//graphics.DrawLine(pen, pXFormer, pYFormer, pX, pY);
        graphics.DrawCurve(pen, new Point[]{new Point(pXFormer, pYFormer), new Point(pX, pY)}, 1.0);
        //更新坐标（只有画过线的坐标才更新，这样不会点和点之间不会有间断）
		pXFormer = pX;
		pYFormer = pY;
	}
}

private void panel_freeDraw_MouseMove(object sender, MouseEventArgs e)
{
    FreeDraw(pen, graphics);
}
```

## 根据坐标数据绘制动态曲线



# 3. 绘制自定义控件

https://www.bilibili.com/video/BV1PL411h7UF/?spm_id_from=333.337.search-card.all.click&vd_source=3b08e97e50222fa2ec22737f6dcb2202