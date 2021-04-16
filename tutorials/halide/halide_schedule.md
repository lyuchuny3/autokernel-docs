# Halide调度策略Schedule
Schedule 指定如何构造/组织算法计算。   
Halide主要有两种Schedule：  
+ 阶段内调度   
+ 阶段间调度   

## 阶段内调度：Domain Order   
一个阶段内的Schedule使用一组传统的循环转换概念来定义函数迭代域的顺序，这些概念应用于函数的维。在Halide的调度语言中，这些选择被用作每个功能的注释。  

- 尺寸可以按顺序（默认）或平行于“ f.parallel（y）”遍历。  
- 固定的尺寸可以展开为f.unroll（x）或矢量化为f.vectorize（x）  
- 尺寸可以重新排序（例如，从列到行：`f.reorder（y，x）`）。 
- 尺寸可以按某些因子进行拆分，从而创建两个新尺寸：外部尺寸和内部尺寸（`f.split（x，exer，inner，factor）`。 
- 可以融合尺寸，将两个尺寸转换为一个（f.fuse（x，y））。 

| 原语    | 描述 |
|--------------|--------------------|
| parallel (x,size)      | 按大小拆分尺寸，并使外部尺寸平行。    | |
| vectorizer(x) |将x指定的尺寸向量化。 ||
| unroll(x) |展开由x指定的尺寸。           ||
| reorder(dim_inner,…,dim_outer)   |将Func的尺寸从dim_inner（most）重新排序为dim_outer（most）。            | |
| split(x, outer,inner, factor)  |将x指定的尺寸分为内部尺寸和外部尺寸。 内部维数从0迭代到factor-1。||
| fuse (inner,outer, fused)|将变量内部和外部指定的尺寸融合到由融合指定的单个尺寸中。||
|tile (x,y, xo,yo, xi, yi,xfactor, yfactor)|通过x_factor和y_factor给变量x和y指定的尺寸分块。||
