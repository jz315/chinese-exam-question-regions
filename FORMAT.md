# 数据格式

## YOLO 标签

每行一个区域：`0 cx cy width height`。四个数值按原图宽高归一化到 0–1。
images 与 labels 采用相同的页面 ID 和 train / val / test 目录。
dataset.yaml 可直接用于训练。试卷按整卷及同场考试分组，同组页面划入同一集合。

## 像素标注

annotations/<page_id>.json 的 boxes 中包含：

| 字段 | 含义 |
| --- | --- |
| question_id | 文档内的题号或题组范围 |
| xyxy | 原图像素坐标 [x1, y1, x2, y2] |
| kind | whole 或 continuation |

question_id 在同一 source_id 内关联。跨栏、跨页片段的题号相同。human_verified 为 false。

## 页面清单

manifest.json 的 records 每页一条：page_id、source_id、split_group、page_number、split、width、height、boxes，
以及 image、label、annotation 的相对路径和 SHA-256。每页均包含 subject 科目字段。

sha256 为图像哈希，label_sha256 为 YOLO 标签哈希，annotation_sha256 为本包像素标注哈希。
CHECKSUMS.sha256 覆盖除自身以外的所有文件。

sources.json 记录来源、科目、页序和整卷分组。

## Hugging Face

Parquet 内嵌图片，划分为 train / validation / test，其中 validation 对应 ZIP 的 val。

| 字段 | 含义 |
| --- | --- |
| image | 原图，bytes 与 path 两个字段组成的 Image 类型 |
| page_id / source_id / split_group | 页面、文档和分组标识 |
| page_number / width / height | 文档页序及原图宽高 |
| subject | 页面科目 |
| bboxes | 原图像素坐标列表，每个元素为 [x1, y1, x2, y2] |
| question_ids / kinds / category_ids | 与 bboxes 同序，类别均为 0 |
| yolo_labels | 完整 YOLO 标签文本 |
| image_sha256 / label_sha256 / annotation_sha256 | 对应 ZIP 文件的哈希 |
| human_verified | false |

题号、区域类型和框按索引一一对应，图片无需额外下载。
