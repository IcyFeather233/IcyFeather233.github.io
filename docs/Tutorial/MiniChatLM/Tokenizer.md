## 准备txt语料

首先从 [中文 Wiki](https://dumps.wikimedia.org/zhwiki/) 上下载 `zhwiki-[存档日期]-pages-articles-multistream.xml.bz2` 获取中文语料数据

```
mkdir tokenizer_data

cd tokenizer_data/

wget https://dumps.wikimedia.org/zhwiki/20240401/zhwiki-20240401-pages-articles-multistream.xml.bz2
```

我的 GPU 服务器下载速度太慢了，使用了自己香港的跳板机来下载：[Jump Server](../../Blog/Utils/jump_server.md)

## 语料处理

首先使用 [WikiExtractor](https://github.com/apertium/WikiExtractor/tree/master)

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

然后，创建一个 `opencc_convert.py` 来转换：

```python
from opencc import OpenCC
from tqdm import tqdm

# 初始化转换器，t2s 表示从繁体转简体
cc = OpenCC('t2s')

# 初始化错误字符计数器
error_count = 0

# 先获取文件总行数，用于进度条
total_lines = sum(1 for line in open('wiki.txt', 'r', encoding='utf-8', errors='ignore'))

with open('wiki.txt', 'r', encoding='utf-8', errors='ignore') as f:
    # 创建一个进度条
    pbar = tqdm(total=total_lines, ncols=70)

    # 创建一个空字符串，用于存储转换后的文本
    simplified_text = ''

    # 逐行读取和转换文本
    for line in f:
        # 更新进度条
        pbar.update(1)

        # 计算原始行和忽略错误后的行的长度差，即为错误字符数量
        error_count += len(line) - len(line.encode('utf-8', 'ignore').decode('utf-8'))

        # 转换文本
        simplified_line = cc.convert(line)

        # 添加到简体文本中
        simplified_text += simplified_line

    # 关闭进度条
    pbar.close()

# 输出到新的文件
with open('wiki.simple.txt', 'w', encoding='utf-8') as f:
    f.write(simplified_text)

print(f'Total invalid characters: {error_count}')
```

运行 `python opencc_convert.py`，这个过程也要花费比较长的时间

```
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/tokenizer_data$ python opencc_convert.py 
100%|███████████████████| 11548973/11548973 [24:17<00:00, 7923.93it/s]
Total invalid characters: 0
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/tokenizer_data$ ls -al
total 6555456
drwxrwxrwx  2 mengzhuo.chen mengzhuo.chen       4096 Apr 11 09:19 .
drwxrwxr-x 12 mengzhuo.chen mengzhuo.chen       4096 Apr 11 07:46 ..
-rw-rw-rw-  1 mengzhuo.chen mengzhuo.chen      25329 Apr 11 07:49 WikiExtractor.py
-rw-rw-r--  1 mengzhuo.chen mengzhuo.chen       1167 Apr 11 08:55 opencc_convert.py
-rw-rw-rw-  1 mengzhuo.chen mengzhuo.chen 1864904100 Apr 11 09:19 wiki.simple.txt
-rw-rw-rw-  1 mengzhuo.chen mengzhuo.chen 1864875360 Apr 11 08:09 wiki.txt
-rw-rw-r--  1 mengzhuo.chen mengzhuo.chen 2982973859 Apr  2 05:02 zhwiki-20240401-pages-articles-multistream.xml.bz2
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/tokenizer_data$ head wiki.simple.txt 

数学:
数学，是研究数量、结构以及空间等概念及其变化的一门学科，属于形式科学的一种。数学利用抽象化和逻辑推理，从计数、计算、量度、对物体形状及运动的观察发展而成。数学家们拓展这些概念，以公式化新的猜想，以及从选定的公理及定义出发，严谨地推导出一些定理。
基础数学的知识与运用是生活中不可或缺的一环。对数学基本概念的完善，早在古埃及、美索不达米亚及古印度历史上的古代数学文本便可观见，而在古希腊那里有更为严谨的处理。从那时开始，数学的发展便持续不断地小幅进展，至16世纪的文艺复兴时期，因为新的科学发现和数学革新两者的交互，致使数学的加速发展，直至今日。数学并成为许多国家及地区的教育中的一部分。
数学在许多领域都有应用，包括科学、工程、医学、经济学和金融学等。数学对这些领域的应用通常被称为应用数学，有时亦会激起新的数学发现，并导致全新学科的发展，例如物理学的实质性发展中建立的某些理论激发数学家对于某些问题的不同角度的思考。数学家也研究纯粹数学，就是数学本身的实质性内容，而不以任何实际应用为目标。许多研究虽然以纯粹数学开始，但其过程中也发现许多可用之处。
词源.
西方语言中“数学”（）一词源自于古希腊语的（），其有“学习”、“学问”、“科学”，还有个较狭义且技术性的意思－「数学研究」，即使在其语源内。其形容词（），意思为「和学习有关的」或「用功的」，亦会被用来指「数学的」。其在英语中表面上的复数形式，及在法语中的表面复数形式'，可溯至拉丁文的中性复数'，由西塞罗译自希腊文复数（），此一希腊语被亚里士多德拿来指「万物皆数」的概念。
汉字表示的「数学」一词大约产生于中国宋元时期。多指象数之学，但有时也含有今天上的数学意义，例如，秦九韶的《数学九章》（《永乐大典》记，即《数书九章》也被宋代周密所著的《癸辛杂识》记为《数学大略》）、《数学通轨》（明代柯尚迁著）、《数学钥》（清代杜知耕著）、《数学拾遗》（清代丁取忠撰）。直到1939年，经过中国数学名词审查委员会研究“算学”与“数学”两词的使用状况后，确认以“数学”表示今天意义上的数学含义。
历史.
数学有着久远的历史。它被认为起源于人类早期的生产活动：中国古代的六艺之一就有「数」，数学一词在西方有希腊语词源（mathematikós），意思是“学问的基础”，源于（máthema，“科学，知识，学问”）。
```

最后将得到的 `wiki.simple.txt` 放到项目根目录的 `data` 目录下即可

```
cp wiki.simple.txt ../data/
```

## 训练 tokenizer

运行 `python train_tokenizer.py`:

```
(MiniChatLM) mengzhuo.chen@dsw-2939-6b55ccb46d-9rwqv:/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese$ python train_tokenizer.py
[00:00:17] Pre-processing sequences       ████████████████████████████████████████████████████████████████████████████████████████████ 323001   /        0
[00:00:57] Tokenize words                 ████████████████████████████████████████████████████████████████████████████████████████████ 39775199 / 39775199
[00:01:26] Count pairs                    ████████████████████████████████████████████████████████████████████████████████████████████ 39775199 / 39775199
[00:05:33] Compute merges                 ████████████████████████████████████████████████████████████████████████████████████████████ 18917    /    18917
slow tokenizer save in path: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/model_save/hf_tokenizer_slow/hf_bpe_tokenizer.josn
fast tokenizer save in path: /maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/model_save/hf_tokenizer

train tokenizer finished. you can use `AutoTokenizer.from_pretrained('/maindata/data/shared/Security-SFT/cmz/ChatLM-mini-Chinese/model_save/hf_tokenizer')` to load and test your tokenizer.
```

??? info "tokenizer 中使用 char level 和 byte level 有什么区别"
    在自然语言处理中，"tokenization"是将输入文本分割成更小的部分（或"tokens"）的过程。不同的tokenizer在如何分割文本方面有不同的策略。在讨论char-level和byte-level tokenization时，我们主要关注的是分割的粒度。

    1. **Char-level Tokenization**：在字符级别的分词中，输入被分割成单个字符。例如，字符串"hello"在字符级别的分词下会被分割成['h', 'e', 'l', 'l', 'o']。这种方法的一个优点是它可以处理任何语言，因为所有的语言都可以被分解成字符。然而，它的一个主要缺点是生成的token序列可能会非常长，这可能会导致计算效率低下，并且可能会损失一些更高层次的语义信息。

    2. **Byte-level Tokenization**：在字节级别的分词中，输入被分割成字节。这是一种更精细的分词方式，因为它可以处理字符级别分词无法处理的字符，比如特殊符号或者是非标准的Unicode字符。这种方法在处理非常复杂的文本或者是多语言的文本时非常有用。在字节级别的分词下，字符串"hello"可能被分割成一系列的字节，这取决于字符到字节的映射。Byte-level BPE（Byte Pair Encoding）是一种流行的字节级别分词方法，它可以动态地学习一个词汇表，以更有效地处理各种语言和符号。

    总的来说，char-level和byte-level分词的主要区别在于分词的粒度，以及它们如何处理各种字符和符号。

??? info "fast tokenizer 和 slow tokenizer 有什么区别？"
    "fast tokenizer"和"slow tokenizer"是Hugging Face提供的两种类型的tokenizer，它们在功能上是相同的，但在实现和性能上有所不同。

    1. **Slow Tokenizer**：这是Hugging Face最初提供的tokenizer，用Python实现。它包含了所有的tokenization方法，如基于词汇表的tokenization（如BERT），字节级的tokenization（如GPT-2）等。尽管它们在功能上很强大，但由于是用Python实现的，所以在处理大量文本时可能会比较慢。

    2. **Fast Tokenizer**：这是Hugging Face后来提供的新型tokenizer，用Rust实现。它们的功能和slow tokenizer相同，但由于是用Rust实现的，所以在处理大量文本时更快。此外，fast tokenizer还提供了一些额外的功能，如对齐原始文本和tokens（这在某些任务中很有用，如命名实体识别），以及更高效的序列批处理。

    在大多数情况下，建议使用fast tokenizer，因为它在速度和功能上都更优。但是，如果你正在处理的文本量不大，或者你需要使用一些slow tokenizer特有的功能，那么使用slow tokenizer也是可以的。

