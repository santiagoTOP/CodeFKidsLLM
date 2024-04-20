## Lagent & AgentLego

[文档资料](https://github.com/InternLM/Tutorial/blob/camp2/agent/README.md)  [视频资料](https://www.bilibili.com/video/BV1Xt4217728/?vd_source=1e18dfc082e362f5bc2e775c28dc3dae)

### 为什么需要智能体

- 大模型目前存在幻觉、数据时效性、可靠性等多方面的限制

![image-20240420094944395](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420094944395.png)

### 什么是智能体

![image-20240420095128764](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420095128764.png)

![image-20240420095219875](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420095219875.png)

### 智能体范式

![image-20240420095332552](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420095332552.png)

- AutoGPT

![image-20240420100043414](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100043414.png)

- ReWoo

![image-20240420100110238](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100110238.png)

- ReAct

![image-20240420100145367](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100145367.png)

### Legent

- Lagent 是一个轻量级开源智能体框架，旨在让用户可以高效地构建基于大语言模型的智能体。同时它也提供了一些典型工具以增强大语言模型的能力。Lagent 目前已经支持了包括 AutoGPT、ReAct 等在内的多个经典智能体范式，各种工具

![image-20240420100348139](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100348139.png)

### AgentLego

- AgentLego 是一个提供了多种开源工具 API 的多模态工具包，旨在像是乐高积木一样，让用户可以快速简便地拓展自定义工具，从而组装出自己的智能体。通过 AgentLego 算法库，不仅可以直接使用多种工具，也可以利用这些工具，在相关智能体框架（如 Lagent，Transformers Agent 等）的帮助下，快速构建可以增强大语言模型能力的智能体。

![image-20240420100519400](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100519400.png)

- Legent与AgentLego的关系：Lagent 是一个智能体框架，而 AgentLego 与大模型智能体并不直接相关，而是作为工具包，在相关智能体的功能支持模块发挥作用。

![image-20240420100942845](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420100942845.png)

## 环境安装配置

- 创建文件夹agent用于存储与agent相关的文件

  - ```
    mkdir -p /root/agent
    ```

- 配置开发环境

  - ```
    # 教学开发机上
    studio-conda -t agent -o pytorch-2.1.2
    
    # 自己的本地环境
    conda create -n agent
    conda activate agent
    conda install python=3.10
    conda install pytorch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 pytorch-cuda=11.8 -c pytorch -c nvidia
    ```

- 安装 Lagent 和 AgentLego

  - Lagent 和 AgentLego 都提供了两种安装方法，一种是通过 pip 直接进行安装，另一种则是从源码进行安装。为了方便使用 Lagent 的 Web Demo 以及 AgentLego 的 WebUI，我们选择直接从源码进行安装。 此处附上源码安装的相关帮助文档：

    - Lagent：https://lagent.readthedocs.io/zh-cn/latest/get_started/install.html
    - AgentLego：https://agentlego.readthedocs.io/zh-cn/latest/get_started.html

    可以执行如下命令进行安装：

    ```
    cd /root/agent
    conda activate agent
    git clone https://gitee.com/internlm/lagent.git
    cd lagent && git checkout 581d9fb && pip install -e . && cd ..
    git clone https://gitee.com/internlm/agentlego.git
    cd agentlego && git checkout 7769e0d && pip install -e . && cd ..
    ```


- 安装其他依赖

  - ```
    conda activate agent
    pip install lmdeploy==0.3.0
    ```

- 准备教学文件

  - ```
    cd /root/agent
    git clone -b camp2 https://gitee.com/internlm/Tutorial.git
    ```

- 环境配置结果

![image-20240420104826454](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420104826454.png)

- ![image-20240420105418775](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420105418775.png)

## Lagent

### 实验一：Legent使用

- Legent Web Demo

  - 通过lmdeploy启动api服务，方便通过api接口访问LLM

  - ```
    conda activate agent
    lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                                --server-name 127.0.0.1 \
                                --model-name internlm2-chat-7b \
                                --cache-max-entry-count 0.1
    ```

  - ![image-20240420110155525](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420110155525.png)

  - 启动并使用Lagent Web Demo

  - ```
    conda activate agent
    cd /root/agent/lagent/examples
    streamlit run internlm2_agent_web_demo.py --server.address 127.0.0.1 --server.port 7860
    ```

  - ![image-20240420110450748](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420110450748.png)

  - 打开api服务端口23333和web demo服务端口

  - ```
    ssh -CNg -L 7860:127.0.0.1:7860 -L 23333:127.0.0.1:23333 root@ssh.intern-ai.org.cn -p 44437
    ```

  - 访问web demo界面
  - ![image-20240420110941649](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420110941649.png)

  - 注意这里的模型ip服务一定要设置为127.0.0.1：23333，否则会报错，如下
  - ![image-20240420112712330](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420112712330.png)

  - 正常运行结果
  - ![image-20240420112837824](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420112837824.png)

### 实验二：自定义工具

- 工具定义步骤
  - 继承 BaseAction 类
  - 实现简单工具的 run 方法；或者实现工具包内每个子工具的功能
  - 简单工具的 run 方法可选被 tool_api 装饰；工具包内每个子工具的功能都需要被 tool_api 装饰

- 创建自定义工具脚本

  - ```
    touch /root/agent/lagent/lagent/actions/weather.py
    ```

- 编写工具脚本

  - ```
    import json
    import os
    import requests
    from typing import Optional, Type
    
    from lagent.actions.base_action import BaseAction, tool_api
    from lagent.actions.parser import BaseParser, JsonParser
    from lagent.schema import ActionReturn, ActionStatusCode
    
    class WeatherQuery(BaseAction):
        """Weather plugin for querying weather information."""
        
        def __init__(self,
                     key: Optional[str] = None,
                     description: Optional[dict] = None,
                     parser: Type[BaseParser] = JsonParser,
                     enable: bool = True) -> None:
            super().__init__(description, parser, enable)
            key = os.environ.get('WEATHER_API_KEY', key)
            if key is None:
                raise ValueError(
                    'Please set Weather API key either in the environment '
                    'as WEATHER_API_KEY or pass it as `key`')
            self.key = key
            self.location_query_url = 'https://geoapi.qweather.com/v2/city/lookup'
            self.weather_query_url = 'https://devapi.qweather.com/v7/weather/now'
    
        @tool_api
        def run(self, query: str) -> ActionReturn:
            """一个天气查询API。可以根据城市名查询天气信息。
            
            Args:
                query (:class:`str`): The city name to query.
            """
            tool_return = ActionReturn(type=self.name)
            status_code, response = self._search(query)
            if status_code == -1:
                tool_return.errmsg = response
                tool_return.state = ActionStatusCode.HTTP_ERROR
            elif status_code == 200:
                parsed_res = self._parse_results(response)
                tool_return.result = [dict(type='text', content=str(parsed_res))]
                tool_return.state = ActionStatusCode.SUCCESS
            else:
                tool_return.errmsg = str(status_code)
                tool_return.state = ActionStatusCode.API_ERROR
            return tool_return
        
        def _parse_results(self, results: dict) -> str:
            """Parse the weather results from QWeather API.
            
            Args:
                results (dict): The weather content from QWeather API
                    in json format.
            
            Returns:
                str: The parsed weather results.
            """
            now = results['now']
            data = [
                f'数据观测时间: {now["obsTime"]}',
                f'温度: {now["temp"]}°C',
                f'体感温度: {now["feelsLike"]}°C',
                f'天气: {now["text"]}',
                f'风向: {now["windDir"]}，角度为 {now["wind360"]}°',
                f'风力等级: {now["windScale"]}，风速为 {now["windSpeed"]} km/h',
                f'相对湿度: {now["humidity"]}',
                f'当前小时累计降水量: {now["precip"]} mm',
                f'大气压强: {now["pressure"]} 百帕',
                f'能见度: {now["vis"]} km',
            ]
            return '\n'.join(data)
    
        def _search(self, query: str):
            # get city_code
            try:
                city_code_response = requests.get(
                    self.location_query_url,
                    params={'key': self.key, 'location': query}
                )
            except Exception as e:
                return -1, str(e)
            if city_code_response.status_code != 200:
                return city_code_response.status_code, city_code_response.json()
            city_code_response = city_code_response.json()
            if len(city_code_response['location']) == 0:
                return -1, '未查询到城市'
            city_code = city_code_response['location'][0]['id']
            # get weather
            try:
                weather_response = requests.get(
                    self.weather_query_url,
                    params={'key': self.key, 'location': city_code}
                )
            except Exception as e:
                return -1, str(e)
            return weather_response.status_code, weather_response.json()
    ```

- 获取天气API
  - ![image-20240420150456459](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420150456459.png)

- 使用lmdeploy启动api服务

  - ```
    conda activate agent
    lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                                --server-name 127.0.0.1 \
                                --model-name internlm2-chat-7b \
                                --cache-max-entry-count 0.1
    ```

- web demo脚本

  - ```
    export WEATHER_API_KEY=在2.2节获取的API KEY
    # 比如 export WEATHER_API_KEY=1234567890abcdef
    conda activate agent
    cd /root/agent/Tutorial/agent
    streamlit run internlm2_weather_web_demo.py --server.address 127.0.0.1 --server.port 7860
    ```

- 打开端口

  - ```
    ssh -CNg -L 7860:127.0.0.1:7860 -L 23333:127.0.0.1:23333 root@ssh.intern-ai.org.cn -p 44437
    ```

- 确保LLM服务地址正确

![image-20240420151257850](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420151257850.png)

- 天气查询

![image-20240420151346859](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420151346859.png)

## AgentLego

### 实验三：直接使用AgentLego

- 以图像检查为例

  - 下载图像检测所需的图片

  - ```
    cd /root/agent
    wget http://download.openmmlab.com/agentlego/road.jpg
    ```

  - 安装凸显检测所需的环境依赖

  - ```
    conda activate agent
    pip install openmim==0.3.9
    mim install mmdet==3.3.0
    ```

  - 编写图像检查脚本

  - ```
    touch /root/agent/direct_use.py  
    ```

  - ```
     import re
    
    import cv2
    from agentlego.apis import load_tool
    
    # load tool
    tool = load_tool('ObjectDetection', device='cuda')
    
    # apply tool
    visualization = tool('/root/agent/road.jpg')
    print(visualization)
    
    # visualize
    image = cv2.imread('/root/agent/road.jpg')
    
    preds = visualization.split('\n')
    pattern = r'(\w+) \((\d+), (\d+), (\d+), (\d+)\), score (\d+)'
    
    for pred in preds:
        name, x1, y1, x2, y2, score = re.match(pattern, pred).groups()
        x1, y1, x2, y2, score = int(x1), int(y1), int(x2), int(y2), int(score)
        cv2.rectangle(image, (x1, y1), (x2, y2), (0, 255, 0), 1)
        cv2.putText(image, f'{name} {score}', (x1, y1), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 0), 1)
    
    cv2.imwrite('/root/agent/road_detection_direct.jpg', image)
    ```

  - 执行脚本

  - ```
    python /root/agent/direct_use.py
    ```

  - 运行结果
  - ![image-20240420153114273](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420153114273.png)

  - 这里的用法是将AgentLego作为一个本地的图像处理库使用

### 实验四：作为LLM的工具库使用

- 由于 AgentLego 算法库默认使用 InternLM2-Chat-20B 模型，因此我们首先需要修改LLM基座模型为7B大小
- ![image-20240420153527478](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420153527478.png)

- 由于 AgentLego 的 WebUI 需要用到 LMDeploy 所启动的 api_server

  - ```
    conda activate agent
    lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                                --server-name 127.0.0.1 \
                                --model-name internlm2-chat-7b \
                                --cache-max-entry-count 0.1
    ```

  - 启动web ui

  - ```
    conda activate agent
    cd /root/agent/agentlego/webui
    python one_click.py
    ```

  - 打开端口

  - ```
    ssh -CNg -L 7860:127.0.0.1:7860 -L 23333:127.0.0.1:23333 root@ssh.intern-ai.org.cn -p 44437
    ```

- 配置Agent
- ![image-20240420161133421](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420161133421.png)
- 配置工具
- ![image-20240420154358115](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420154358115.png)

- 工具选择
- ![image-20240420154543649](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420154543649.png)

- 使用工具进行对话
- ![image-20240420154741200](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420154741200.png)

- 这里是将AgentLego工具接入到Agent中使得LLM可以调用图像处理的工具

### 实验五：AgentLego自定义工具

- 使用AgentLego构建自定义工具
  - 继承 BaseTool 类
  - 修改 default_desc 属性（工具功能描述）
  - 如有需要，重载 setup 方法（重型模块延迟加载）
  - 重载 apply 方法（工具功能实现）
  - 其中第一二四步是必须的步骤
- 实现一个调用 MagicMaker 的 API 以实现图像生成的工具

- 创建新的工具调用文件

- ```
  touch /root/agent/agentlego/agentlego/tools/magicmaker_image_generation.py
  ```

- 编写脚本

- ```
  import json
  import requests
  
  import numpy as np
  
  from agentlego.types import Annotated, ImageIO, Info
  from agentlego.utils import require
  from .base import BaseTool
  
  
  class MagicMakerImageGeneration(BaseTool):
  
      default_desc = ('This tool can call the api of magicmaker to '
                      'generate an image according to the given keywords.')
  
      styles_option = [
          'dongman',  # 动漫
          'guofeng',  # 国风
          'xieshi',   # 写实
          'youhua',   # 油画
          'manghe',   # 盲盒
      ]
      aspect_ratio_options = [
          '16:9', '4:3', '3:2', '1:1',
          '2:3', '3:4', '9:16'
      ]
  
      @require('opencv-python')
      def __init__(self,
                   style='guofeng',
                   aspect_ratio='4:3'):
          super().__init__()
          if style in self.styles_option:
              self.style = style
          else:
              raise ValueError(f'The style must be one of {self.styles_option}')
          
          if aspect_ratio in self.aspect_ratio_options:
              self.aspect_ratio = aspect_ratio
          else:
              raise ValueError(f'The aspect ratio must be one of {aspect_ratio}')
  
      def apply(self,
                keywords: Annotated[str,
                                    Info('A series of Chinese keywords separated by comma.')]
          ) -> ImageIO:
          import cv2
          response = requests.post(
              url='https://magicmaker.openxlab.org.cn/gw/edit-anything/api/v1/bff/sd/generate',
              data=json.dumps({
                  "official": True,
                  "prompt": keywords,
                  "style": self.style,
                  "poseT": False,
                  "aspectRatio": self.aspect_ratio
              }),
              headers={'content-type': 'application/json'}
          )
          image_url = response.json()['data']['imgUrl']
          image_response = requests.get(image_url)
          image = cv2.imdecode(np.frombuffer(image_response.content, np.uint8), cv2.IMREAD_COLOR)
          return ImageIO(image)
  ```

- 注册工具

- ```
   /root/AgentLego/agentlego/agentlego/tools/__init__.py
  ```

- ![image-20240420160020632](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420160020632.png)

- 工具使用

  - 开启API服务

  - ```
    conda activate agent
    lmdeploy serve api_server /root/share/new_models/Shanghai_AI_Laboratory/internlm2-chat-7b \
                                --server-name 127.0.0.1 \
                                --model-name internlm2-chat-7b \
                                --cache-max-entry-count 0.1
    ```

  - 开启web demo服务

  - ```
    conda activate agent
    cd /root/agent/agentlego/webui
    python one_click.py
    ```

  - 打开端口

  - ```
    ssh -CNg -L 7860:127.0.0.1:7860 -L 23333:127.0.0.1:23333 root@ssh.intern-ai.org.cn -p 44437
    ```

  - 工具选择
  - ![image-20240420160757926](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420160757926.png)
  - ![image-20240420160827438](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420160827438.png)

  - ![image-20240420161333782](http://typora-picture-room.oss-cn-chengdu.aliyuncs.com/img/image-20240420161333782.png)

- AgentLego得使用流程
  - 先要配置Agent（需要选择我们得Agent基座）
  - 选择工具
  - 进行chat对话