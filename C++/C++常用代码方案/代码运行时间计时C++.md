# c++实现代码运行时间计时
```
#include<ctime>
void func(){
   clock_t clock_start=clock();
    /*
    type your code here
    */
    clock_t clock_end=clock();
    double runningTime=(double)(clock_end-clock_start)/CLOCKS_PER_SEC;
    cout<<runningTime<<endl;
}
```