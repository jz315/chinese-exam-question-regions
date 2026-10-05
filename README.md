# 中文试卷题目区域数据集

1500 页试卷，9816 个题目框，来自 453 份文档。用于训练试卷裁题模型。

[下载数据集 v0.1](https://github.com/jz315/chinese-exam-question-regions/releases/download/v0.1.0/chinese-exam-question-regions-v0.1.zip) · [版本记录](https://github.com/jz315/chinese-exam-question-regions/releases) · [标注规则](ANNOTATION_POLICY.md)

## 数据划分

| 划分 | 页数 | 题目框 |
| --- | ---: | ---: |
| train | 1124 | 7465 |
| val | 218 | 1244 |
| test | 158 | 1107 |

按整卷 split_group 划分，同一套试卷及其不同版本放在同一组。
标注方式为 AI 逐页标注与交叉复核。类别固定为 `0: question`。

## 下载与训练

下载 ZIP 并解压，目录中的 dataset.yaml 可直接用于 Ultralytics。

```python
from pathlib import Path
from ultralytics import YOLO

data = Path("chinese-exam-question-regions/dataset.yaml").resolve()
YOLO("yolo26s.pt").train(data=str(data), epochs=100, imgsz=1280)
```

模型和输入尺寸可以自行选择。传给训练接口的 data 为解压目录中 YAML 的绝对路径。

## 文件结构

```text
images/train|val|test/   原图
labels/train|val|test/   YOLO 标签
annotations/            原图像素坐标、题号与续题关系
dataset.yaml            训练配置
manifest.json           页面尺寸、来源、分组、划分与哈希
sources.json            来源记录
CHECKSUMS.sha256         文件校验和
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
