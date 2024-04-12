## 数据下载

首先我们要收集海量的预训练数据，然后我们要把数据集统一变成这种格式：

```json
{
    "prompt": "对于花园街，你有什么了解或看法吗？",
    "response": "花园街（是香港油尖旺区的一条富有特色的街道，位于九龙旺角东部，北至界限街，南至登打士街，与通菜街及洗衣街等街道平行。现时这条街道是香港著名的购物区之一。位于亚皆老街以南的一段花园街，也就是\"波鞋街\"整条街约150米长，有50多间售卖运动鞋和运动用品的店舖。旺角道至太子道西一段则为排档区，售卖成衣、蔬菜和水果等。花园街一共分成三段。明清时代，花园街是芒角村栽种花卉的地方。此外，根据历史专家郑宝鸿的考证：花园街曾是1910年代东方殷琴拿烟厂的花园。纵火案。自2005年起，花园街一带最少发生5宗纵火案，当中4宗涉及排档起火。2010年。2010年12月6日，花园街222号一个卖鞋的排档于凌晨5时许首先起火，浓烟涌往旁边住宅大厦，消防接报4"
}
```

注意，我们的项目是一个 Chat LLM，所以全都是问答数据

我们把数据集全部放到 `data/raw_data` 目录下：

数据集包括：

### 社区问答 json 版 webtext2019zh-大规模高质量数据集

见：[nlp_chinese_corpus-webtext2019zh](https://github.com/brightmart/nlp_chinese_corpus?tab=readme-ov-file#4%E7%A4%BE%E5%8C%BA%E9%97%AE%E7%AD%94json%E7%89%88webtext2019zh-%E5%A4%A7%E8%A7%84%E6%A8%A1%E9%AB%98%E8%B4%A8%E9%87%8F%E6%95%B0%E6%8D%AE%E9%9B%86)

共410万，清洗后剩余260万。

这个数据集放在 [Google Drive](https://drive.google.com/open?id=1u2yW_XohbYL2YAK6Bzc5XrngHstQTf0v) 上，我可不希望本地下载之后再上传到服务器上，所以我使用 gdown 来下载，这个地方可以开个 tmux 挂在后台慢慢下

??? info "gdown"
    `gdown` 是一个 Python 库，用于下载 Google Drive 的文件。它是一个命令行工具，可以从 Google Drive 下载大文件，而无需进行复杂的身份验证过程。gdown 的主要优点是它可以下载大文件，并且可以处理 Google Drive 的配额限制。

安装 `gdown`：

```
pip install gdown
```

下载 Google Drive 文件：

```
gdown https://drive.google.com/uc?id=1u2yW_XohbYL2YAK6Bzc5XrngHstQTf0v
```

### baike_qa2018 百科类问答

见：[精baike_qa2018 百科类问答json版](https://github.com/brightmart/nlp_chinese_corpus?tab=readme-ov-file#3%E7%99%BE%E7%A7%91%E7%B1%BB%E9%97%AE%E7%AD%94json%E7%89%88baike2018qa)

共140万，清洗后剩余130万。

注：这里原项目用的是 baike_qa2019，但是写的数据来源是这个 GitHub，又不说具体有什么区别，感觉可能就是直接复制过来的，为了下载方便我们还是用 Google Drive 来进行下载

```
gdown https://drive.google.com/uc?id=1_vgGQZpfSxN_Ng9iTAvE7hM3Z7NVwXP2
```


### 中国医药领域问答数据集

见：[Chinese medical dialogue data 中文医疗问答数据集](https://github.com/Toyhom/Chinese-medical-dialogue-data)

这个就直接 git clone 下载了：

```
git clone https://github.com/Toyhom/Chinese-medical-dialogue-data.git
```

共79万，清洗后剩余79万。

### 知乎问答数据

见：[Zhihu-KOL](https://huggingface.co/datasets/wangrui6/Zhihu-KOL)

这是一个 huggingface 数据集，可以用 hf-mirror 镜像下载：

```
wget https://hf-mirror.com/hfd/hfd.sh

chmod a+x hfd.sh

export HF_ENDPOINT=https://hf-mirror.com

./hfd.sh wangrui6/Zhihu-KOL --dataset --tool aria2c -x 4
```

这个工具有个好处就是可以断点重下，如果中间有文件下载失败了，可以再运行一遍，工具会自动跳过已经下载好的文件，遇到网络不好的情况，你只要坚持不懈的重新运行下载就行了

共100万行，清洗后剩余97万行。

### belle开源的指令训练数据

介绍：[BELLE](https://github.com/LianjiaTech/BELLE)

下载：[BelleGroup](https://huggingface.co/BelleGroup)

我们选取其中的Belle_open_source_1M、train_2M_CN、train_3.5M_CN 中部分回答较短、不含复杂表格结构、翻译任务（没做英文词表）的数据，共370万行，清洗后剩余338万行。

```
export HF_ENDPOINT=https://hf-mirror.com

./hfd.sh BelleGroup/train_1M_CN --dataset --tool aria2c -x 4

./hfd.sh BelleGroup/train_2M_CN --dataset --tool aria2c -x 4

./hfd.sh BelleGroup/train_3.5M_CN --dataset --tool aria2c -x 4
```

### 维基百科（Wikipedia）词条数据

将词条拼凑为提示语，百科的前N个词为回答，使用202309的百科数据，清洗后剩余119万的词条提示语和回答

这个就是之前 Tokenizer 部分的数据，不需要额外下载了


## 数据处理

数据处理使用 [`utils/raw_data_process.py`](https://github.com/charent/ChatLM-mini-Chinese/blob/main/utils/raw_data_process.py)

上面下载下来的数据照着代码里面的路径放即可，该解压的解压，该改名的改名，该移动位置的移动位置


## 训练部分

因为预训练时间很长，首先开一个 tmux 窗口：

```
tmux new -s train

mamba activate MiniChatLM
```

如果要开启 DeepSpeed 来预训练：

```
pip install deepspeed
```

运行 `accelerate config` 来进行单机多卡的配置

```
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ accelerate config----------------------------------------------------------------------------------------------------------------------------------------------------------In which compute environment are you running?
This machine                                                                                                                                              ----------------------------------------------------------------------------------------------------------------------------------------------------------Which type of machine are you using?                                                                                                                      
multi-GPU                                                                                                                                                 
How many different machines will you use (use more than 1 for multi-node training)? [1]:                                                                  
Should distributed operations be checked while running for errors? This can avoid timeout issues but will be slower. [yes/NO]: yes                        
Do you wish to optimize your script with torch dynamo?[yes/NO]:yes                                                                                        ----------------------------------------------------------------------------------------------------------------------------------------------------------Which dynamo backend would you like to use?                                                                                                               
inductor                                                                                                                                                  
Do you want to customize the defaults sent to torch.compile? [yes/NO]:                                                                                    
Do you want to use DeepSpeed? [yes/NO]: yes                                                                                                               
Do you want to specify a json file to a DeepSpeed config? [yes/NO]:                                                                                       ----------------------------------------------------------------------------------------------------------------------------------------------------------What should be your DeepSpeed's ZeRO optimization stage?                                                                                                  
2                                                                                                                                                         ----------------------------------------------------------------------------------------------------------------------------------------------------------Where to offload optimizer states?                                                                                                                        
cpu                                                                                                                                                       ----------------------------------------------------------------------------------------------------------------------------------------------------------Where to offload parameters?                                                                                                                              
cpu                                                                                                                                                       
How many gradient accumulation steps you're passing in your script? [1]: 8                                                                                
Do you want to use gradient clipping? [yes/NO]: yes                                                                                                       
What is the gradient clipping value? [1.0]:                                                                                                               
Do you want to enable `deepspeed.zero.Init` when using ZeRO Stage-3 for constructing massive models? [yes/NO]: 
How many GPU(s) should be used for distributed training? [1]:8
----------------------------------------------------------------------------------------------------------------------------------------------------------Do you wish to use FP16 or BF16 (mixed precision)?
bf16                                                                                                                                                      
accelerate configuration saved at /home/mengzhuo.chen/.cache/huggingface/accelerate/default_config.yaml
```

得到对应这个 `default_config.yaml` 文件的内容：

```
compute_environment: LOCAL_MACHINE                                                                                                                        
debug: true
deepspeed_config:
  gradient_accumulation_steps: 8
  gradient_clipping: 1.0
  offload_optimizer_device: cpu
  offload_param_device: cpu
  zero3_init_flag: false
  zero_stage: 2
distributed_type: DEEPSPEED
downcast_bf16: 'no'
dynamo_config:
  dynamo_backend: INDUCTOR
machine_rank: 0
main_training_function: main
mixed_precision: bf16
num_machines: 1
num_processes: 8
rdzv_backend: static
same_network: true
tpu_env: []
tpu_use_cluster: false
tpu_use_sudo: false
use_cpu: false
```

然后使用项目实现的 `train.py` 来进行训练：

```
accelerate launch --multi_gpu --num_processes 8 ./train.py train
```

从断点处继续训练：

```
accelerate launch --multi_gpu --num_processes 8 ./train.py train --is_keep_training=True
```

但是此时直接运行报了错：

```
FileNotFoundErrorFileNotFoundError: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_train_dataset.parquet
```