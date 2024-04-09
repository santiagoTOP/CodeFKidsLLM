## 茴香豆RAG

[项目文档](https://github.com/InternLM/Tutorial/blob/camp2/huixiangdou/readme.md)  [项目视频](https://www.bilibili.com/video/BV1QA4m1F7t4/?spm_id_from=333.788&vd_source=1e18dfc082e362f5bc2e775c28dc3dae)

### RAG基础

- RAG概念

![image-20240408082343825](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408082343825.png)

- 工作原理

![image-20240408082516925](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408082516925.png)

- 向量数据库

![image-20240408082654762](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408082654762.png)

- 工作流程

![image-20240408082830094](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408082830094.png)

- RAG发展

![image-20240408083158397](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408083158397.png)

- RAG优化方法
  - 提高向量数据库的质量来提升RAG性能
    - 嵌入优化：优化嵌入的性能（优化将文本转化为向量的能力）
    - 索引优化：采用混合索引（多级索引）、文本粒度分割、添加元数据（未被向量化的数据）
  - 查询优化（前检索）
    - 对用户的提问进行扩展或转换，提高查询的多样性
  - 查询优化（后检索）
    - 对检索回来的信息进行重排
    - 对信息进行压缩去除冗余信息（可以训练一个小模型）
  - 检索优化
    - 迭代检索
    - 递归检索
    - 自适应检索
  - 对LLM进行优化
    - 大模型微调

![image-20240408083434506](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408083434506.png)

- RAG Vs 微调

![image-20240408085740734](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408085740734.png)

- LLM优化方法比较

![image-20240408090119716](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408090119716.png)

- RAG评估

![image-20240408090216595](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408090216595.png)

- 总结

![image-20240408091035088](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408091035088.png)

### 茴香豆项目

- 项目介绍

![image-20240408101155984](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408101155984.png)

- 特点

![image-20240408101421150](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408101421150.png)

- 组成要件
  - 资料库
  - 前端（问答平台）
  - LLM后端（本地大模型+远端大模型API）
  - 茴香豆大脑

![image-20240408101721381](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408101721381.png)

- 工作流程
  - 在拒答工作流中，会将用户的问询与数据库中的示例问题的比较来给出问题相关性的得分
  - 根据得分来判断是否要进入回答环节（进行答案的生成与回复）
  - 通过控制LLM scroing的阈值来确定茴香豆是一个话痨还是一个问答专家

![image-20240408102114460](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408102114460.png)

- 回答工作流程
  - 根据问题对内容进行多来源检索：包括向量数据库、网络搜索、知识图谱
  - 对检索到的内容进行评分，过滤掉低质量的检索内容
  - 回答的方式有调用本地LLM进行回答和远程LLM的API进行回答
  - 对回答的内容进行检查，判断LLM生成的回答是否合规

![image-20240408103122952](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408103122952.png)

### 搭建茴香豆智能助手

- 创建虚拟环境并激活环境

```
# 创建虚拟环境
studio-conda -o internlm-base -t InternLM2_Huixiangdou
# 激活虚拟环境
conda activate InternLM2_Huixiangdou
```

- 获取必要的模型文件（LLM模型，嵌入模型、排序模型）

```
# 创建模型文件夹
cd /root && mkdir models

# 复制BCE模型（嵌入模型和排序模型）
ln -s /root/share/new_models/maidalun1020/bce-embedding-base_v1 /root/models/bce-embedding-base_v1
ln -s /root/share/new_models/maidalun1020/bce-reranker-base_v1 /root/models/bce-reranker-base_v1

# 复制大模型（LLM模型）
ln -s /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b /root/models/internlm2-chat-7b
```

- 安装茴香豆所需要的依赖

```
# 安装 python 依赖
# pip install -r requirements.txt

pip install protobuf==4.25.3 accelerate==0.28.0 aiohttp==3.9.3 auto-gptq==0.7.1 bcembedding==0.1.3 beautifulsoup4==4.8.2 einops==0.7.0 faiss-gpu==1.7.2 langchain==0.1.14 loguru==0.7.2 lxml_html_clean==0.1.0 openai==1.16.1 openpyxl==3.1.2 pandas==2.2.1 pydantic==2.6.4 pymupdf==1.24.1 python-docx==1.1.0 pytoml==0.1.21 readability-lxml==0.8.1 redis==5.0.3 requests==2.31.0 scikit-learn==1.4.1.post1 sentence_transformers==2.2.2 textract==1.6.5 tiktoken==0.6.0 transformers==4.39.3 transformers_stream_generator==0.0.5 unstructured==0.11.2

## 因为 Intern Studio 不支持对系统文件的永久修改，在 Intern Studio 安装部署的同学不建议安装 Word 依赖，后续的操作和作业不会涉及 Word 解析。
## 想要自己尝试解析 Word 文件的同学，uncomment 掉下面这行，安装解析 .doc .docx 必需的依赖
# apt update && apt -y install python-dev python libxml2-dev libxslt1-dev antiword unrtf poppler-utils pstotext tesseract-ocr flac ffmpeg lame libmad0 libsox-fmt-mp3 sox libjpeg-dev swig libpulse-dev
```

- 获取茴香豆官方仓库

```
cd /root
# 下载 repo
git clone https://github.com/internlm/huixiangdou && cd huixiangdou
git checkout 447c6f7e68a1657fce1c4f7c740ea1700bde0440
```

#### 修改配置文件

- 需要根据自己的三个模型的真实路径来修改我们的配置文件`huixiangdou/config.ini`

![image-20240408133556777](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408133556777.png)

#### 构建知识库

- 利用 **InternLM** 的 **Huixiangdou** 文档作为新增知识数据检索来源，在不重新训练的情况下，打造一个 **Huixiangdou** 技术问答助手

- 获取Huixiangdou文档预料
  - depth=1表示只clone最新的提交版本

```
cd /root/huixiangdou && mkdir repodir  # 创建文件夹
git clone https://github.com/internlm/huixiangdou --depth=1 repodir/huixiangdou  # clone文档
```

- 整个的茴香豆语料中包含了三种语料
  - 知识库语料
  - 接受语料库（接受问题列表，希望茴香豆助手回答的示例问题）
    - 存储在 `huixiangdou/resource/good_questions.json` 中
  - 拒答语料库（拒绝问题列表，希望茴香豆助手拒答的示例问题）
    - 存储在 `huixiangdou/resource/bad_questions.json` 中

- 扩展接受问答的语料库

```
cd /root/huixiangdou
mv resource/good_questions.json resource/good_questions_bk.json  # 对原来的进行备份
echo '[
	"问题1"，
	"问题2"，
	......
]' > /root/huixiangdou/resource/good_questions.json # 保存扩展后的接受问答的语料库
```

- 创建一个测试用的问询列表

```
cd /root/huixiangdou

echo '[
"huixiangdou 是什么？",
"你好，介绍下自己"
]' > ./test_queries.json
```

#### 创建向量数据库

```
# 创建向量数据库存储目录
cd /root/huixiangdou && mkdir workdir 

# 分别向量化知识语料、接受问题和拒绝问题中后保存到 workdir
python3 -m huixiangdou.service.feature_store --sample ./test_queries.json
```

检索过程中，茴香豆会将输入问题与两个列表中的问题在向量空间进行相似性比较，判断该问题是否应该回答，避免群聊过程中的问答泛滥。确定回答的问题会利用基础模型提取关键词，在知识库中检索 `top K` 相似的 `chunk`，综合问题和检索到的 `chunk` 生成答案

#### 问题

- 在进行语料向量化的过程中出现了如下错误

![image-20240408150132940](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408150132940.png)

- 解决方案修改参数

![image-20240408150221115](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408150221115.png)

#### 运行茴香豆助手

```
# 填入问题
sed -i '74s/.*/    queries = ["huixiangdou 是什么？", "茴香豆怎么部署到微信群", "今天天气怎么样？"]/' /root/huixiangdou/huixiangdou/main.py

# 运行茴香豆
cd /root/huixiangdou/
python3 -m huixiangdou.main --standalone
```

- --standalone 表示调用混合LLM服务

- 修改后的问题列表

![image-20240408150455730](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408150455730.png)

- 问题1回复

![image-20240408152755784](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408152755784.png)

- 问题2回复

![image-20240408155942570](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408155942570.png)

答案生成

![image-20240408160906102](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408160906102.png)

- 问题3回复

![image-20240408152921518](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240408152921518.png)

拒绝回答.

### 茴香豆Web版

[项目地址](https://openxlab.org.cn/apps/detail/tpoisonooo/huixiangdou-web)

- 第一步：创建自己知识库的账户和密码

![image-20240409144309461](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409144309461.png)

- 第二步：上传文档

![image-20240409144538846](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409144538846.png)

- 聊天测试

![image-20240409144713512](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409144713512.png)

- 拒答测试

![image-20240409145020583](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409145020583.png)

![image-20240409152227417](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240409152227417.png)

- 注意
  - 正反样例的设置需要结合知识库内容进行设置