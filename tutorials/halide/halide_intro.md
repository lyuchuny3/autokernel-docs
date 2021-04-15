# Halide初探  
Halide是视觉和图像处理算法的特定领域语言，允许程序员编写有效的图像处理pipeline。 Halide将程序分为两个概念部分：  
■ 算法（algorithm）–定义在每个像素处要计算的内容  
■ 布局策略（schedule）–定义应如何组织计算  
每个Halide程序都必须由这两部分组成，并且用户必须同时指定这两个部分。下面的示例是在Halide中编写的水平模糊程序：  
```
// Image with 8 bits per pixel.  
ImageParam input(UInt(8), 2) Halide::Func f;    
// Algorithm.   
f(x, y) = (input(x-1, y) + input(x, y) + input(x+1, y))/3;    
// Schedule   
f.hexagon().vectorize(x, 8).parallel(y, 16);  
```
该算法根据像素的x和y坐标简洁地定义了模糊计算。而schedule指示应如何进行计算。在本示例中，程序员指示Halide将模糊算法矢量化8像素，将算法并行化16倍，然后在Hexagon HVX处理器上启动它。   
Halide HVX编译器自动将程序员编写的高级图像算法转换为高效的HVX可执行文件。   
[Halide社区](https://halide-lang.org/tutorials/tutorial_introduction.html)上托管了丰富的教程集，想要对Halide有更多了解，欢迎自行学习。 
