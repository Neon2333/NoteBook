# 1. 多线程

---

> 在32位系统中，每个进程对应1个虚拟地址空间，	从0开始，到4G结束。用户区占3G，1G属于内核。
>
> 程序无法访问物理内存，程序中打印的变量的地址都是虚拟地址空间中的地址，而不是实际在内存的位置。
>
> ![image-20240427134742787](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240427134742787.png)
>
> ![image-20240427135051567](https://raw.githubusercontent.com/Neon2333/ImageHost/main/image-20240427135051567.png)
>
> 多线程调试复杂，线程数够用就行，不要创建太多。

