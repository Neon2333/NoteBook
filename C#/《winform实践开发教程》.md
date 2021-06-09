1. P18将listbox的Items.Add放在Load事件中

2. ShowDialog显示模态窗体，只有关闭窗体才执行后面代码

3. P21将窗体隐藏，如果后面还要用。用hide还是visible=false更好？？

4. MDI窗体不同于SDI，就是窗体间有父子和兄弟关系的。父窗体可以对子窗体层叠、平铺等。

   一般在父窗体的load事件中new子窗体，然后设置子窗体的父窗体

   ```C#
   Form2 childForm = new Form2();
   childForm.Mdiparent = this;
   childForm.Show();
   ```

   子窗体只能在父窗体范围内活动，不能超出范围。

* 指定层叠、水平平铺、垂直平铺等

```C#
this.LayoutMdi(MdiLayout.Cascade);	//层叠
this.LayoutMdi(MdiLayout.TileHorizontal);	//水平平铺
this.LayoutMdi(MdiLayout.TileVertical);	//垂直平铺
```

* MDI主窗口作为父窗体，非MDI窗体作为子窗体。子窗体之间的传值问题。

