# Algorithm of Halide  
Every Halide program has two parts, an algorithm and a schedule. The algorithm defines what is computed at each pixel. Here we present the constructs that Halide provides to write an algorithm.  

## Func  
A Func represents a pipeline stage. It describes what value a pixel should have. A Func is backed by a memory buffer, which can be thought of as an image. In the following example, f is a Func that is used to compute an image where every pixel is the sum of its x and y coordinates.   
```  
f(x, y) = x + y;   
```   
A Func can be an input to another Func. In this case, an image processing pipeline can be constructed by cascading multiple Funcs (each of which is a pipeline stage) in producer-consumer relationships.    
The following example is a Gaussian Blur filter represented in Halide:  
```   
bounded_input(x, y) = BoundaryConditions::repeat_edge(input)(x, y);   
input_16(x, y) = cast<int16_t>(bounded_input(x, y));    
input_16(x, y) = cast<int16_t>(input(x, y));      
rows(x, y) = input_16(x, y-2) + 4*input_16(x, y-1) + 6*input_16(x,y) + 4*input_16(x,y+1) + input_16(x,y+2);    
cols(x,y) = rows(x-2, y) + 4*rows(x-1, y) + 6*rows(x, y) + 4*rows(x+1, y) + rows(x+2, y);    
output(x, y) = cast<uint8_t> (cols(x, y) >> 8);     
```  
Bounded_input, input_16, rows, cols, and output are all Funcs, each representing a stage of this 5x5 Gaussian blur filter. Output is the final stage of the pipeline, and the memory buffer that is associated with output contains the blurred image.     

## Var   
A Var is a name to use as a variable when defining a Func. A Func can be defined as Var x,y:   
```
blur_x(x , y) = (input(x-1, y) + input(x, y) + input(x+1, y))/3;   
```
In this example, x and y have no meaning on their own. However, when used with the blur_x and input Funcs, they signify the two dimensions of these Funcs.    

## Expr  
An Expr in Halide is composed of Funcs, Vars, and other Exprs, for example: of Exprs in Halide.    
```
Expr e = x + y;     
Output(x, y) = 3*e + x;     
```
____
In these examples, x and y are Vars, e is an Expr, and Output is a Func.
