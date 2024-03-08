---
title: 使用Megatron框架对Skywork-3b模型做SFT
author: icyfeather
date: 2024-03-08 18:11:00 +0800
categories: [Blogging, LLM]
tags: [LLM, SFT, Megatron]
---

从零开始在一台双A100卡的Linux服务器上跑通Skywork-3B模型的SFT

在Megatron-LM的官方文档里面，其实给了整体流程以及很多demo代码，公司的代码应该也是基于NV的代码进行的修改
例如下面将会提到的`aliyun_megatron-lm/configs/skywork-2-13b-contrain-sft/data_preprocess_sft.py`借鉴的应该就是NV的[官方示例代码](https://github.com/NVIDIA/Megatron-LM/blob/main/tools/preprocess_data.py)
可以先大概看看这个仓库了解整体流程思路
接下来是具体的步骤：