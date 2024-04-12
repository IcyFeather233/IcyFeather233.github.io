## 数据说明

所有数据集均来自互联网公开的单轮对话数据集，经过数据清洗、格式化后保存为parquet文件。

具体请见 [`utils/raw_data_process.py`](https://github.com/charent/ChatLM-mini-Chinese/blob/main/utils/raw_data_process.py)

数据集总数量1023万：Text-to-Text预训练集：930万，评估集：2.5万

??? info "parquet文件"
    Parquet是一种列式存储格式，被广泛用于大数据处理和存储。它最初由Cloudera和Twitter共同开发，现在已经成为Apache Software Foundation的一部分。

    Parquet文件的主要特性包括：

    1. **列式存储**：与传统的行式存储（例如CSV或JSON）相比，列式存储可以更有效地进行分析查询。这是因为在大多数分析查询中，只需要处理数据的一小部分列。列式存储使得只读取必要的数据，从而大大提高了I/O效率。

    2. **压缩和编码**：Parquet支持多种压缩和编码方案，包括Snappy、Gzip、LZO、Brotli、LZ4、Zstandard等。这些压缩方案可以根据数据的特点进行选择，以实现最佳的存储效率。同时，由于它是列式存储，所以在同一列中的数据类型是相同的，这使得压缩算法可以更有效地工作。

    3. **模式演进**：Parquet支持模式演进，这意味着你可以在不破坏已有数据的情况下，向你的数据表添加新的列。

    4. **兼容性**：Parquet与许多大数据处理工具兼容，包括Hadoop、Spark、Impala、Hive、Presto等。

    5. **高效的数据读取**：Parquet文件包含了丰富的元数据信息，这使得在读取数据时可以跳过不符合条件的数据块，从而大大提高了查询速度。

    6. **向量化操作**：Parquet支持向量化操作，这可以进一步提高处理速度。

    由于以上特性，Parquet在大数据处理中得到了广泛的应用。

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

我们选取其中的Belle_open_source_1M、train_2M_CN 中部分回答较短、不含复杂表格结构、翻译任务（没做英文词表）的数据

```
export HF_ENDPOINT=https://hf-mirror.com

./hfd.sh BelleGroup/train_1M_CN --dataset --tool aria2c -x 4

./hfd.sh BelleGroup/train_2M_CN --dataset --tool aria2c -x 4
```

### 维基百科（Wikipedia）词条数据

将词条拼凑为提示语，百科的前N个词为回答，使用202309的百科数据，清洗后剩余119万的词条提示语和回答

首先从 [中文 Wiki](https://dumps.wikimedia.org/zhwiki/) 上下载 `zhwiki-[存档日期]-pages-articles-multistream.xml.bz2` 获取中文语料数据

```
mkdir tokenizer_data

cd tokenizer_data/

wget https://dumps.wikimedia.org/zhwiki/20240401/zhwiki-20240401-pages-articles-multistream.xml.bz2
```

我的 GPU 服务器下载速度太慢了，使用了自己香港的跳板机来下载：[Jump Server](../../Blog/Utils/jump_server.md)

然后我们需要把语料提取出来，使用 [WikiExtractor](https://github.com/apertium/WikiExtractor/tree/master)

```
wget https://raw.githubusercontent.com/apertium/WikiExtractor/master/WikiExtractor.py

python WikiExtractor.py --infn zhwiki-20240401-pages-articles-multistream.xml.bz2 
```

这个处理的过程要比较长的时间，有879万左右的条目，最后会输出一个 `wiki.txt`


```
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/tokenizer_data$ ls -al
total 4734260
drwxrwxrwx  2 mengzhuo.chen mengzhuo.chen       4096 Apr 11 07:50 .
drwxrwxr-x 12 mengzhuo.chen mengzhuo.chen       4096 Apr 11 07:46 ..
-rw-rw-rw-  1 mengzhuo.chen mengzhuo.chen      25329 Apr 11 07:49 WikiExtractor.py
-rw-rw-rw-  1 mengzhuo.chen mengzhuo.chen 1864875360 Apr 11 08:09 wiki.txt
-rw-rw-r--  1 mengzhuo.chen mengzhuo.chen 2982973859 Apr  2 05:02 zhwiki-20240401-pages-articles-multistream.xml.bz2
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/tokenizer_data$ head wiki.txt 

数学:
数学，是研究數量、结构以及空间等概念及其变化的一門学科，屬於形式科學的一種。數學利用抽象化和邏輯推理，從計數、計算、量度、對物體形狀及運動的觀察發展而成。數學家們拓展這些概念，以公式化新的猜想，以及從選定的公理及定義出發，嚴謹地推導出一些定理。
基礎數學的知識與運用是生活中不可或缺的一環。對數學基本概念的完善，早在古埃及、美索不达米亚及古印度历史上的古代數學文本便可觀見，而在古希臘那裡有更為嚴謹的處理。從那時開始，數學的發展便持續不斷地小幅進展，至16世紀的文藝復興時期，因为新的科學發現和數學革新兩者的交互，致使數學的加速发展，直至今日。数学并成为許多國家及地區的教育中的一部分。
数学在许多领域都有应用，包括科學、工程、醫學、經濟學和金融學等。數學對這些領域的應用通常被稱為應用數學，有時亦會激起新的數學發現，並導致全新學科的發展，例如物理学的实质性发展中建立的某些理论激发数学家对于某些问题的不同角度的思考。數學家也研究純粹數學，就是數學本身的实质性內容，而不以任何實際應用為目標。許多研究雖然以純粹數學開始，但其过程中也發現許多可用之处。
詞源.
西方语言中“數學”（）一詞源自於古希臘語的（），其有“學習”、“學問”、“科學”，還有個較狹義且技術性的意思－「數學研究」，即使在其語源內。其形容詞（），意思為「和學習有關的」或「用功的」，亦會被用來指「數學的」。其在英语中表面上的複數形式，及在法语中的表面複數形式'，可溯至拉丁文的中性複數'，由西塞罗譯自希臘文複數（），此一希臘語被亚里士多德拿來指「萬物皆數」的概念。
汉字表示的「數學」一詞大約产生于中国宋元時期。多指象數之學，但有時也含有今天上的數學意義，例如，秦九韶的《數學九章》（《永樂大典》記，即《數書九章》也被宋代周密所著的《癸辛雜識》記爲《數學大略》）、《數學通軌》（明代柯尚遷著）、《数学钥》（清代杜知耕著）、《數學拾遺》（清代丁取忠撰）。直到1939年，經過中國數學名詞審查委員會研究“算學”與“數學”兩詞的使用狀況後，確認以“數學”表示今天意義上的數學含義。
历史.
數學有着久遠的歷史。它被認為起源於人類早期的生產活動：中國古代的六艺之一就有「數」，數學一詞在西方有希腊语詞源（mathematikós），意思是“学问的基础”，源于（máthema，“科学，知识，学问”）。
```

可以看到里面还有繁体字，我们需要进行一下简繁体的转换，把繁体字都变成简体字，这一步要用到 Python 的 OpenCC 库


!!! info
    OpenCC 是一个开源的中文转换库，可以在繁体中文和简体中文之间进行转换。在 Python 中，可以使用 opencc-python-reimplemented 这个库来使用 OpenCC。以下是一个使用 opencc-python-reimplemented 将文件从繁体中文转换为简体中文的例子。

首先，需要安装 opencc-python-reimplemented。如果你还没有安装，可以使用 pip 来安装：

```bash
pip install opencc-python-reimplemented
```

后面的部分在 `utils/raw_data_process.py` 里面有对应的处理部分，包含了繁体转简体。


## 数据处理

数据处理使用 [`utils/raw_data_process.py`](https://github.com/charent/ChatLM-mini-Chinese/blob/main/utils/raw_data_process.py)

上面下载下来的数据照着代码里面的路径放即可，该解压的解压，该改名的改名，该移动位置的移动位置

运行的时候记得先全都注释掉一个一个的处理运行


```
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
[2024-04-12 01:43:27.272] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_test.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 64.3/64.3 MB 0:00:00
[2024-04-12 01:43:31.011] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_test.json，共68341行，处理后剩余42124行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_web_text_zh.parquet。耗时：3.73938s
[2024-04-12 01:43:31.012] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_train.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3.9/3.9 GB 0:00:00
[2024-04-12 01:46:55.966] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_train.json，共4121624行，处理后剩余2540865行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_web_text_zh.parquet。耗时：204.953s
[2024-04-12 01:46:55.967] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_valid.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 64.8/64.8 MB 0:00:00
[2024-04-12 01:46:59.402] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/web_text_zh_valid.json，共68345行，处理后剩余41927行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_web_text_zh.parquet。耗时：3.43487s

(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
[2024-04-12 01:51:22.956] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/baike_qa_train.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.5/1.5 GB 0:00:00
[2024-04-12 01:54:32.802] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/baike_qa_train.json，共1425170行，处理后剩余1281860行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_baike_qa.parquet。耗时：189.846s
[2024-04-12 01:54:32.803] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/baike_qa_valid.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 48.5/48.5 MB 0:00:00
[2024-04-12 01:54:38.637] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/baike_qa_valid.json，共44972行，处理后剩余40439行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_baike_qa.parquet。耗时：5.83313s

(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
[2024-04-12 01:59:00.991] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Andriatria_男科/男科5-13000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 67.8/67.8 MB 0:00:00
[2024-04-12 01:59:08.643] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Andriatria_男科/男科5-13000.repaired.csv，共94597行，处理后剩余94509行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：7.65174s
[2024-04-12 01:59:08.644] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/IM_内科/内科5000-33000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 156.4/156.4 MB 0:00:00
[2024-04-12 01:59:27.025] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/IM_内科/内科5000-33000.repaired.csv，共220607行，处理后剩余219985行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：18.3811s
[2024-04-12 01:59:27.027] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/OAGD_妇产科/妇产科6-28000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 119.6/119.6 MB 0:00:00
[2024-04-12 01:59:40.867] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/OAGD_妇产科/妇产科6-28000.repaired.csv，共183752行，处理后剩余183472行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：13.8396s
[2024-04-12 01:59:40.868] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Oncology_肿瘤科/肿瘤科5-10000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 57.4/57.4 MB 0:00:00
[2024-04-12 01:59:47.457] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Oncology_肿瘤科/肿瘤科5-10000.repaired.csv，共75554行，处理后剩余75457行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：6.58859s
[2024-04-12 01:59:47.458] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Pediatric_儿科/儿科5-14000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 72.2/72.2 MB 0:00:00
[2024-04-12 01:59:55.497] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Pediatric_儿科/儿科5-14000.repaired.csv，共101603行，处理后剩余101481行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：8.03851s
[2024-04-12 01:59:55.498] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Surgical_外科/外科5-14000.repaired.csv
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 80.1/80.1 MB 0:00:00
[2024-04-12 02:00:04.663] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/chinese_medical_dialogue_datasets/Data_数据/Surgical_外科/外科5-14000.repaired.csv，共115992行，处理后剩余115826行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_chinese_medical_dialogue.parquet。耗时：9.16507s

(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
[2024-04-12 02:01:25.248] [INFO]: process file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/Zhihu-KOL/data/train-00000-of-00005-a1278ede4e8c5cdb.parquet
Working... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:55
[2024-04-12 02:02:20.661] [INFO]: process file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/Zhihu-KOL/data/train-00001-of-00005-1fc2da944397e9c2.parquet
Working... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:55
[2024-04-12 02:03:16.050] [INFO]: process file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/Zhihu-KOL/data/train-00002-of-00005-68ced004a1458143.parquet
Working... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:56
[2024-04-12 02:04:12.462] [INFO]: process file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/Zhihu-KOL/data/train-00003-of-00005-1dae36b67c12169f.parquet
Working... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:53
[2024-04-12 02:05:06.608] [INFO]: process file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/Zhihu-KOL/data/train-00004-of-00005-c374cc9fbda9fda7.parquet
Working... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:56
[2024-04-12 02:06:03.611] [INFO]: save file to: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/zhihu_kol.parquet, 全部数据共1006218行，清洗后剩余973200行

(MiniChatLM) mengzhuo.chen@dsw-2939-6559cdd868-njpvw:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
[2024-04-12 02:46:24.113] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/bell_open_source/train_2M_CN.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.9/1.9 GB 0:00:00
[2024-04-12 02:50:27.505] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/bell_open_source/train_2M_CN.json，共2000000行，处理后剩余1731148行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_belle_cn.parquet。耗时：243.392s
[2024-04-12 02:50:27.507] [INFO]: process file:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/bell_open_source/Belle_open_source_1M.json
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 457.7/457.7 MB 0:00:00
[2024-04-12 02:51:22.348] [INFO]: 原始文件:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/raw_data/bell_open_source/Belle_open_source_1M.json，共917424行，处理后剩余844154行，保存到文件：/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/my_belle_cn.parquet。耗时：54.8411s

(MiniChatLM) mengzhuo.chen@dsw-3043-d7655499-nzk42:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.9/1.9 GB 0:00:00

(MiniChatLM) mengzhuo.chen@dsw-3043-d7655499-nzk42:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python utils/raw_data_process.py 
delete file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/wiki_zh_simple.parquet ? Yes (y) or No (n)y 
deleted.
Reading... ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.9/1.9 GB 0:00:00
[2024-04-12 04:22:50.155] [INFO]: merge into file: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/data/my_data/wiki_zh_simple.parquet, 全部数据共11548973行，清洗后剩余1220604行
```

到这里单个数据集的处理就结束了，最后是代码里面合并清洗的部分，直接一起执行了就行 `python utils/raw_data_process.py`:

??? info "相关代码"
    ```python

    if __name__ == '__main__':

        processed_file_dir = PROJECT_ROOT + '/data/my_data'
        if not exists(processed_file_dir):
            mkdir(processed_file_dir)
        
        # 注释了，不重复处理
        # 1.
        # process_web_text(keep_start=5, response_less_word=15)

        # 2.
        # process_bake_qa(response_less_word=15)

        # 3.
        # process_chinese_medical_datasets(response_less_word=15)

        # 4. 金融问答数据集质量太差了
        # process_finace_dataset(prompt_less_word=10, response_less_word=15)

        # 5.
        # process_zhihu_kol_dataset(prompt_less_word=4, response_less_word=10)

        # 6.
        # process_belle_knowledge_enhanced_dataset(response_less_words=5)

        # convert_wiki_to_simple_zh()

        # 7.
        # process_zh_wiki_data_to_datset(groups_cnt=10000, max_len=512)

        #=================================================================

        # merge
        merge_dataset_as_single_file(groups_cnt=50000, min_len=3, max_len=512, cut_max_len=True)
            
        
        remove_dataset_duplicate_rows(groups_cnt=50000)

        # # shuffle
        shuffle_parquet_dataset(
            parquet_file=PROJECT_ROOT + '/data/my_dataset.parquet', 
            shuffle_file=PROJECT_ROOT + '/data/my_dataset.shuffle.parquet',  
            seed=23333
        )

        # split train validated and test
        split_train_valid_test_datasets(
                source_parquet_file=PROJECT_ROOT + '/data/my_dataset.shuffle.parquet',
                max_len=320, 
                groups_cnt=50000
            )

        parquet_to_text()

        count_my_parquet_data(PROJECT_ROOT + '/data/my_dataset.parquet')

        dataset_length_cnt()

        process_belle_knowledge_enhanced_dataset_for_finetune(max_len=320, group_cnt=50000)

        count_my_parquet_data(PROJECT_ROOT + '/data/')

        parquet_to_json()
        count_my_json_data()
    ```
