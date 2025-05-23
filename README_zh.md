<div id=top align="center">

![FlagData](flagdata_logo.png)
[![Pypi Package](https://img.shields.io/pypi/v/flagdata?label=pypi%20package)](https://pypi.org/project/flagdata/)
[![License](https://img.shields.io/github/license/FlagOpen/FlagData.svg?color=blue)](https://github.com/FlagOpen/FlagData/blob/main/LICENSE)
![GitHub release (release name instead of tag name)](https://img.shields.io/github/v/release/FlagOpen/FlagData?include_prereleases&style=social)

| [English](README.md) | [中文](README_zh.md) |

</div>

-----------------------------------------------------------------------
&emsp;&emsp;数据是人工智能领域发展的基础要素之一。随着大规模预训练模型及相关技术不断取得突破，在相应研究中使用高效数据处理工具提升数据质量变得越来越重要。因此我们推出了FlagData，一个便于使用且易于扩展的数据处理工具包。FlagData集成了包含数据获取、数据准备、数据预处理、数据分析等4个阶段的多个数据处理工具与算法，为自然语言处理、计算机视觉等领域的模型训练与部署提供了数据层面的有力支撑。

FlagData支持以下特性：

* 实现多种原始格式数据的高质量内容提取，极大降低处理成本

* 提供大模型微调数据透视功能

* 一站式高效分布式数据处理功能

完整的pipeline流程以及功能如下图：
![pipeline](pipeline_zh.png)

## 动态
- [June 13st, 2024] FlagData v3.0.0 update，支持多种数据类型，多达几十种算子池供DIY，一键生成高质量数据
- [Dec 31st, 2023] FlagData v2.0.0 升级
- [Jan 31st, 2023] FlagData v1.0.0 上线了!

--------------------------------------------------------------------------------

- [安装](#1安装)
- [快速上手](#2快速上手)
    - [数据获取阶段](#21数据获取阶段)
    - [数据准备阶段](#22数据准备阶段)
    - [数据预处理阶段](#23数据预处理阶段)
        - [语言识别](#231语言识别)
        - [数据清洗](#232数据清洗)
        - [质量评估](#233质量评估)
        - [数据去重](#234数据去重)
    - [数据分析阶段](#24数据分析阶段)
- [配置](#3配置)
    - [数据清洗](#31数据清洗)
    - [数据质量评估](#32数据质量评估)
- [算子池](#4算子池)
- [强有力的社区支持](#5强有力的社区支持)
- [使用用户](#6使用用户)
- [许可证](#7参考项目)
- [许可证](#8许可证)

# V3.0.0 UPDATE
  在社区运营的反馈下，FlagData迎来了功能升级。本次更新提供了一套傻瓜式的语言预训练数据构造工具。根据不同的数据类型，我们提供了例如 Html、Text、Book、Arxiv、Qa 等一键式数据质量提升任务。无论是小白用户还是进阶用户都能轻松生成高质量的数据。
- 小白用户：只需确认数据类型，即可生成高质量数据。
- 进阶用户：我们提供了几十种算子池，供用户DIY自己的LLM预训练数据构造过程。

**项目特点：**

- 易用性：傻瓜式操作，只需简单配置即可生成高质量数据。
- 灵活性：进阶用户可通过多种算子池自定义数据构造过程。
- 多样性：支持多种数据类型（HTML、Web、Wiki、Book、Paper、QA、Redpajama、Code）

**核心亮点**

- 🚀 一键生成高质量数据
- 🔧 多达几十种算子池供DIY
- 🌐 支持多种数据类型

## 1、安装

- requirements.txt 文件下，是FlagData项目所有的依赖包

```bash
pip install -r requirements.txt
```

**安装main分支的最新版本**

main分支为FlagData正式发布的分支，如果你想安装/更新到main分支的最新版本，请使用以下命令：

```
git clone https://github.com/FlagOpen/FlagData.git
```

## 2、快速上手

### 2.1、数据获取阶段

我们提供了基于LLM接口的数据增强模块，
利用LLM接口，以三种不同策略，构建一系列针对不同能力的单轮SFT数据。策略包括：

+ ImitateGenerator：以若干案例样本为模板，扩增数据。支持同时生成多种语言数据。
+ AbilityExtractionGenerator: 利用LLM接口，归纳出若干案例样本中包含的能力。根据这个能力集合，生成新样本和答案。
+ AbilityDirectGenerator: 根据指定的能力类型，或者任务类型，直接生成与该能力或任务相关的新样本。例如，指定能力为“逻辑推理”，则可生成一系列逻辑推理题目及答案。为增强生成样本的多样性，支持排除已生成样本。

具体示例见[数据增强模块的使用说明](flagdata/data_gen/README_zh.md)

### 2.2、数据准备阶段

&emsp;&emsp;all2txt模块下，将pdf2txt、epub2txt等非结构化/半结构化的文件转成txt，并且可以很好的解决单栏、双栏，以及图表穿插中文本的顺序等导致问题文本内容不连贯的问题。

&emsp;&emsp;同时解析后的元素种类有"Table（表格）", "FigureCaption（图片标题）", "NarrativeText【正文】", "
ListItem【参考文献】", "
Title【章节标题】", "Address【邮箱地址】","PageBreak", "Header【页眉】", "Footer【页脚】", "UncategorizedText【arxiv竖排编号】", "
Image(图)", "Formula（公式）" 等，工具脚本提供保留全文，以及按照类别解析保存两种形式。

具体示例见[all2txt模块的使用说明](flagdata/all2txt/README_zh.md)

### 2.3、数据预处理阶段

#### 2.3.1、语言识别

&emsp;&emsp;language_identification模块下，使用 fastText 的语言分类器来做分类，fastText 的语言分类器是在
Wikipedia、Tatoeba、SETimes
上面训练的，使用了 n-grams 来作为特征，使用了层级的 softmax。支持 176 种语言的分类，并且最后会输出一个 0~1 的分数。

+ 每个 CPU 核心上，每秒可以处理一千个文档。
+ 对于每一个网页做一次语言分类，得到分类的分数。
+ 对于一般清洗规则，如果大于 0.5，那么就分类为某个特定的语言，否则表示不确定是什么语言的网页并丢掉这个网页。

具体示例见[language_identification模块的使用说明](flagdata/language_identification/README_zh.md)

#### 2.3.2、数据清洗
我们提供了例如 Html、Text、Book、Arxiv、Qa 等一键式数据质量提升任务。更多自定义的功能部分，用户可以参阅“data_operator”部分。
##### TextCleaner
TextCleaner提供了一个快速且可扩展的文本数据清理工具。它提供常用的文本清理模块。
使用者仅需在cleaner_builder.py选择 text_clean.yaml文件，便可进行text数据的处理。
具体说明见[TextCleaner 的使用说明](flagdata/cleaner/docs/Text_Cleaner.md)

##### ArxivCleaner
ArxivCleaner提供了常用的arxiv文本数据清理工具。
使用者仅需在cleaner_builder.py选择 arxiv_clean.yaml文件，便可进行arxiv数据的处理。

##### HtmlCleaner
HtmlCleaner提供了常用的Html格式的文本抽取、数据清理工具。
使用者仅需运行main方法，便可进行arxiv数据的处理。

##### QaCleaner
QaCleaner提供了常用的Qa格式的文本抽取、数据清理工具。
使用者仅需运行main方法，便可进行Qa数据的处理。
具体说明见[Qa 的使用说明](flagdata/cleaner/docs/Qa_Cleaner_ZH.md)

##### BookCleaner
BookCleaner提供了常用的Book格式的文本抽取、数据清理工具。
使用者仅需运行main方法，便可进行Book数据的处理。
具体说明见[Book 的使用说明](flagdata/cleaner/docs/Book_Cleaner_ZH.md)

#### 2.3.3、质量评估

选择BERT和fasttext作为评估模型，是因为它们具有以下优点：

1. BERT模型在文本分类和理解任务中表现出色，具有强大的语言理解和表示能力，能够有效地评估文本质量。
2. FastText模型具有高效的训练和推理速度，同时保持分类性能，可以显著减少训练和推理时间。

文章比较了不同的文本分类模型，包括逻辑回归、BERT和FastText，以评估它们的性能。在实验中，BERTEval和FastText模型在文本分类任务中表现良好，其中FastText模型在精度和召回率方面表现最佳。【实验结果来自ChineseWebText】

具体示例见[quality_assessment模块的使用说明](flagdata/quality_assessment/README_zh.md)

#### 2.3.4、数据去重

&emsp;&emsp;deduplication模块下，提供海量文本数据去重能力，该阶段使用的是MinHashLSH（最小哈希局部敏感哈希）通过将文本转换为一系列哈希值，以便比较文本之间的相似性。

&emsp;&emsp;我们可以通过控制参数threshold，它代表了相似性的阈值，threshold值的范围是从0到1。设置为1时意味着完全匹配，任何文本都不会被过滤掉。相反，如果设置了较低的threshold值，相似性稍微高一些的文本也会被保留，我们可以根据需要设置更高的threshold值，以便只保留那些非常相似的文本，而丢弃那些相似性稍微低一些的文本，经验默认值为0.87；同时我们利用了Spark的分布式计算能力处理大规模数据，使用了MapReduce思想来实现去重，同时经spark调优，来高效地处理大规模文本数据集。

如下是在数据去重过程中迭代计算的相似文本，该文本在换行、编辑姓名等方面有细微区别，但是去重算法可以识别出两段文本高度相似。

```json lines
{
  "__id__": 3023656977259,
  "content": "\"2022海口三角梅花展\"已接待游客3万多名——\n三角梅富了边洋村\n一年四季,美丽的海南岛始终春意盎然、鲜花盛开,而作为海南省省花的三角梅就是其中最引人注目的鲜花品种之一,成为海南的一道亮丽风景线。\n\"可别小看这一盆盆普通的三角梅花,特别受游客喜爱。仅最近一个多月,我们就卖出了200多万元,盆栽三角梅销路火爆......吸引更多本地和外地游客来赏花、买花。(经济日报 记者 潘世鹏)\n(责任编辑:单晓冰)"
}
{
  "__id__": 3934190045072,
  "content": "记者 潘世鹏\n\"2022海口三角梅花展\"已接待游客3万多名——\n三角梅富了边洋村\n一年四季,美丽的海南岛始终春意盎然、鲜花盛开,而作为海南省省花的三角梅就是其中最引人注目的鲜花品种之一,成为海南的一道亮丽风景线。\n\"可别小看这一盆盆普通的三角梅花,特别受游客喜爱。仅最近一个多月,我们就卖出了200多万元,盆栽三角梅销路火爆。......吸引更多本地和外地游客来赏花、买花。(经济日报 记者 潘世鹏)"
}
```

spark单一能力的集成：

&emsp;&emsp;很多时候，我们想使用spark的分布式处理数据能力，这里提供了一个普通函数改造成spark udf函数，进而使用spark能力的方法

&emsp;&emsp;但是对于想要改造成spark任务的函数需要满足：

1. 数据并行性：函数的输入数据可以划分为多个部分并进行并行处理。
2. 可序列化和不可变性：Spark中的函数必须是可序列化的，以便在不同节点上传输。
3. 不依赖于特定计算节点：函数的执行不依赖于特定节点的计算资源或数据存储位置，以便能够在集群中的任何节点上执行。
4. 无状态或可共享状态：函数不依赖于外部状态或只依赖于可共享的状态。这样可以确保在不同计算节点上并行执行函数时不会发生冲突或竞争条件。

&emsp;&emsp;在使用 UDF 时，应该考虑性能和优化。有些函数可能在本地 Python 环境中运行良好，但在分布式 Spark 环境中可能效率不高。
对于复杂的逻辑或需要大量内存的函数，可能需要进一步的优化和考虑。UDF 是为了简单的逻辑和数据处理而设计的，对于更复杂的计算，可能需要使用
Spark 的原生算子来进行处理。

deduplication模块下提供了普通Python函数（判断是否是其他字符串的子字符串）使用spark
udf的改写，可以方便的使用spark分布式能力，详细请见`stringMatching.py`和`udf_spark_stringMatching.py`的对比

如果用户只是单单将python函数改成spark任务，如果没有spark集群是不行的。这里详细的写了傻瓜式搭建集群的文档，方便小白用户使用。

具体示例见[spark集群搭建](flagdata/deduplication/README_zh.md)

### 2.4、数据分析阶段

analysis数据分析模块提供如下功能：
（1）文本的平均轮次分析代码，计算平均轮次（以换行符为例）

（2）文本的领域分布

（3）文本的语言分布

（4）文本的长度分析

具体详细示例见[analysis模块的使用说明](flagdata/analysis/README_zh.md)

## 3、配置

针对`数据清洗`、`数据质量评估`模块，
我们提供了配置文件模板：[text_clean.yaml、arxiv_clean.yaml](flagData/cleaner/configs)， [bert_config.yaml](flagdata/quality_assessment/Bert/bert_config.yaml)。
配置文件为易读的 [YAML](https://yaml.org) 格式，并提供了详尽的注释。使用这些模块前请确认已经在配置文件中修改好相应参数。

以下是一些你需要注意的重要参数：

### 3.1、数据清洗

   ```yaml
   # 待清洗的原始数据
   input: ./demo/demo_input.jsonl
   # 清洗后数据的保存路径
   output: ./demo/output.jsonl
   # 待处理的字段
   source_key: text
   # key in the output file for saving
   result_key: cleanedContent
   # 需要选择的Pipline类
   cleaner_class: ArxivCleaner
   ```

### 3.2、数据质量评估

   ```yaml
   # 数据评估模型可以从[ChineseWebText下载](https://github.com/CASIA-LM/ChineseWebText)
   pretrained_model_path: "models/bert-base-chinese"
   checkpoint: "models/pretrain/2023-08-16-21-36/model-epoch_19-step_2999.pt"
   # text_key 字段为被评估的字段
   text_key: "raw_content"
   ```

## 4、算子池
我们提供了一些用于数据清洗，过滤，格式转换等的基本算子，帮助用户构建自己的数据构建流程。

提供的算子分为三种：Formatter、Pruner、Filter。Formatter用于处理结构化数据，可以用于不同格式数据的相互转换；Pruner用于清洗文本数据；Filter用于样本过滤。
下图是这些算子位于不同的处理位置以及其中一些operator的列表

<img src="pic/data_operator.png" width="50%" height="auto">

<img src="pic/some_operator.png" width="50%" height="auto">

具体详细说明见[data_operator的使用说明](flagdata/data_operator/Operator_ZH.md)

## 5、强有力的社区支持
### 社区支持
如果你对本项目的使用和代码有任何问题，可以提交issue。同时你也可以通过邮箱 data@baai.ac.cn 直接联系我们；

一个活跃的社区离不开你的贡献，如果你有新的idea，欢迎加入我们的社区，让我们成为开源的一部分，一起为开源贡献自己的力量！！！

<img src="contact_me.png" width="50%" height="auto">

或者关注智源 FlagOpen 开源体系，FlagOpen 官网 https://flagopen.baai.ac.cn/
![contact_me](FlagOpen.png)

### 问题和反馈
- 请通过GitHub Issues 来反馈问题和提出建议，我们将在24h内快速响应。
- 同时也欢迎大家在GitHub Discussions积极讨论。
- 如果不方便使用GitHub ，当然FlagData开源社区大家也可以畅所欲言，对于合理的建议，我们会在下一个版本中进行迭代。
- 我们会邀请领域专家 定期举办线上、线下交流会 分享最新的LLM的研究成果。
## 6、使用用户

<img src="pic/users.png" width="50%" height="auto">

## 7、参考项目

本项目部分参考自以下代码：
[GeneralNewsExtractor](https://github.com/GeneralNewsExtractor/GeneralNewsExtractor)
[text-data-distillation](https://github.com/arumaekawa/text-dataset-distillation)
[emoji](https://github.com/carpedm20/emoji)
[transformers](https://github.com/huggingface/transformers)
[ChineseWebText](https://github.com/CASIA-LM/ChineseWebText)
[lid](https://github.com/facebookresearch/cc_net)
[unstructured](https://github.com/Unstructured-IO/unstructured)
[minHash](https://github.com/ChenghaoMou/text-dedup)

## 8、许可证

FlagData项目基于 [Apache 2.0 协议](LICENSE)。
