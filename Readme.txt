# Variance-Weighted LBP (Halcon)
Variance-weighted LBP feature extraction for face detection

（方差加权 LBP 特征描述子算法）

## 说明

1. 测试程序用于验证算法是否能正常运行
2. 为优化加速，采用预计算 LBP 图的方式运行
3. `compute_lbp_his` 函数在预计算 LBP 图基础上运行，提供两种统计方式：
   - 方式 1：权重方式，用 `gray_histo` 加速
   - 方式 2：保留权重，在统计上进行优化
4. 两种方式的耗时差异（参考 70 个 ROI 框）：
   - 方式 1：单 ROI 约 0.007s，总计可达 0.2s
   - 方式 2：单 ROI 约 0.03s，总计 2~11s
   - 具体效果视检测 ROI 数量决定
5. 最终输出的 `Histogram` 参数为 511 维 LBP 特征

## 方法

方差加权 LBP 特征 + Fisher 判别（也可将 511 维特征输入 MLP 处理）+ 密度聚类后处理。

## 后处理（关键）

得分图中背景得分偏高（0.8+），人脸得分集中在 0.6~0.8，单阈值无法分离。
采用密度聚类：点筛选 → 区域增长 → IoU 合并。
试过 IoU / DIoU 几版，最终用区域增长版本。

## 性能

单帧约 2s，召回率约 0.83，无 GPU。
（该速度基于方式 2 的 LBP 统计方式）

## 文件

- `pro_compute_LBP.hdev`：Halcon 代码（需 license），LBP 预计算图
- `compute_LBP_his.hdev`：Halcon 代码（需 license），LBP 特征统计（511 维）
- `pro_compute_LBP.txt`：导出的文本版（无需 license）
- `compute_LBP_his.txt`：导出的文本版（无需 license）
- `方差加权LBP特征描述子测试程序.hdev`：Halcon 代码（需 license）
- `方差加权LBP特征描述子测试程序.txt`：导出的文本版（无需 license）

## License

MIT