# 中文试卷切题数据集

**Chinese Exam Question Detection Dataset**

2360 页试卷，12431 个题目框，覆盖数学、语文、英语、物理、化学、生物、历史、地理、政治九个科目。这套中文试卷题目区域数据集用于训练 YOLO 等目标检测模型，支持题目检测、试卷分割和自动切题。

数据包含原图、YOLO 标签、像素坐标标注、题号和续题关系。类别为 `0: question`。标注采用 AI 逐页标注与交叉复核。

A Chinese exam paper layout dataset with 2,360 page images and 12,431 question bounding boxes across nine subjects. Includes YOLO labels and pixel-coordinate annotations for question detection, automatic question cropping, and document layout analysis.

[Hugging Face 数据集](https://huggingface.co/datasets/ccace/chinese-exam-question-regions) · [GitHub 仓库](https://github.com/jz315/chinese-exam-question-regions) · [下载 ZIP](https://github.com/jz315/chinese-exam-question-regions/releases/download/v0.3.0/chinese-exam-question-regions-v0.3.zip) · [数据格式](FORMAT.md) · [标注规则](ANNOTATION_POLICY.md)

## 数据划分

按整卷及同场考试分组，训练集 2094 页、验证集 130 页、测试集 136 页。

| 科目 | 训练 | 验证 | 测试 | 合计 |
| --- | ---: | ---: | ---: | ---: |
| 数学 | 882 | 15 | 14 | 911 |
| 语文 | 193 | 13 | 16 | 222 |
| 英语 | 164 | 11 | 16 | 191 |
| 物理 | 189 | 15 | 16 | 220 |
| 化学 | 126 | 15 | 15 | 156 |
| 生物 | 119 | 16 | 14 | 149 |
| 历史 | 144 | 15 | 15 | 174 |
| 地理 | 134 | 15 | 15 | 164 |
| 政治 | 143 | 15 | 15 | 173 |

## 使用

### YOLO

下载 ZIP 并解压，将 dataset.yaml 的绝对路径传给训练接口：

```python
from pathlib import Path
from ultralytics import YOLO

data = Path("chinese-exam-question-regions-v0.3/dataset.yaml").resolve()
YOLO("yolo26s.pt").train(data=str(data), epochs=100, imgsz=1280)
```

### Hugging Face

Parquet 文件内嵌原图和标注，可通过 datasets 读取：

```python
from datasets import load_dataset

dataset = load_dataset("ccace/chinese-exam-question-regions")
page = dataset["train"][0]
image, boxes = page["image"], page["bboxes"]
```

bboxes 使用原图像素坐标 `[x1, y1, x2, y2]`；YOLO 标签使用归一化坐标。

## 文件内容

| 文件或目录 | 内容 |
| --- | --- |
| images/ | 试卷原图 |
| labels/ | YOLO 标签 |
| annotations/ | 像素坐标、题号与续题关系 |
| dataset.yaml | YOLO 训练配置 |
| manifest.json | 页面尺寸、来源、划分与文件哈希 |
| sources.json | 来源记录 |
| CHECKSUMS.sha256 | 文件校验和 |

## 标注规则

一个框包含题号、题干、全部选项、公式、配图及印刷小问。共享阅读材料与对应小题合成题组；跨页、跨栏的连续题面分别成框，并共享 question_id。标题、页码、长空白答题区和答案页排除。

反馈标注问题时，请提供 page_id、question_id 和建议坐标。

## 许可

标注与数据说明采用 CC BY 4.0，署名 jz315 / Chinese Exam Question Regions。原卷图片保留相应权利人的权利。详见 [LICENSE.md](LICENSE.md)。
