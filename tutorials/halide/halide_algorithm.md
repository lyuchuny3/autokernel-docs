# Halide算法简述 
每个Halide程序都有两个部分，一个算法和一个调度策略。 该算法定义在每个像素处计算的内容。 在这里，我们介绍了Halide提供的用于编写算法的构造。    

## Func  
Func代表一个pipeline。 它描述了像素应具有的值。 Func由内存缓冲区支持，可以将其视为映像。 在下面的示例中，f是一个Func，用于计算图像，其中每个像素都是其x和y坐标之和。   
```  
f(x, y) = x + y;   
```   
一个func可以是另一个func的输入。 在这种情况下，可以通过以供需关系级联多个Funcs（每个都是一个pipeline）来构建。     
以下示例是用Halide表示的高斯模糊过滤器：     
```   
bounded_input(x, y) = BoundaryConditions::repeat_edge(input)(x, y);   
input_16(x, y) = cast<int16_t>(bounded_input(x, y));    
input_16(x, y) = cast<int16_t>(input(x, y));      
rows(x, y) = input_16(x, y-2) + 4*input_16(x, y-1) + 6*input_16(x,y) + 4*input_16(x,y+1) + input_16(x,y+2);    
cols(x,y) = rows(x-2, y) + 4*rows(x-1, y) + 6*rows(x, y) + 4*rows(x+1, y) + rows(x+2, y);    
output(x, y) = cast<uint8_t> (cols(x, y) >> 8);     
```  
Bounded_input，input_16，rows，cols和output均为Funcs，每个均代表此5x5高斯模糊过滤器的一个阶段。输出是pipeline的最后阶段，与输出关联的内存缓冲区包含模糊的图像。    

## Var   
Var是在定义Func时用作变量的名称。 可以将Func定义为Var x，y：    
```
blur_x(x , y) = (input(x-1, y) + input(x, y) + input(x+1, y))/3;   
```
在此示例中，x和y本身没有意义。 但是，当与blur_x和输入Funcs一起使用时，它们表示这些Funcs的两个维度。    

## Expr  
Halide中的Expr由Funcs，Vars和其他Expr组成，例如：Halide中的Exprs.    
```
Expr e = x + y;     
Output(x, y) = 3*e + x;     
```
____
在这些示例中，x和y是Vars，e是Expr，而Output是Func.  
