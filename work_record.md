## 2024/05/02

1、把杨金博的ch7跑起来；

2、下载rcore_tutorial代码；

3、研究polyhal中的examples；

4、研究杨金博的ch7实现；

5、用polyhal实现rcore_tutorial的ch1

## 2024/04/30

在拆分和编写memtest，exception，task/yield的CI时出现了与log有关的bug，因为log的“0.4.21”版本支持macro_use的宏定义时会出现未知bug，所以删除了axtask模块中的macro_use关键字,将所有的info!,trace!,debug!宏都加了log::，更好的解决方案应该是锁定log为低版本，但没有找到方法。