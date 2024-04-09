### LMDeploy

#### 大模型部署背景

![image-20240409132614489](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409132614489.png)

#### 大模型部署难点

- 大模型部署的难点-计算量巨大

![image-20240409132645554](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409132645554.png)

- 大模型部署难点-内存开销巨大

![image-20240409132837295](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409132837295.png)

- 大模型部署难点-访存瓶颈

![image-20240409133132366](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409133132366.png)

#### 大模型部署方法

- 模型剪枝

![image-20240409133907429](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409133907429.png)

- 知识蒸馏

![image-20240409134013443](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134013443.png)

- 模型量化

![image-20240409134112851](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134112851.png)

#### LMDeploy

![image-20240409134520182](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134520182.png)

- 核心功能

![image-20240409134540672](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134540672.png)

![image-20240409134856284](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134856284.png)

![image-20240409134922284](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409134922284.png)

### 部署推理实践

- 创建虚拟环境

```
studio-conda -t lmdeploy -o pytorch-2.1.2     # 创建虚拟环境lmdeploy
conda activate lmdeploy                       # 激活虚拟环境
pip install lmdeploy[all]==0.3.0              # 安装lmdeploy包
```

