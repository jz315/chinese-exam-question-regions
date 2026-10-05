# 数据格式

## YOLO 标签

每行一个区域：`0 cx cy width height`。四个数值按原图宽高归一化到 0–1。
images 与 labels 采用相同的页面 ID 和 train / val / test 划分。

## 像素标注

annotations/<page_id>.json 的 boxes 中包含：

| 字段 | 含义 |
| --- | --- |
| question_id | 文档内的题号或题组范围 |
| xyxy | 原图像素坐标 [x1, y1, x2, y2] |
| kind | whole 或 continuation |

question_id 在同一 source_id 内关联。跨栏、跨页片段的题号相同。

## 页面清单

manifest.json 的 records 每页一条：page_id、source_id、split_group、page_number、split、width、height、boxes，
以及 image、label、annotation 的相对路径和 SHA-256。

sha256 为图像哈希，label_sha256 为 YOLO 标签哈希，annotation_sha256 为本包像素标注哈希。
input_annotation_sha256 和 input_manifest_sha256 用于追溯生成本包的本地标注版本。
CHECKSUMS.sha256 覆盖除自身以外的所有文件。
