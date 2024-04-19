### LMDeploy

[文档链接](https://github.com/InternLM/Tutorial/blob/camp2/lmdeploy/README.md)

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

### 进阶作业

- 设置KV Cache最大占用比例为0.4，开启W4A16量化，以命令行方式与模型对话。（优秀学员必做）

  - 第一步：开启W4A16量化、

  - ```
    lmdeploy lite auto_awq \
       /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \
      --calib-dataset 'ptb' \
      --calib-samples 128 \
      --calib-seqlen 1024 \
      --w-bits 4 \
      --w-group-size 128 \
      --work-dir /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit
    ```

  - 第二步：设置KV Cache  `--cache-max-entry-count 0.4`

  - 第三步：以命令行方式与模型对话
  
  - ```
    lmdeploy chat /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit --model-format awq --cache-max-entry-count 0.4
    ```
  
  ![image-20240411134851425](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411134851425.png)

- 以API Server方式启动 lmdeploy，开启 W4A16量化，调整KV Cache的占用比例为0.4，分别使用命令行客户端与Gradio网页客户端与模型对话。（优秀学员）

  - 第一步：开启API服务

  - ```
    lmdeploy serve api_server \
        /root/models/Shanghai_AI_Laboratory/internlm2-chat-1_8b-4bit \
        --model-format awq \
        --cache-max-entry-count 0.4 \
        --quant-policy 0 \
        --server-name 0.0.0.0 \
        --server-port 23333 \
        --tp 1
    ```

  - ![image-20240411141420510](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411141420510.png)

  - 第二步：命令客户端访问

  - ```
    lmdeploy serve api_client http://localhost:23333
    ```

  - ![image-20240411141605806](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411141605806.png)

  - 第三步：Gradio网页客户端

  - ```
    lmdeploy serve gradio http://localhost:23333 \
        --server-name 0.0.0.0 \
        --server-port 6006
    ```

  - ![image-20240411141842196](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411141842196.png)

  - ![image-20240411141924031](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411141924031.png)

- 使用W4A16量化，调整KV Cache的占用比例为0.4，使用Python代码集成的方式运行internlm2-chat-1.8b模型。（优秀学员必做）

  - 第一步：创建python脚本

  - ```
    touch /root/demo/awq_KV_Cache.py
    ```

  - 第二步：编写脚本

  - ![image-20240411142830485](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411142830485.png)
  
  - 第三步：运行脚本`python /root/demo/awq_KV_Cache.py`
  - ![image-20240411142945764](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411142945764.png)

- 使用 LMDeploy 运行视觉多模态大模型 llava gradio demo （优秀学员必做）

  - 第一步：安装llava依赖包

  - ```
    pip install git+https://github.com/haotian-liu/LLaVA.git@4e2277a060da264c4f21b364c867cc622c945874
    ```

  - 第二步：创建脚本

  - ```
    touch /root/demo/pipline_llava.py
    ```

  - 第三步：边写脚本

  - ```
    from lmdeploy.vl import load_image
    from lmdeploy import pipeline, TurbomindEngineConfig
    
    
    backend_config = TurbomindEngineConfig(session_len=8192) # 图片分辨率较高时请调高session_len
    # pipe = pipeline('liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config) 非开发机运行此命令
    pipe = pipeline('/share/new_models/liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config)
    
    image = load_image('https://raw.githubusercontent.com/open-mmlab/mmdeploy/main/tests/data/tiger.jpeg')
    response = pipe(('describe this image', image))
    print(response)
    ```

![image-20240411145721919](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411145721919.png)

- Gradio启动

  - 脚本编写

  - ```
    import gradio as gr
    from lmdeploy import pipeline, TurbomindEngineConfig
    
    
    backend_config = TurbomindEngineConfig(session_len=8192) # 图片分辨率较高时请调高session_len
    # pipe = pipeline('liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config) 非开发机运行此命令
    pipe = pipeline('/share/new_models/liuhaotian/llava-v1.6-vicuna-7b', backend_config=backend_config)
    
    def model(image, text):
        if image is None:
            return [(text, "请上传一张图片。")]
        else:
            response = pipe((text, image)).text
            return [(text, response)]
    
    demo = gr.Interface(fn=model, inputs=[gr.Image(type="pil"), gr.Textbox()], outputs=gr.Chatbot())
    demo.launch()
    ```

  ![image-20240411150921367](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240411150921367.png)

### 将模型部署到OpenXLab上

#### GIT安装

- 安装git以及lfs(Large File Storage)用于模型的部署，使用sudo需要root权限，这里在试验机上没有使用root安装

- ```
  # install git
  sudo apt-get update
  sudo apt-get install git
  
  # install git lfs
  sudo apt-get update
  sudo apt-get install git-lfs
  
  # use git install lfs
  git lfs install
  ```

![image-20240418195052983](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418195052983.png)

 #### 配置git账户信息

- 用于与openxlab与本地git交互

- 配置用户名

- ```
  git config --global user.name "TNGPNG"
  ```

- 配置邮箱

- ```
  git config --global user.email "1537211712top@gmail.com"
  ```

- 注意用户名是openxlab上的用户名

![image-20240418195613774](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418195613774.png)

#### 在openxlab上创建仓库

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418200154542.png" alt="image-20240418200154542" style="zoom:50%;" />

- 获取下载地址，clone到本地

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418200353965.png" alt="image-20240418200353965" style="zoom: 50%;" />

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418200548311.png" alt="image-20240418200548311" style="zoom:50%;" />

####  获取 Git Access Token

- 用于后续的模型上传时需要
- <img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418201000168.png" alt="image-20240418201000168" style="zoom: 67%;" />

- <img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418201104141.png" alt="image-20240418201104141" style="zoom:67%;" />

#### 上传模型到openxlab

- 第一步需要将模型文件复制到本地的仓库里面

- ```
  cp -r share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b/* /root/InternLM2-chat-7B/
  ```

![image-20240418201638191](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418201638191.png)

- **LFS管理大文件**：使用 `git lfs track` 命令来标记你希望通过 Git LFS 管理的大文件

- ```
  # 表示标记bin和model结尾的文件
  git lfs track "*.bin"
  git lfs track "*.model"
  ```

  ![image-20240418201935723](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418201935723.png)

- 提交仓库的更新信息并进行推送

- ```
  cd internlm2-chat-7b
  git add -A
  git commit -m "upload model"
  git push
  ```

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418202443960.png" alt="image-20240418202443960" style="zoom:50%;" />

- 查看远程仓库的模型文件

![image-20240418202605332](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418202605332.png)

### 代码仓库

- 在github上创建一个新的仓库用于代码的存储

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418203321218.png" alt="image-20240418203321218" style="zoom:50%;" />

- clone到本地并进行编写

![image-20240418204638387](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418204638387.png)

- 推送模型到远程仓库

- ```
  cd internlm2-chat-7b-git
  git add -A
  git commit -m "add app.py requirements.txt packages.txt"
  git push
  ```

![image-20240418205044940](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418205044940.png)

### 应用创建

![image-20240418205409741](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418205409741.png)

![image-20240418205504792](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418205504792.png)

- 应用创建成功并对话

![image-20240419200140300](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240419200140300.png)

- 将应用公开，并查看后台数据

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240419200357152.png" alt="image-20240419200357152" style="zoom:50%;" />

<img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240419200308856.png" alt="image-20240419200308856" style="zoom:50%;" />

