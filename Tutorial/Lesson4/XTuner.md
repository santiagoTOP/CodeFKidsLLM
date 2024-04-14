## XTuner

[文档链接](https://github.com/InternLM/Tutorial/blob/main/xtuner/README.md)

### 如何得到一个垂直领域模型

![image-20240402100042376](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100042376.png)

### 为什么要进行指令微调

![image-20240402100142509](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100142509.png)

### 指令跟随微调

- 指定角色定义：根据微调的目标领域写

![image-20240402100309823](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100309823.png)

#### 对话模板构建

![image-20240402100631424](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100631424.png)

#### 训练

![image-20240402100756924](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100756924.png)

### 增量预训练微调

- 只需要将角色指定喝用户输入指定为空就可以了，只计算输出的损失

![image-20240402100956766](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402100956766.png)

### LoRA & QLoRA

![image-20240402101303882](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402101303882.png)

![image-20240402101422125](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402101422125.png)

### XTuner框架

![image-20240402101731208](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402101731208.png)

### XTuner 使用

![image-20240402102159333](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102159333.png)

![image-20240402102237535](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102237535.png)

![image-20240402102303384](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102303384.png)

![image-20240402102400223](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102400223.png)

![image-20240402102431944](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102431944.png)

![image-20240402102531271](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102531271.png)

![image-20240402102610576](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102610576.png)

![image-20240402102646659](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240402102646659.png)