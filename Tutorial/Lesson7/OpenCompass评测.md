## OpenCompass评测

[视频资料](https://www.bilibili.com/video/BV1Pm41127jU/?vd_source=1e18dfc082e362f5bc2e775c28dc3dae) [文档资料](https://github.com/InternLM/Tutorial/blob/camp2/opencompass/readme.md) [文档资料](https://github.com/InternLM/Tutorial/blob/main/opencompass/opencompass_tutorial.md)

- 为什么要对LLM进行评测

![image-20240420201716683](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420201716683.png)

- 大模型评测的难点
  - 数据集污染：在测试集上进行了训练又在测试集上进行评测

![image-20240420201908218](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420201908218.png)

- 不同类型的模型如何评测

![image-20240420202550002](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420202550002.png)

- 如何进行评测
  - 客观评测：为了更好地激发出模型在题目测试领域的能力，并引导模型按照一定的模板输出答案，OpenCompass采用提示词工程 （prompt engineering）和语境学习（in-context learning）进行客观评测
    - 判别式评测：该评测方式基于将问题与候选答案组合在一起，计算模型在所有组合上的困惑度（perplexity），并选择困惑度最小的答案作为模型的最终输出。例如，若模型在 问题? 答案1 上的困惑度为 0.1，在 问题? 答案2 上的困惑度为 0.2，最终我们会选择 答案1 作为模型的输出。
    - 生成式评测：该评测方式主要用于生成类任务，如语言翻译、程序生成、逻辑分析题等。具体实践时，使用问题作为模型的原始输入，并留白答案区域待模型进行后续补全。我们通常还需要对其输出进行后处理，以保证输出满足数据集的要求。
  - 主观评测
    -  OpenCompass采取的主观评测方案是指借助受试者的主观判断对具有对话能力的大语言模型进行能力评测。在具体实践中，我们提前基于模型的能力维度构建主观测试问题集合，并将不同模型对于同一问题的不同回复展现给受试者，收集受试者基于主观感受的评分
    - 在实际评测中，本文将采用真实人类专家的主观评测与基于模型打分的主观评测相结合的方式开展模型能力评估。 在具体开展主观评测时，OpenComapss采用单模型回复满意度统计和多模型满意度比较两种方式开展具体的评测工作

![image-20240420202730557](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420202730557.png)

- 提示词工程，通过对提示进行改写而不是泛泛而谈让题目变得更好，可以让模型给出的答案更加的真实

![image-20240420202835997](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420202835997.png)

![image-20240420203349079](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420203349079.png)

- 长文本评测，通过大海捞针进行评测

![image-20240420203507667](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420203507667.png)

- 评测工具集

![image-20240420203730712](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420203730712.png)

- 高效利用资源进行并行评测

![image-20240420203911670](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420203911670.png)

- 不同类型的评测工具

![image-20240420204027680](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420204027680.png)

- 丰富的评测维度

![image-20240420204229816](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420204229816.png)

- 自研评测数据集

![image-20240420204300887](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420204300887.png)

 ### 模型评测流程

![image-20240420205736724](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420205736724.png)

- 配置：这是整个工作流的起点。您需要配置整个评估过程，选择要评估的模型和数据集。此外，还可以选择评估策略、计算后端等，并定义显示结果的方式。
- 推理与评估：在这个阶段，OpenCompass 将会开始对模型和数据集进行并行推理和评估。推理阶段主要是让模型从数据集产生输出，而评估阶段则是衡量这些输出与标准答案的匹配程度。这两个过程会被拆分为多个同时运行的“任务”以提高效率，但请注意，如果计算资源有限，这种策略可能会使评测变得更慢。如果需要了解该问题及解决方案，可以参考 FAQ: 效率。
- 可视化：评估完成后，OpenCompass 将结果整理成易读的表格，并将其保存为 CSV 和 TXT 文件。你也可以激活飞书状态上报功能，此后可以在飞书客户端中及时获得评测状态报告。 

### 实验

- 书生浦语在 `C-Eval` 基准任务上的评估
- 如何启动opencompass的评测
- 讲解opencompass整个代码评测的运行逻辑
- 如何去自建一个自定义的评测
- opencompass的其他功能

#### 环境搭建

- 环境安装

- ```python
  studio-conda -o internlm-base -t opencompass
  source activate opencompass
  git clone -b 0.2.4 https://github.com/open-compass/opencompass
  cd opencompass
  pip install -e .
  
  # pip install -e . 安装错误可以采用下面的安装方式
  pip install -r requirements.txt
  ```

- 准备测评数据集

- ```
  cp /share/temp/datasets/OpenCompassData-core-20231110.zip /root/opencompass/
  unzip OpenCompassData-core-20231110.zip
  ```

- 查看配置文件

- ```
  列出所有跟 internlm 及 ceval 相关的配置
  python tools/list_configs.py internlm ceval
  ```

![image-20240420214708290](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420214708290.png)

#### 启动测评

- 方法一：使用全命令的方式

- ```
  python run.py
  --datasets ceval_gen \  # 评测数据集
  --hf-path /share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \  # HuggingFace 模型路径
  --tokenizer-path /share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \  # HuggingFace tokenizer 路径（如果与模型路径相同，可以省略）
  --tokenizer-kwargs padding_side='left' truncation='left' trust_remote_code=True \  # 构建 tokenizer 的参数
  --model-kwargs device_map='auto' trust_remote_code=True \  # 构建模型的参数
  --max-seq-len 1024 \  # 模型可以接受的最大序列长度
  --max-out-len 16 \  # 生成的最大 token 数
  --batch-size 2  \  # 批量大小
  --num-gpus 1  # 运行模型所需的 GPU 数量
  --work-dir "xxxx" # 运行结果的保存路径，如果不写默认是在outputs/default下面
  --reuse latest  # 断点续训
  --debug  # 在终端输出日志
  ```

- 方法二：使用bash脚本

- ```
  将在命令行里面执行的过程一步一步的写bash脚本中，包括环境激活
  最后使用bash -i test.sh执行
  ```

- ![image-20240421111209687](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421111209687.png)

- 方法三：使用脚本文件，将所有的参数写到一个py脚本里面

- ```
  python run.py configs/eval_demo.py 
  ```


#### 评测结果

![image-20240421101103262](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421101103262.png)

#### opencompass执行逻辑

- 对数据，模型进行分片处理`opencompass/opencompass/partitioners`
- 分片完成以后变成一个个的任务`opencompass/opencompass/tasks`
- 不同任务的提交方式`opencompass/opencompass/runners`
- 每一个任务的执行`opencompass/opencompass/openicl`
- 执行完成以后如何进行汇总`opencompass/opencompass/summarizers`

#### 自建数据集

- 以ceval为例

- 第一步：配置数据集的config文件`opencompass/configs/datasets`
  - 实现配置文件中的这个部分，这里的typy对应的类名与第二步增加的类名对应
  - 这里的path对应的ceval子集的根目录，name对应的子集名称
  - type、name、path用于初始化一个数据集实例
  - infer_cfg表示的是对这个数据集进行推理时候的配置文件
  - eval_cfg表示采用什么方式对结果进行评估
  - reader_cfg表示数据读取和输出的规范，用于推理时候的使用
  - <img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421102048443.png" alt="image-20240421102048443" style="zoom: 80%;" />

- 第二步：增加一个数据集的类在`opencompass/opencompass/datasets`，
  - 要实现load方法，主要实现数据集的加载
  - load中的path对应的是数据集的根目录，name对应的是数据集的名称
  - 因为一个大的数据集下面，可能会有多个子数据集
  - <img src="http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421102527482.png" alt="image-20240421102527482" style="zoom:80%;" />

- 第三步：修改`opencompass/opencompass/datasets`的`__init__`文件，导入新建立的类

#### configs配置文件

- opencompass/configs中包含了数据集的配置文件和模型配置文件

- 如果需要自定义自己训练的模型，需要在configs/models下面新建一个模型的配置文件

- 自定义模型配置文件

- ```
  # 使用 `HuggingFaceCausalLM` 评估由 HuggingFace 的 `AutoModelForCausalLM` 支持的模型
  from opencompass.models import HuggingFaceCausalLM
  
  # OPT-350M
  opt350m = dict(
         type=HuggingFaceCausalLM,
         # `HuggingFaceCausalLM` 的初始化参数
         path='facebook/opt-350m',
         tokenizer_path='facebook/opt-350m',
         tokenizer_kwargs=dict(
             padding_side='left',
             truncation_side='left',
             proxies=None,
             trust_remote_code=True),
         model_kwargs=dict(device_map='auto'),
         # 下面是所有模型的共同参数，不特定于 HuggingFaceCausalLM
         abbr='opt350m',               # 结果显示的模型缩写
         max_seq_len=2048,             # 整个序列的最大长度
         max_out_len=100,              # 生成的最大 token 数
         batch_size=64,                # 批量大小
         run_cfg=dict(num_gpus=1),     # 该模型所需的 GPU 数量
      )
  ```

- 自定义数据集

- ```
  from mmengine.config import read_base  # 使用 mmengine.read_base() 读取基本配置
  
  with read_base():
      # 直接从预设的数据集配置中读取所需的数据集配置
      from .datasets.winograd.winograd_ppl import winograd_datasets  # 读取 Winograd 配置，基于 PPL（困惑度）进行评估
      from .datasets.siqa.siqa_gen import siqa_datasets  # 读取 SIQA 配置，基于生成进行评估
  
  datasets = [*siqa_datasets, *winograd_datasets]       # 最终的配置需要包含所需的评估数据集列表 'datasets'
  ```

- 评估

- ```
  python run.py --models hf_llama_7b --datasets base_medium
  ```

- 可以将数据集的配置和模型配置放在一起

- ```
  from mmengine.config import read_base
  
  with read_base():
      from .datasets.siqa.siqa_gen import siqa_datasets
      from .datasets.winograd.winograd_ppl import winograd_datasets
      from .models.opt.hf_opt_125m import opt125m
      from .models.opt.hf_opt_350m import opt350m
  
  datasets = [*siqa_datasets, *winograd_datasets]  # 同时推理多个数据集
  models = [opt125m, opt350m]  # 多个模型
  ```

- 评估

- ```
  python run.py configs/eval_demo.py
  ```

#### 主观评测

- 需要待评测的模型和数据集
  - 在配置模型的时候max_leng的最终长度由数据集决定[30:50] [[视频文件](https://www.bilibili.com/video/BV1Gg4y1U7uc/?spm_id_from=333.999.0.0&vd_source=1e18dfc082e362f5bc2e775c28dc3dae)]
- 以及一种裁判模型
- ![image-20240421142440644](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421142440644.png)
- NaivePartitioner：不分片，数据是串行的一批一批的推理
- SizePartitioner：对数据进行分片

- mode = “singlescore” 表示一个模型对另外一个模型的评价采用打分的方式

- ```
  from mmengine.config import read_base
  
  with read_base():
      from .datasets.subjective.alignbench.alignbench_judgeby_critiquellm import subjective_datasets
  
  from opencompass.models import HuggingFaceCausalLM, HuggingFace, HuggingFaceChatGLM3, OpenAI
  from opencompass.models.openai_api import OpenAIAllesAPIN
  from opencompass.partitioners import NaivePartitioner, SizePartitioner
  from opencompass.partitioners.sub_naive import SubjectiveNaivePartitioner
  from opencompass.partitioners.sub_size import SubjectiveSizePartitioner
  from opencompass.runners import LocalRunner
  from opencompass.runners import SlurmSequentialRunner
  from opencompass.tasks import OpenICLInferTask
  from opencompass.tasks.subjective_eval import SubjectiveEvalTask
  from opencompass.summarizers import AlignmentBenchSummarizer
  
  api_meta_template = dict(
      round=[
          dict(role='HUMAN', api_role='HUMAN'),
          dict(role='BOT', api_role='BOT', generate=True),
      ]
  )
  
  # -------------Inference Stage ----------------------------------------
  # For subjective evaluation, we often set do sample for models
  models = [
      dict(
          type=HuggingFaceChatGLM3,
          abbr='chatglm3-6b-hf',
          path='THUDM/chatglm3-6b',
          tokenizer_path='THUDM/chatglm3-6b',
          model_kwargs=dict(
              device_map='auto',
              trust_remote_code=True,
          ),
          tokenizer_kwargs=dict(
              padding_side='left',
              truncation_side='left',
              trust_remote_code=True,
          ),
          generation_kwargs=dict(
              do_sample=True,
          ),
          meta_template=api_meta_template,
          max_out_len=2048,
          max_seq_len=4096,
          batch_size=8,
          run_cfg=dict(num_gpus=1, num_procs=1),
      )
  ]
  
  datasets = [*subjective_datasets]
  
  # -------------Evalation Stage ----------------------------------------
  
  ## ------------- JudgeLLM Configuration
  judge_models = [dict(
      abbr='GPT4-Turbo',
      type=OpenAI,
      path='gpt-4-1106-preview',
      key='xxxx',  # The key will be obtained from $OPENAI_API_KEY, but you can write down your key here as well
      meta_template=api_meta_template,
      query_per_second=16,
      max_out_len=2048,
      max_seq_len=2048,
      batch_size=8,
      temperature=0,
  )]
  
  ## ------------- Evaluation Configuration
  eval = dict(
      partitioner=dict(
          type=SubjectiveSizePartitioner, 
          max_task_size=1000,
          mode='singlescore', 
          models=models, 
          judge_models=judge_models,
      ),
      runner=dict(type=LocalRunner, 
                  max_num_workers=2, 
                  task=dict(type=SubjectiveEvalTask)),
  )
  
  summarizer = dict(type=AlignmentBenchSummarizer, judge_type='general')
  
  work_dir = 'outputs/alignment_bench/'
  ```

- 主观评测可以设置额外的一些参数generation_kwargs，确保模型回复的多样性
- ![image-20240421143439657](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240421143439657.png)