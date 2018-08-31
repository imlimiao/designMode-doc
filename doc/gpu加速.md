####  GUP加速原理
> 网页的渲染过程
 
    1. 获取dom 分割成多个层
    2. 对每个图层的节点计算样式结果 （Recalculate style）
    3. 为每个节点生成图形和位置 layout 
    4. 将每个节点绘制并填充到图形的位置中 paint 
    5. 图层作为纹理上传到GPU
    6. 符合条件的多图层生成屏幕的图像  Composite layer 合成层
    7.浏览器渲染过程 layout -> paint -> composite layer （触发的阶段越高，代价越高）

    独立的层 -->重排重绘 不会影响父层
    
 > 哪些些会生成独立的层
 
    1. 根元素 position transform 半透明 canvas video overflow 滤镜...

 > 哪些属性可以开启GPU加速 
 
 	 - 虽然生成独立的层，但是还是cpu一直在计算重绘重排，再传到GPU 进行合成
    - 开启GPU加速后 就省去CPU重排重绘的过程 GPU(图形处理器)擅长图形处理   (gpu.js)
    - css3d video webgl transfrom css滤镜 flash z-index大于某个相邻节点的layer元素... 
    
> 硬件加速（GPU加速）
 
    - 术语：Texture 即CPU传到GPU的一个Bitmap
    - GPU 能对 快速对Texture 进行 偏移 旋转，缩放，修改透明度等
    - 节约了 
        1. CPU进行layout paint 的时间 
        2. CPU上传位图的时间
    
 > CPU VS GPU
 
    - 相同：都有总线与外界联系，有自己的缓存体系，以及数字与逻辑运算单元，两者都是为了完成计算任务而设计的
    - 不同：CPU 主要处理操作系统和应用程序 GPU主要处理跟显示相关的数据处理， GPU的活一般CPU都能干，但是效率低
    
 > layer模型
 
    - 浏览器根据css属性生成layer
    - 将layer作为Texture上传GPU
    - 当改变layer的transfrom opacity 等属性，渲染会跳过 layout paint 直接通知GPU做出改变
    