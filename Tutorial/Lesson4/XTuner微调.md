## XTuner微调

[文档链接](https://github.com/InternLM/Tutorial/blob/camp2/xtuner/readme.md)  [视频链接](https://www.bilibili.com/video/BV15m421j78d/?spm_id_from=333.788&vd_source=1e18dfc082e362f5bc2e775c28dc3dae) [XTuner](https://github.com/InternLM/xtuner)

### 为什么微调

- 为了更好的适应下游任务
- 微调的方式：增量微调和指令微调
  - 增量预训练微调是在基座模型的基础上，采用垂直领域的数据，使用预训练的方式进行继续预训练
  - 其目的是为了使模型学习到垂直领域里面的知识
  - 指令微调是让模型学会如何与人类对话，他需要去理解问题
- Continue PreTraining(增量预训练): 为了给模型注入领域知识，就需要用领域内的语料进行继续的预训练。-
- SFT( Supervised Finetuning,有监督微调): 通过SFT可以激发大模型理解领域内的各种问题并进行回答的能力(在有召回知识的基础上)
-  RLHF(奖励建模、强化学习训练): 通过RLHF可以让大模型的回答对齐人们的偏好，比如行文的风格。
- DPO(直接偏好优化)
- 增量预训练与指令微调损失函数的不同
  - 增量预训练计算的是整句话的损失
  - 指令微调计算的是输出的损失

![image-20240415191356656](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415191356656.png)

- 增量预训练与指令微调的不同
  - 指令微调以后模型能够很好的进行指令跟随，理解问题并作出合理的回复。

![image-20240415192233224](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415192233224.png)

### 数据处理

![image-20240415192614753](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415192614753.png)

原始数据：网上获取的数据或者其他来源的数据

标准格式数据：将原始数据进行进一步的处理，转化为统一的格式数据

![image-20240415192835474](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415192835474.png)

![image-20240415192848837](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415192848837.png)

对话模板：其目的是为了让模型可以区分出系统提示、用户输入、模型输出，不用的底座模型其对话模板是不一样的，因此我们在使用不用的模型进行训练的时候需要获取其对话模板的标准形式，通过对话模板来构建模型的输入。

![image-20240415193451662](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415193451662.png)

上图为增量预训练的过程，损失的计算计算整个句子的预测损失

![image-20240415193758931](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415193758931.png)

上图为指令微调的损失计算，只计算输出部分的损失。

### 微调方式

- XTuner框架的微调方式LoRA和QLoRA
- 全量微调整个模型会面临显存需求大，数据需求大
- 对LLM的高效微调是重要的，LoRA采用微调部分参数来进行微调，微调结束后将微调的LoRA模型和原模型进行相加得到微调后的模型，LoRA的微调工具可以使用transformer提供PEFT工具。

![image-20240415193912350](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415193912350.png)

- 全参数微调、LoRA、QLoRA比较

![image-20240415194600129](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415194600129.png)

### XTuner

- 一个轻量的封装好的微调工作
- 通过配置文件来进行模型微调

![image-20240415195818957](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415195818957.png)

- 安装XTuner

![image-20240415200045961](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415200045961.png)

- 配置配置文件

![image-20240415200137528](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415200137528.png)

- 对训练好的模型进行对话

![image-20240415200320909](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415200320909.png)

### 数据引擎

- 不同格式的数据集，通过数据引擎将数据集转化为统一的模型数据格式

![image-20240415200821368](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415200821368.png)

![image-20240415200858036](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415200858036.png)

- 这里的数据集映射函数是将原始数据集映射为对应的格式

![image-20240415201551189](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415201551189.png)

![image-20240415202526339](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415202526339.png)

### 优化方法

![image-20240415202715218](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415202715218.png)

![image-20240415202731429](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415202731429.png)

### 多模态大模型

![image-20240415203541409](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415203541409.png)

- LLaVA方案训练多模态大模型

![image-20240415203803018](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415203803018.png)

![image-20240415203933993](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415203933993.png)

![image-20240415204028183](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415204028183.png)

- 预训练阶段的数据集

![image-20240415204654332](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415204654332.png)

- 微调阶段的数据集

![image-20240415204728737](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240415204728737.png)

### XTuner微调实践

- 整个微调过程流程图

![image-20240416140043328](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416140043328.png)

- 根据微调的模型，微调数据，微调方法选择合适的微调脚本

  - 微调数据不同的数据对应不同的数据处理方法，可以参考[dataset_map_fns](https://github.com/InternLM/xtuner/tree/main/xtuner/dataset/map_fns/dataset_map_fns)文件选择合适的数据映射方法，这些方法的最终目的都是将数据转化为XTuner统一的数据处理格式
  - 你可以选择自己的数据，但是要将数据处理为XTuner可接受的数据个数

- 选择好微调模板以后，我们需要进行配置文件修改

  - 根据自己的实际情况修改模型地址和数据集存储地址
  - ![image-20240416141049538](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416141049538.png)
  - 根据数据集选择合适的数据映射函数
  - ![image-20240416141157634](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416141157634.png)

  - 修改数据集加载方式，如果使用自定义的数据集。

  - ```
    + data_files = ['/path/to/json/file.json']
    train_dataset = dict(
        ...,
    -   dataset=dict(type=load_dataset, path='tatsu-lab/alpaca'),
    +   dataset=dict(type=load_dataset, path='json', data_files=data_files),
        ...
    )
    ```

- 修改完成以后进行模型训练
  - XTuner自动开启Flash Attention优化如何使用deepspeed则表示同时开启两种优化方式

#### 环境安装

```
# 如果你是在 InternStudio 平台，则从本地 clone 一个已有 pytorch 的环境：
# pytorch    2.0.1   py3.10_cuda11.7_cudnn8.5.0_0
studio-conda xtuner0.1.17

# 如果你是在其他平台：
# conda create --name xtuner0.1.17 python=3.10 -y

# 激活环境
conda activate xtuner0.1.17
# 进入家目录 （~的意思是 “当前用户的home路径”）
cd ~
# 创建版本文件夹并进入，以跟随本教程
mkdir -p /root/xtuner0117 && cd /root/xtuner0117

# 拉取 0.1.17 的版本源码
git clone -b v0.1.17  https://github.com/InternLM/xtuner
# 无法访问github的用户请从 gitee 拉取:
# git clone -b v0.1.15 https://gitee.com/Internlm/xtuner

# 进入源码目录
cd /root/xtuner0117/xtuner

# 从源码安装 XTuner
pip install -e '.[all]'
```

![image-20240416151018108](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416151018108.png)

#### 数据准备

- 创建文件夹用于数据的存储

```
# 前半部分是创建一个文件夹，后半部分是进入该文件夹。
mkdir -p /root/ft && cd /root/ft

# 在ft这个文件夹里再创建一个存放数据的data文件夹
mkdir -p /root/ft/data && cd /root/ft/data   
```

- 创建脚本文件，编写数据扩充脚本

```
# 创建 `generate_data.py` 文件
touch /root/ft/data/generate_data.py
```

```python
import json

# 设置用户的名字
name = 'TNGPNG'  # 自己的昵称
# 设置需要重复添加的数据次数
n =  10000

# 初始化OpenAI格式的数据结构
data = [
    {
        "messages": [
            {
                "role": "user",
                "content": "请做一下自我介绍"
            },
            {
                "role": "assistant",
                "content": "我是{}的小助手，内在是上海AI实验室书生·浦语的1.8B大模型哦".format(name)
            }
        ]
    }
]

# 通过循环，将初始化的对话数据重复添加到data列表中
for i in range(n):
    data.append(data[0])

# 将data列表中的数据写入到一个名为'personal_assistant.json'的文件中
with open('personal_assistant.json', 'w', encoding='utf-8') as f:
    # 使用json.dump方法将数据以JSON格式写入文件
    # ensure_ascii=False 确保中文字符正常显示
    # indent=4 使得文件内容格式化，便于阅读
    json.dump(data, f, ensure_ascii=False, indent=4)
```

![image-20240416151736455](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416151736455.png)

#### 模型准备

```
# 创建目标文件夹，确保它存在。
# -p选项意味着如果上级目录不存在也会一并创建，且如果目标文件夹已存在则不会报错。
mkdir -p /root/ft/model

# 复制内容到目标文件夹。-r选项表示递归复制整个文件夹。
cp -r /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b/* /root/ft/model/
```

![image-20240416152107135](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416152107135.png)

#### 选择配置文件

```
# 列出所有内置配置文件
# xtuner list-cfg

# 假如我们想找到 internlm2-1.8b 模型里支持的配置文件
xtuner list-cfg -p internlm2_1_8b
```

- 查看支持internlm2 1.8B 微调的脚本

![image-20240416153442157](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416153442157.png)

- 选择脚本并复制到指定目录

```
# 创建一个存放 config 文件的文件夹
mkdir -p /root/ft/config

# 使用 XTuner 中的 copy-cfg 功能将 config 文件复制到指定的位置
xtuner copy-cfg internlm2_1_8b_qlora_alpaca_e3 /root/ft/config
```

- 修改配置文件
  - 配置文件介绍
  - **PART 1 Settings**：涵盖了模型基本设置，如预训练模型的选择、数据集信息和训练过程中的一些基本参数（如批大小、学习率等）。
  - **PART 2 Model & Tokenizer**：指定了用于训练的模型和分词器的具体类型及其配置，包括预训练模型的路径和是否启用特定功能（如可变长度注意力），这是模型训练的核心组成部分。
  - **PART 3 Dataset & Dataloader**：描述了数据处理的细节，包括如何加载数据集、预处理步骤、批处理大小等，确保了模型能够接收到正确格式和质量的数据。
  - **PART 4 Scheduler & Optimizer**：配置了优化过程中的关键参数，如学习率调度策略和优化器的选择，这些是影响模型训练效果和速度的重要因素。
  - **PART 5 Runtime**：定义了训练过程中的额外设置，如日志记录、模型保存策略和自定义钩子等，以支持训练流程的监控、调试和结果的保存。

![image-20240416154211625](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416154211625.png)

#### 模型训练

- 常规训练

```
# 指定保存路径，如果不指定着保存在运行脚本的位置
# 使用word-dir指定保存路径
xtuner train /root/ft/config/internlm2_1_8b_qlora_alpaca_e3_copy.py --work-dir /root/ft/train
```

- 使用deepspeed训练

  - DeepSpeed是一个深度学习优化库，由微软开发，旨在提高大规模模型训练的效率和速度。它通过几种关键技术来优化训练过程，包括模型分割、梯度累积、以及内存和带宽优化等。DeepSpeed特别适用于需要巨大计算资源的大型模型和数据集。

    在DeepSpeed中，`zero` 代表“ZeRO”（Zero Redundancy Optimizer），是一种旨在降低训练大型模型所需内存占用的优化器。ZeRO 通过优化数据并行训练过程中的内存使用，允许更大的模型和更快的训练速度。ZeRO 分为几个不同的级别，主要包括：

    - **deepspeed_zero1**：这是ZeRO的基本版本，它优化了模型参数的存储，使得每个GPU只存储一部分参数，从而减少内存的使用。
    - **deepspeed_zero2**：在deepspeed_zero1的基础上，deepspeed_zero2进一步优化了梯度和优化器状态的存储。它将这些信息也分散到不同的GPU上，进一步降低了单个GPU的内存需求。
    - **deepspeed_zero3**：这是目前最高级的优化等级，它不仅包括了deepspeed_zero1和deepspeed_zero2的优化，还进一步减少了激活函数的内存占用。这通过在需要时重新计算激活（而不是存储它们）来实现，从而实现了对大型模型极其内存效率的训练。

    选择哪种deepspeed类型主要取决于你的具体需求，包括模型的大小、可用的硬件资源（特别是GPU内存）以及训练的效率需求。一般来说：

    - 如果你的模型较小，或者内存资源充足，可能不需要使用最高级别的优化。
    - 如果你正在尝试训练非常大的模型，或者你的硬件资源有限，使用deepspeed_zero2或deepspeed_zero3可能更合适，因为它们可以显著降低内存占用，允许更大模型的训练。
    - 选择时也要考虑到实现的复杂性和运行时的开销，更高级的优化可能需要更复杂的设置，并可能增加一些计算开销。

  - ```
    # 使用 deepspeed 来加速训练
    xtuner train /root/ft/config/internlm2_1_8b_qlora_alpaca_e3_copy.py --work-dir /root/ft/train_deepspeed --deepspeed deepspeed_zero2
    ```

- 模型出现过拟合

  - 即模型丢失了基础的能力，只会成为某一句话的复读机
  - 解决方法
    - **减少保存权重文件的间隔并增加权重文件保存的上限**
      - 原来300批次保存一次，现在改为100批次保存一次
      - 同时还要修改模型文件保存的上限即修改`save_total_limit`的值
    - **增加常规的对话数据集从而稀释原本数据的占比**
      - 这个方法其实就是希望我们正常用对话数据集做指令微调的同时还加上一部分的数据集来让模型既能够学到正常对话，但是在遇到特定问题时进行特殊化处理。
      - 比如说我在一万条正常的对话数据里混入两千条和小助手相关的数据集，这样模型同样可以在不丢失对话能力的前提下学到剑锋大佬的小助手这句话。

- 模型续训

  - 另外假如我们模型中途中断了，我们也可以参考以下方法实现模型续训工作

  - ```
    # 模型续训
    xtuner train /root/ft/config/internlm2_1_8b_qlora_alpaca_e3_copy.py --work-dir /root/ft/train --resume /root/ft/train/iter_600.pth
    ```

  - 在原来的基础上继续后面批次的训练，不会从头开始

- 训练结果

- 第300个批次的表现

- ![image-20240416220415910](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416220415910.png)

- 第600个批次的表现
- ![image-20240416220440132](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416220440132.png)

- 在回答`你是我的小助手吗`在第600轮的时候已经过拟合了。

### 模型转换

- 模型转换的本质其实就是将原本使用 Pytorch 训练出来的模型权重文件转换为目前通用的 Huggingface 格式文件

- ```python
  # 创建一个保存转换后 Huggingface 格式的文件夹
  mkdir -p /root/ft/huggingface
  
  # 模型转换
  # xtuner convert pth_to_hf ${配置文件地址} ${权重文件地址} ${转换后模型保存地址}
  xtuner convert pth_to_hf /root/ft/train/internlm2_1_8b_qlora_alpaca_e3_copy.py /root/ft/train/iter_768.pth /root/ft/huggingface
  ```

- 额外的转换参数选择，转换后得到的模型文件是训练的LoRA模型文件，在后续的使用中我们需要与原始的文件进行配合使用

- | 参数名                | 解释                                         |
  | --------------------- | -------------------------------------------- |
  | --fp32                | 代表以fp32的精度开启，假如不输入则默认为fp16 |
  | --max-shard-size {GB} | 代表每个权重文件最大的大小（默认为2GB）      |

- 转换后的效果
- ![image-20240416205130794](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416205130794.png)

### 模型合并

- 将转化后的LoRA权重与原始的模型进行合并，使得原始权重中包含微调后的信息

- ```
  # 创建一个名为 final_model 的文件夹存储整合后的模型文件
  mkdir -p /root/ft/final_model
  
  # 解决一下线程冲突的 Bug 
  export MKL_SERVICE_FORCE_INTEL=1
  
  # 进行模型整合
  # xtuner convert merge  ${NAME_OR_PATH_TO_LLM} ${NAME_OR_PATH_TO_ADAPTER} ${SAVE_PATH} 
  xtuner convert merge /root/ft/model /root/ft/huggingface /root/ft/final_model
  ```

- 额外的参数选择

- | 参数名                 | 解释                                                         |
  | ---------------------- | ------------------------------------------------------------ |
  | --max-shard-size {GB}  | 代表每个权重文件最大的大小（默认为2GB）                      |
  | --device {device_name} | 这里指的就是device的名称，可选择的有cuda、cpu和auto，默认为cuda即使用gpu进行运算 |
  | --is-clip              | 这个参数主要用于确定模型是不是CLIP模型，假如是的话就要加上，不是就不需要添加 |

![image-20240416210001160](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416210001160.png)

### 对话测试

- 以后合并后的模型进行对话

- ```
  # 与模型进行对话
  xtuner chat /root/ft/final_model --prompt-template internlm2_chat
  ```

- 不同的模型prompt-template是不一样的，不管是在微调还是推理的时候都应该注意，不同模型的[推理模板](https://github.com/InternLM/xtuner/blob/main/xtuner/utils/templates.py)
- ![image-20240416220731286](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416220731286.png)
- 过拟合了，失去了对话能力
- 微调前的表现
- ![image-20240416221848042](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240416221848042.png)

- 除了在对话的时候使用合并后的模型，也可以通过原模型外挂适配器的方式

- ```
  # 使用 --adapter 参数与完整的模型进行对话
  xtuner chat /root/ft/model --adapter /root/ft/huggingface --prompt-template internlm2_chat
  ```

- 问题：
- ![image-20240417191444003](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417191444003.png)

- 解决方案

- ```
  export MKL_SERVICE_FORCE_INTEL=1
  ```

![image-20240417192211380](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417192211380.png)

### Web demo

- 安装依赖

- ```
  pip install streamlit==1.24.0
  ```

- clone 项目

- ```
  # 创建存放 InternLM 文件的代码
  mkdir -p /root/ft/web_demo && cd /root/ft/web_demo
  
  # 拉取 InternLM 源文件
  git clone https://github.com/InternLM/InternLM.git
  
  # 进入该库中
  cd /root/ft/web_demo/InternLM
  ```

- 修改地址

- ![image-20240417192725833](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417192725833.png)

- 启动服务

- ```
  # 开启6006端口
  ssh -CNg -L 6006:127.0.0.1:6006 root@ssh.intern-ai.org.cn -p 39036
  
  streamlit run /root/ft/web_demo/InternLM/chat/web_demo.py --server.address 127.0.0.1 --server.port 6006
  ```

  

- 部署展示
- ![image-20240417193523765](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417193523765.png)
