# Formula-Retrieve-By-InternLM
[English](README.md) | 简体中文

<div align="center">



<div align="center">
  <a href="resource/figures/wechat.jpg" target="_blank">
    <img alt="Wechat" src="https://img.shields.io/badge/wechat-robot%20inside-brightgreen?logo=wechat&logoColor=white" />
  </a>
  <a href="https://huixiangdou.readthedocs.io/zh-cn/latest/" target="_blank">
    <img alt="Readthedocs" src="https://img.shields.io/badge/readthedocs-chat%20with%20AI-brightgreen?logo=readthedocs&logoColor=white" />
  </a>
  <!-- <a href="https://huixiangdou.readthedocs.io/zh-cn/latest/" target="_blank">
    <img alt="Readthedocs" src="https://img.shields.io/badge/readthedocs-black?logo=readthedocs&logoColor=white" />
  </a> -->
  <a href="https://youtu.be/ylXrT-Tei-Y" target="_blank">
    <img alt="YouTube" src="https://img.shields.io/badge/YouTube-black?logo=youtube&logoColor=red" />
  </a>
  <a href="https://www.bilibili.com/video/BV1S2421N7mn" target="_blank">
    <img alt="BiliBili" src="https://img.shields.io/badge/BiliBili-pink?logo=bilibili&logoColor=white" />
  </a>
  <a href="https://discord.gg/TW4ZBpZZ" target="_blank">
    <img alt="discord" src="https://img.shields.io/badge/discord-red?logo=discord&logoColor=white" />
  </a>


</div>

</div>

本项目基于茴香豆构建一个大模型应用，来自动检索标准或规范中的公式并计算出结果，本项目将从系统架构、数据处理、模型集成、计算模块的设计与实现、用户界面设计以及性能优化等多个方面来逐步实现和完善。茴香豆是一个基于 LLM 的专业知识助手，优势：

1. 设计预处理、拒答、响应三阶段 pipeline：
    * `chat_in_group` 群聊场景，解答问题时不会消息泛滥。见 [2401.08772](https://arxiv.org/abs/2401.08772)，[2405.02817](https://arxiv.org/abs/2405.02817)，[混合检索](./docs/zh/doc_knowledge_graph.md)和[业务数据精度测试](./evaluation)
    * `chat_with_repo` 实时聊天场景，响应更快
2. 无需训练适用各行业，提供 CPU-only、2G、10G、20G、80G 规格配置
3. 提供一整套前后端 web、android、算法源码，工业级开源可商用

查看[茴香豆已运行在哪些场景](./huixiangdou-inside.md)；加入[微信群](resource/figures/wechat.jpg)直接体验群聊助手效果。


# 🔆 系统架构设计

为了实现上述目标，项目可以采用模块化设计，主要分为以下几个部分：

- \文档解析与预处理模块：解析标准或规范中的公式，并建立公式数据库。
- \自然语言处理（NLP）模块：使用InternLM2.5-20b模型进行语义分析和公式匹配。
- \计算模块：根据解析出的公式，结合用户的输入进行计算。
- \用户接口模块：为用户提供一个友好的界面，支持输入查询并展示计算结果。
- \后台管理模块：管理和更新标准文档及公式库。



# 🔆 模块设计与实现

一、文档解析与预处理模块
首先，需要从各种标准或规范文档中提取公式。标准文档通常以PDF格式或其他结构化文档格式存在，因此，文档解析主要包含以下几个步骤：

1、文档转换：将PDF或其他格式的文档转换为文本格式，使用开源工具如PDFPlumber或PyMuPDF。

2、文本分割与清洗：分割文档内容，将文档中的文字、公式、图表等内容分离，并进行清洗，去除噪声数据。

3、公式识别与提取：使用正则表达式或专门的公式识别工具，如Mathpix API，自动提取公式并将其保存为可处理的数学表达式（例如LaTeX格式）。

4建立公式数据库：将提取的公式与对应的上下文（如章节、条款等）存储在一个数据库中，便于后续检索。

二、自然语言处理（NLP）模块
这一模块是项目的核心，通过InternLM2.5-20b模型来理解用户输入的查询，并匹配相应的标准公式：

1、预训练模型微调：根据项目的具体需求，可以对InternLM2.5-20b模型进行微调，使用领域相关的语料库对模型进行进一步训练，以提高其在特定领域的语义理解能力。

2、语义分析：用户输入的查询可能包含对标准或规范内容的描述，模型需要理解这些描述并提取出核心信息，例如相关的物理量、单位、计算要求等。

3、公式匹配：利用模型输出的语义信息，从公式数据库中检索最匹配的公式。这里可以采用相似度计算方法，如余弦相似度、BERT嵌入向量的距离计算等。

4、多步推理：在一些复杂的查询中，可能需要多步推理才能找到最终的公式。这时需要设计一个多步推理算法，让模型能够分阶段处理用户查询。

三、计算模块
这一模块负责根据匹配到的公式和用户提供的参数，进行实际的计算工作：

1、输入参数解析：解析用户输入的参数，确定其物理量、单位等，确保计算的准确性。

2、公式解析与计算：将提取出的公式转化为可执行的代码（如Python表达式），并结合用户参数进行计算。可以使用SymPy库进行符号计算和公式简化。

3、错误处理：在计算过程中，可能出现各种异常情况，如输入参数不符合要求、公式解析错误等，需要设计相应的错误处理机制，并向用户提供友好的错误提示。


四、用户接口模块
为了让用户能够方便地使用系统，需要设计一个直观的用户接口：

1、用户查询界面：提供一个简单的输入框，用户可以在其中输入对某一标准公式的查询。界面可以支持自然语言输入，并通过后端的NLP模块进行处理。

2、结果展示：将匹配到的公式和计算结果展示给用户，必要时附上计算过程和公式的出处（例如标准的具体条款号）。

3、交互优化：可以设计一些交互性更强的功能，如智能推荐、自动补全等，提升用户体验。

五、后台管理模块
后台管理模块主要用于维护和更新标准文档和公式数据库：

1、文档管理：支持上传新的标准或规范文档，自动解析并更新数据库。

2、公式库管理：提供接口以手动或自动的方式管理公式库，支持公式的增加、删除或修改。

3、日志与监控：记录系统运行日志和用户查询日志，监控系统性能，及时发现和处理潜在的问题。



<table align="center">
  <tbody>
    <tr align="center" valign="bottom">
      <td>
        <b>LLM</b>
      </td>
      <td>
        <b>文件格式</b>
      </td>
      <td>
        <b>检索方法</b>
      </td>
      <td>
        <b>接入方法</b>
      </td>
      <td>
        <b>预处理</b>
      </td>
    </tr>
    <tr valign="top">
      <td>

- [InternLM2/InternLM2.5](https://github.com/InternLM/InternLM)


</td>
<td>

- pdf
- word


</td>

<td>

- [知识图谱](./docs/zh/doc_knowledge_graph.md)
- [联网搜索](./huixiangdou/service/web_search.py)
- [SourceGraph](https://sourcegraph.com)


</td>

<td>

- 微信（[android](./docs/zh/doc_add_wechat_accessibility.md)/[wkteam](./docs/zh/doc_add_wechat_commercial.md)）
- 飞书
- [OpenXLab Web](https://openxlab.org.cn/apps/detail/tpoisonooo/huixiangdou-web)
- [Gradio Demo](./huixiangdou/gradio_ui.py)
- [HTTP Server](./huixiangdou/server.py)
- [Read the Docs](./docs/zh/doc_add_readthedocs.md)

</td>

<td>

- [指代消歧](https://arxiv.org/abs/2405.02817)

</td>

</tr>
  </tbody>
</table>

# 📦 硬件要求

以下是不同特性所需显存，区别仅在**配置选项是否开启**。


# 🔥 运行标准版

我们以标准版（本地运行 LLM，纯文本检索）为例，介绍该项目功能。其他版本仅仅是配置选项不同。

## 一、下载模型，安装依赖

首先[点击同意 BCE 模型协议](https://huggingface.co/maidalun1020/bce-embedding-base_v1)，命令行登录 huggingface

```shell
huggingface-cli login
```

安装依赖

```bash
# parsing `word` format requirements
apt update
apt install python-dev libxml2-dev libxslt1-dev antiword unrtf poppler-utils pstotext tesseract-ocr flac ffmpeg lame libmad0 libsox-fmt-mp3 sox libjpeg-dev swig libpulse-dev
# python requirements
pip install -r requirements.txt
# python3.8 安装 faiss-gpu 而不是 faiss
```

## 二、创建知识库，执行测试

我们将用 mmpose 的文档构建 mmpose 知识库，过滤问题。如有自己的文档，放入 `repodir` 下即可。

复制下面所有命令（包含 '#' 符号）建立知识库。

```shell
# 下载知识库，我们仅以 mmpose 的文档为例。repodir下可以放任何自己的文档
cd HuixiangDou
mkdir repodir
git clone https://github.com/open-mmlab/mmpose --depth=1 repodir/mmpose

# 把 repodir 的特征保存到 workdir，把正反例阈值更新进 `config.ini`
mkdir workdir
python3 -m huixiangdou.service.feature_store
```

结束后执行 `python3 -m huixiangdou.main --standalone`，此时回复 mmpose 相关问题（和知识库相关），同时不响应天气问题。



<br/>

💡 也可以启动 `gradio` 搭建一个简易的 Web UI，默认绑定 7860 端口：

```bash
python3 -m huixiangdou.gradio_ui
# 若已单独运行 `llm_server_hybrid.py`，可以 
# python3 -m huixiangdou.gradio_ui --no-standalone
```


或者启动服务端，监听 23333 端口。默认使用 `chat_with_repo` pipeline：
```bash
python3 -m huixiangdou.server

# cURL 测试状态回调接口
curl -X POST http://127.0.0.1:23333/huixiangdou_stream  -H "Content-Type: application/json" -d '{"text": "how to install mmpose","image": ""}'
# cURL 测试同步接口
curl -X POST http://127.0.0.1:23333/huixiangdou_inference  -H "Content-Type: application/json" -d '{"text": "how to install mmpose","image": ""}'
```

请调整 `repodir` 文档、[good_questions](./resource/good_questions.json) 和 [bad_questions](./resource/bad_questions.json)，尝试自己的领域知识（医疗，金融，电力等）。

## 三、集成到飞书、微信群

- [**单向**发送到飞书群](./docs/zh/doc_send_only_lark_group.md)
- [**双向**飞书群收发、撤回](./docs/zh/doc_add_lark_group.md)
- [个微 android 接入](./docs/zh/doc_add_wechat_accessibility.md)
- [个微 wkteam 接入](./docs/zh/doc_add_wechat_commercial.md)

## 四、WEB 前后端部署，零编程集成飞书微信

我们提供了完整的 typescript 前端和 python 后端服务源码：

- 支持多租户管理
- 零编程接入飞书、微信群
- 架构松散，适合 k8s

效果同 [OpenXlab APP](https://openxlab.org.cn/apps/detail/tpoisonooo/huixiangdou-web) ，请阅读 [web 部署文档](./web/README.md)。

# 🍴 其他配置

## **纯 CPU 版**

若没有 GPU，可以使用 [siliconcloud](https://siliconflow.cn/) API 完成模型推理。

以 docker miniconda+Python3.11 为例，安装 cpu 依赖，运行：

```bash
# 启动容器
docker run  -v /path/to/huixiangdou:/huixiangdou  -p 7860:7860 -p 23333:23333  -it continuumio/miniconda3 /bin/bash
# 装依赖
apt update
apt install python-dev libxml2-dev libxslt1-dev antiword unrtf poppler-utils pstotext tesseract-ocr flac ffmpeg lame libmad0 libsox-fmt-mp3 sox libjpeg-dev swig libpulse-dev
python3 -m pip install -r requirements-cpu.txt
# 建立知识库
python3 -m huixiangdou.service.feature_store  --config_path config-cpu.ini
# 问答测试
python3 -m huixiangdou.main --standalone --config_path config-cpu.ini
# gradio UI
python3 -m huixiangdou.gradio_ui --config_path config-cpu.ini
```

如果装依赖太慢，[dockerhub 里](https://hub.docker.com/repository/docker/tpoisonooo/huixiangdou/tags)提供了安装好依赖的镜像，docker 启动时替换即可。





# 🛠️ FAQ

1. 机器人太高冷/太嘴碎怎么办？

   - 把真实场景中，应该回答的问题填入`resource/good_questions.json`，应该拒绝的填入`resource/bad_questions.json`
   - 调整 `repodir` 中的文档，确保不包含场景无关内容

   重新执行 `feature_store` 来更新阈值和特征库。

   ⚠️ 如果你足够自信，也可以直接修改 config.ini 的 `reject_throttle` 数值，一般来说 0.5 是很高的值；0.2 过低。

2. 启动正常，但运行期间显存 OOM 怎么办？

   基于 transformers 结构的 LLM 长文本需要更多显存，此时需要对模型做 kv cache 量化，如 [lmdeploy 量化说明](https://github.com/InternLM/lmdeploy/blob/main/docs/zh_cn/quantization)。然后使用 docker 独立部署 Hybrid LLM Service。

3. 如何接入其他 local LLM / 接入后效果不理想怎么办？

   - 打开 [hybrid llm service](./huixiangdou/service/llm_server_hybrid.py)，增加新的 LLM 推理实现
   - 参照 [test_intention_prompt 和测试数据](./tests/test_intention_prompt.py)，针对新模型调整 prompt 和阈值，更新到 [prompt.py](./huixiangdou/service/prompt.py)

4. 响应太慢/网络请求总是失败怎么办？

   - 参考 [hybrid llm service](./huixiangdou/service/llm_server_hybrid.py) 增加指数退避重传
   - local LLM 替换为 [lmdeploy](https://github.com/internlm/lmdeploy) 等推理框架，而非原生的 huggingface/transformers

5. 机器配置低，GPU 显存不足怎么办？

   此时无法运行 local LLM，只能用 remote LLM 配合 text2vec 执行 pipeline。请确保 `config.ini` 只使用 remote LLM，关闭 local LLM

6. 报错 `(500, 'Internal Server Error')`，意为 standalone 模式启动的 LLM 服务没访问到。按如下方式定位

   - 执行 `python3 -m huixiangdou.service.llm_server_hybrid` 确定 LLM 服务无报错，监听的端口和配置一致。检查结束后按 ctrl-c 关掉。
   - 检查 `config.ini` 中各种 TOKEN 书写正确。


# 🍀 致谢

- [KIMI](https://kimi.moonshot.cn/): 长文本 LLM，支持直接上传文件
- [FlagEmbedding](https://github.com/FlagOpen/FlagEmbedding): BAAI RAG 组
- [BCEmbedding](https://github.com/netease-youdao/BCEmbedding): 中英双语特征模型
- [Langchain-ChatChat](https://github.com/chatchat-space/Langchain-Chatchat): Langchain 和 ChatGLM 的应用
- [GrabRedEnvelope](https://github.com/xbdcc/GrabRedEnvelope): 微信抢红包

# 📝 引用

```shell

```
