## Llava多模态微调以及OpenXLab

### Llava多模态微调

- Llava模型

![image-20240417210825734](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417210825734.png)

- 多模态LLM原理简介

![image-20240417204557034](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417204557034.png)

单模态的网络结构，输入的文本被转化为文本向量以后输入到LLM中生成回复

![image-20240417204734343](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417204734343.png)

多模态的网络结构讲文本数据和图像数据转化为为向量后拼接在一起输入到LLM中生成回复

- Llava训练

  - 训练阶段
  - ![image-20240417205937768](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417205937768.png)

  - 训练的数据是`<question text><image> -- <answer text>`,利用这些数据对，配合文本单模态LLM，训练出一个Image Projector，讲图像的语义信息映射到语言的语义信息空间
  - 测试阶段
  - ![image-20240417210213882](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417210213882.png)

  - 
  - Image Projector的训练和测试，有点类似之前我们讲过的LoRA微调方案。二者都是在已有LLM的基础上，用新的数据训练一个新的小文件。只不过，LLM套上LoRA之后，有了新的灵魂（角色）；而LLM套上Image Projector之后，才有了眼睛。
  - 完整的训练结构包括了预训练+微调
  - ![image-20240417210503343](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417210503343.png)

  - 在Pretrain阶段，我们会使用大量的`图片+简单文本（caption, 即图片标题）`数据对，使LLM理解图像中的**普遍特征**。即，对大量的图片进行**粗看**。Pretrain阶段训练完成后，此时的模型已经有视觉能力了！但是由于训练数据中都是图片+图片标题，所以此时的模型虽然有视觉能力，但无论用户问它什么，它都只会回答输入图片的标题。即，**此时的模型只会给输入图像“写标题”**。

  - 微调阶段需要构造高质量的图像对话数据

### 数据准备

- 我们可以效法LLaVA作者的做法，将自己的图片发送给GPT，要求其按照上述格式生成若干条问答对

```
cd ~ && git clone https://github.com/InternLM/tutorial -b camp2 && conda activate xtuner0.1.17 && cd tutorial

python /root/tutorial/xtuner/llava/llava_data/repeat.py \
  -i /root/tutorial/xtuner/llava/llava_data/unique_data.json \
  -o /root/tutorial/xtuner/llava/llava_data/repeated_data.json \
  -n 200
```

- 数据生成

![image-20240417211806011](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417211806011.png)

- 查看关于Llava的配置文件

- ```
  xtuner list-cfg -p llava_internlm2_chat_1_8b
  ```

![image-20240417211941872](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417211941872.png)

- 拷贝选择的配置文件

- ```
  xtuner copy-cfg \
    llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune \
    /root/tutorial/xtuner/llava
  ```

- 修改配置文件
  - pretrained_pth：预训练的图像映射模型的路径
  - llm_name_or_path：语言模型的路径
  - visual_encoder_name_or_path ： 视觉模型的路径
  - data_root ： 数据文件夹的路径
  - data_path ： 完整的数据路径
  - image_folder ： 图像路径
- ![image-20240417213145519](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417213145519.png)

### 开始训练

- ```
  cd /root/tutorial/xtuner/llava/
  xtuner train /root/tutorial/xtuner/llava/llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune_copy.py --deepspeed deepspeed_zero2
  ```

![image-20240417214325767](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240417214325767.png)

### 训练结果

- finetune前

```
# 解决小bug
export MKL_SERVICE_FORCE_INTEL=1
export MKL_THREADING_LAYER=GNU

# pth转huggingface
xtuner convert pth_to_hf \
  llava_internlm2_chat_1_8b_clip_vit_large_p14_336_e1_gpu8_pretrain \   # 转化脚本
  /root/share/new_models/xtuner/iter_2181.pth \                         # 原格式pth
  /root/tutorial/xtuner/llava/llava_data/iter_2181_hf                   # 转化后的hf格式

# 启动！
xtuner chat /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \    # 原模型
  --visual-encoder /root/share/new_models/openai/clip-vit-large-patch14-336 \      # 视觉编码器
  --llava /root/tutorial/xtuner/llava/llava_data/iter_2181_hf \                    # 转化后的视觉映射器地址
  --prompt-template internlm2_chat \                                               # 聊天模板
  --image /root/tutorial/xtuner/llava/llava_data/test_img/oph.jpg                  # 测试图片
```

![image-20240418211928141](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418211928141.png)

- finetune后

```
# 解决小bug
export MKL_SERVICE_FORCE_INTEL=1
export MKL_THREADING_LAYER=GNU

# pth转huggingface
xtuner convert pth_to_hf \
  /root/tutorial/xtuner/llava/llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune_copy.py \
  /root/tutorial/xtuner/llava/work_dirs/llava_internlm2_chat_1_8b_qlora_clip_vit_large_p14_336_lora_e1_gpu8_finetune_copy/iter_1200.pth \
  /root/tutorial/xtuner/llava/llava_data/iter_1200_hf

# 启动！
xtuner chat /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-1_8b \
  --visual-encoder /root/share/new_models/openai/clip-vit-large-patch14-336 \
  --llava /root/tutorial/xtuner/llava/llava_data/iter_1200_hf \
  --prompt-template internlm2_chat \
  --image /root/tutorial/xtuner/llava/llava_data/test_img/oph.jpg
```

![image-20240418212137036](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418212137036.png)

![image-20240418213416709](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240418213416709.png)

- 微调之后，Llava可以对图片进行描述而不是简单的图片标注

### OpenXLab

- 部署过程同[LMDeploy](https://github.com/santiagoTOP/CodeFKidsLLM/blob/master/Tutorial/Lesson5/LMDeploy.md)
