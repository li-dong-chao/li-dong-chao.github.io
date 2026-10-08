---
title: ChatGlm3简单上手
date: 2024-03-04
categories: ["llm"]
tags: ["ChatGLM3-6B"]
---

最近对去年10月份开源的 `ChatGLM3` 比较感兴趣，刚好最近找到了一块 `V100` ，便上手玩一玩，本文记录一下使用过程。

`ChatGLM3` 一共开源了三款模型，分别是 `ChatGLM-6B` 、 `ChatGLM3-6B-Base` 和 `ChatGLM3-6B-32K` 。根据官方介绍，其各方面性能都领先于同参数量的模型。官方提供的性能测试数据如下：

| Model            | GSM8K | MATH | BBH  | MMLU | C-Eval | CMMLU | MBPP | AGIEval |
| ---------------- | ----- | ---- | ---- | ---- | ------ | ----- | ---- | ------- |
| ChatGLM2-6B-Base | 32.4  | 6.5  | 33.7 | 47.9 | 51.7   | 50.0  | -    | -       |
| Best Baseline    | 52.1  | 13.1 | 45.0 | 60.1 | 63.5   | 62.2  | 47.5 | 45.8    |
| ChatGLM3-6B-Base | 72.3  | 25.7 | 66.1 | 61.4 | 69.0   | 67.5  | 52.4 | 53.7    |

以上为 `ChatGLM3` 在其官方选取的8个中英文典型数据集上的测试表现，其中 `Best Baseline ` 指的是截止 2023年10月27日、模型参数在 10B 以下、在对应数据集上表现最好的预训练模型，不包括只针对某一项任务训练而未保持通用能力的模型。

另外，官方对 `ChatGLM3-6B-32K` 在长文本应用场景下的表现也进行了测试，相较于 `ChatGLM2-6B-32K` ，三代平均效果提升超过50%，在论文阅读、文档摘要和财报分析等应用中，提升尤为显著。以下为在 `LongBench` 评测集上的测评表现。

| Model           | 平均 | Summary | Single-Doc QA | Multi-Doc QA | Code | Few-shot | Synthetic |
| --------------- | ---- | ------- | ------------- | ------------ | ---- | -------- | --------- |
| ChatGLM2-6B-32K | 41.5 | 24.8    | 37.6          | 34.7         | 52.8 | 51.3     | 47.7      |
| ChatGLM3-6B-32K | 50.2 | 26.6    | 45.8          | 46.1         | 56.2 | 61.2     | 65        |

> 但从上述测试数据来看，`ChatGLM3` 确实较之前版本有较大提升。

## 部署过程

关于部署，官方提供了比较详细的[部署教程](https://github.com/THUDM/ChatGLM3?tab=readme-ov-file#%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F)，其中需要注意的点主要集中于：

1. 受限于网络问题，国内从 `hugging face` 下载模型文件较慢。官方提供了其在 `modelscope` 平台的下载地址，可以参考[这里](https://modelscope.cn/models/ZhipuAI/chatglm3-6b/summary)下载；

2. 在配置环境时注意 `torch` 版本和 本地cuda环境的版本对应关系，不要盲目地执行 `pip install -r requirements.txt` ，官方建议的 `torch` 版本可能与本地的cuda环境不匹配，导致无法正常加载模型，两者之间的对应关系可以参考[pytorch官方文档](https://pytorch.org/get-started/previous-versions/)。

    > 当发现下面的报错时，就需要检查 `torch` 和 `cuda` 的版本对应关系是否有问题了。
    >
    > ![image-20240304200740699](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20240304200740699.png)

智谱AI官方还提供了[技术文档](https://zhipu-ai.feishu.cn/wiki/HIj5wVxGqiUg3rkbQ1OcVEe5n9g)，安装时也可以参考。

官方提供了三种方式来启动网页版的demo服务，启动方式分别如下：

```shell
# 方式一
python basic_demo/web_demo_gradio.py
# 方式二
python basic_demo/web_demo_streamlit.py
# 方式三
streamlit run basic_demo/web_demo_streamlit.py
```

基于方式三启动后的web界面如下：

![image-20240304201512440](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20240304201512440.png)

部署完成，简单使用后体验感觉还不错，关于数学、代码、通用问答方面，该模型都能生成相对正确的回复。但由于没有结合任何实际的业务场景，仅仅简单的问答并不能体现模型的实际应用价值，后续需要在结合实际场景进行测试。