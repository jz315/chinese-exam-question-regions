# 中文试卷题目区域数据集

2062 页试卷，11354 个题目框。用于训练中文试卷裁题模型，包含原图、YOLO 标签、题号和续题关系。

[下载 v0.2](https://github.com/jz315/chinese-exam-question-regions/releases/download/v0.2.0/chinese-exam-question-regions-v0.2.zip) · [版本记录](https://github.com/jz315/chinese-exam-question-regions/releases) · [标注规则](ANNOTATION_POLICY.md)

## 数据划分

| 划分 | 页数 | 题目框 |
| --- | ---: | ---: |
| train | 1124 | 7465 |
| val | 218 | 1244 |
| test | 158 | 1107 |
| additional | 562 | 1538 |
| 合计 | 2062 | 11354 |

原 1500 页的图片、标签和划分保持不变。新增 562 页单独存放在 additional，尚未划入训练集。合并训练前，需要核对新旧数据中同一套试卷的不同版本，按整卷分组重新划分。

新增数据按 auto-cut-question 的候选框逐页修正，再由另一位 AI 逐页视觉复核。595 个候选页中，562 页通过，10 页排除，23 页因缺图、题面不完整或边界无法确认暂未收入。标注均由 AI 完成，未经真人逐页核验。类别固定为 `0: question`。

## 新增科目

| 科目 | 页数 |
| --- | ---: |
| 语文 | 111 |
| 化学 | 106 |
| 生物 | 73 |
| 英语 | 65 |
| 历史 | 70 |
| 地理 | 64 |
| 政治 | 73 |

## 下载与训练

下载 ZIP 并解压，dataset.yaml 使用原 1500 页的 train / val / test。

```python
from pathlib import Path
from ultralytics import YOLO

data = Path("chinese-exam-question-regions-v0.2/dataset.yaml").resolve()
YOLO("yolo26s.pt").train(data=str(data), epochs=100, imgsz=1280)
```

模型和输入尺寸可以自行选择。传给训练接口的 data 为解压目录中 YAML 的绝对路径。

Hugging Face 版仓库名为 `ccace/chinese-exam-question-regions`，采用带内嵌图片的 Parquet 文件；上传完成后可使用：

```python
from datasets import load_dataset

pages = load_dataset("ccace/chinese-exam-question-regions")
page = pages["additional"][0]
image, boxes = page["image"], page["bboxes"]
```

Hugging Face 的 validation 对应 ZIP 中的 val。bboxes 为原图像素坐标 `[x1, y1, x2, y2]`。

## 文件结构

```text
images/train|val|test|additional/   原图
labels/train|val|test|additional/   YOLO 标签
annotations/                      原图像素坐标、题号与续题关系
dataset.yaml                      原 1500 页的训练配置
manifest.json                     页面尺寸、来源、分组、划分与哈希
sources.json                      来源记录与新增页面的公开来源链接
CHECKSUMS.sha256                   文件校验和
```

YOLO 标签格式为 `class cx cy width height`，坐标归一化到原图。
annotations 中的 xyxy 为原图像素坐标，详见 [FORMAT.md](FORMAT.md)。

## 标注规则

一个框包含题号、题干、全部选项、公式、配图及印刷小问。
共享阅读材料与对应小题合成题组；跨页、跨栏的连续题面分别成框，并共享 question_id。
标题、页码、长空白答题区和答案页排除。

测试集用于本地模型比较，部分页面用于看图排错。模型选择实验使用验证集。
反馈标注问题时，请提供 page_id、question_id、建议坐标和修改原因。

## 许可

标注与数据说明采用 CC BY 4.0，署名 jz315 / Chinese Exam Question Regions。
原卷图片保留相应权利人的权利。详见 [LICENSE.md](LICENSE.md)。
