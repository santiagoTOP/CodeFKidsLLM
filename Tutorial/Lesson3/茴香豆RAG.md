## 茴香豆RAG

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