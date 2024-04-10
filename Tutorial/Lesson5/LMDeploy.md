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

![image-20240410140921314](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410140921314.png)

- 模型存储的格式
  - HF格式：模型的存储以Huggingface格式存储，托管在HuggingFace社区、阿里巴巴的[MindScope](https://www.modelscope.cn/home)社区，或者上海AI Lab搭建的[OpenXLab](https://openxlab.org.cn/home)社区上面的模型存储格式都是HF格式。
  - TurboMind是LMDeploy团队开发的一款关于LLM推理的高效推理引擎，它的主要功能包括：LLaMa 结构模型的支持 continuous batch 推理模式和可扩展的 KV 缓存管理器。
  - TurboMind格式：TurboMind推理引擎仅支持的模型格式。因此，TurboMind在推理HF格式的模型时，会首先自动将HF格式模型转换为TurboMind格式的模型。**该过程在新版本的LMDeploy中是自动进行的，无需用户操作。**

- 模型下载

```
ln -s /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b /root/models/Shanghai_AI_Laboratory/
```

#### 使用Transformer进行推理

在root/demo下创建推理脚本`touch /root/demo/pipeline_transformer.py`

![image-20240410142030895](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410142030895.png)

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

tokenizer = AutoTokenizer.from_pretrained("/root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b", trust_remote_code=True)

model = AutoModelForCausalLM.from_pretrained("/root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b", torch_dtype=torch.float16, trust_remote_code=True).cuda()
model = model.eval()

inp = "hello"
print("[INPUT]", inp)
response, history = model.chat(tokenizer, inp, history=[])
print("[OUTPUT]", response)

inp = "please provide three suggestions about time management"
print("[INPUT]", inp)
response, history = model.chat(tokenizer, inp, history=history)
print("[OUTPUT]", response)

```

- 推理过程

![image-20240410142550562](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410142550562.png)

#### 使用LMDeploy进行推理

- 命令格式：lmdeploy chat [HF格式模型路径/TurboMind格式模型路径]
- 执行命令推理：lmdeploy chat /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b

![image-20240410143518230](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410143518230.png)

- 输入后回车两次表示输入结束，进行生成
- 输入exit表示退出
- lmdeploy chat -h 查看默认参数

![image-20240410144037216](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410144037216.png)

### LMDeploy量化

- 量化是一种以参数或计算中间结果精度下降换空间节省（以及同时带来的性能提升）的策略

- 针对Decoder Only访存密集问题使用 **KV Cache 量化**和 **4bit Weight Only 量化（W4A16）**
  - KV Cache 量化是指将逐 Token（Decoding）生成过程中的上下文 K 和 V 中间结果进行 INT8 量化（计算时再反量化），以降低生成过程中的显存占用
  - 4bit Weight 量化，将 FP16 的模型权重量化为 INT4，Kernel 计算时，访存量直接降为 FP16 模型的 1/4，大幅降低了访存成本，计算时反量化为FP16

- 模型在运行时，占用的显存可大致分为三部分：模型参数本身占用的显存、KV Cache占用的显存，以及中间运算结果占用的显存

- LMDeploy的KV Cache管理器可以通过设置`--cache-max-entry-count`参数，控制KV缓存**占用剩余显存**的最大比例。
  - 默认的比例为0.8。

#### KV Cache量化

- --cache-max-entry-count 设置为0.01
- 约等于禁止KV Cache占用显存，代价是会降低模型推理速度

```
lmdeploy chat /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b --cache-max-entry-count 0.01
```

![image-20240410144952230](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410144952230.png)

- --cache-max-entry-count 设置为0.5
- 表示KV-Cache占用剩余显存的50%
- 60.21%  约等于 19.18% + （80% * 0.5）

![image-20240410145135189](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410145135189.png)

- 不设置 --cache-max-entry-count 
- 表示KV-Cache默认占用剩余显存的80%
- 85.22%  约等于 19.18% + （80% * 0.8）

![image-20240410145511574](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410145511574.png)

#### 使用W4A16量化

- LMDeploy使用AWQ算法（激活感知权重量化），实现模型4bit权重量化。保留对LLM重要的权重，其余权重进行4bit量化

- 安装量化依赖
  - `pip install einops==0.7.0`

```
lmdeploy lite auto_awq \
   /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \
  --calib-dataset 'ptb' \
  --calib-samples 128 \
  --calib-seqlen 1024 \
  --w-bits 4 \
  --w-group-size 128 \
  --work-dir /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit
```

- 参数详解
  - auto_awq  表示量化算法
  - calib-dataset 表示量化时统计数据用到的数据集
  - calib-samples 表示采样128
  - calib-seqlen 表示每条样本的长度
  - w-bits 量化大小
  - w-group-size  量化分组统计的尺寸
  - work-dir 量化后模型的输出地址

- 运行量化后的模型

```
lmdeploy chat /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit --model-format awq
```

![image-20240410152603724](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410152603724.png)

- 量化后的显存占用
  - 显存占用下降一半

```
lmdeploy chat /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit --model-format awq --cache-max-entry-count 0.01
```

![image-20240410152805758](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410152805758.png)

### LMDeploy服务

- 服务架构模型图
  - 模型推理/服务。主要提供模型本身的推理，一般来说可以和具体业务解耦，专注模型推理本身性能的优化。可以以模块、API等多种方式提供。
  - API Server。中间协议层，把后端推理/服务通过HTTP，gRPC或其他形式的接口，供前端调用。
  - Client。可以理解为前端，与用户交互的地方。通过通过网页端/命令行去调用API接口，获取模型推理/服务。

![image-20240410153615839](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410153615839.png)

- 启动API服务
  - quant-policy 默认值为 0，表示不使用 KV Cache，如果需要开启，则将该参数设置为 4
  - tp 表示显卡数量

```
lmdeploy serve api_server \
    /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \
    --model-format hf \
    --quant-policy 0 \
    --server-name 0.0.0.0 \
    --server-port 23333 \
    --tp 1
```

- 查看默认接口

![image-20240410154415276](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410154415276.png)

#### 用API接口访问

![image-20240410154729201](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410154729201.png)

```
lmdeploy serve api_client http://localhost:23333
```

![image-20240410155103423](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410155103423.png)

#### 网页客户端访问API服务器（Gradio）

![image-20240410155201159](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410155201159.png)

- 前提要开启API服务

```
lmdeploy serve gradio http://localhost:23333 \
    --server-name 0.0.0.0 \
    --server-port 6006
```

![image-20240410155608368](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410155608368.png)

#### 集成到python代码中

![image-20240410161847666](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410161847666.png)

```
python /root/demo/pipeline.py
```

![image-20240410162327287](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410162327287.png)

#### 通过代码向TurboMind传递参数

![image-20240410162711778](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240410162711778.png)
