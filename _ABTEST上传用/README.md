# 电商落地页转化率 A/B Test 分析

使用 Python 完成实验数据清洗、效果量估计、双侧两比例 z 检验和 95% 置信区间，并使用 Tableau 构建支持筛选联动的决策看板。

[查看分析 Notebook](notebooks/ab_test.ipynb) · [打开交互式 Tableau Dashboard](https://public.tableau.com/views/ABtest_17871506401860/1?:showVizHome=no)

## 项目背景

某电商平台计划用新版落地页替换现有页面，希望提升用户转化率。本项目通过随机对照实验回答以下问题：

- 实验分组和页面展示是否正确，数据能否用于效果评估？
- 新旧页面的总体转化率和效果量分别是多少？
- 观察到的差异是否达到统计显著，真实效果的合理范围有多大？
- 当前证据是否支持新页面全量上线？

## 关键结果

| 指标 | 结果 |
|---|---:|
| 清洗后有效用户 | 290,584 |
| 旧页面转化率 | 12.0386% |
| 新页面转化率 | 11.8808% |
| 绝对变化（新页面 - 旧页面） | -0.1578 个百分点 |
| 相对变化 | -1.31% |
| 双侧检验 p 值 | 0.1899 |
| 95% 置信区间 | [-0.3938%, 0.0781%] |

**决策建议：暂不建议以“提升转化率”为理由全量上线新页面。** 置信区间跨越 0，且 p 值高于 0.05，当前数据不足以证明新页面带来真实的转化提升。该结论不等同于证明两个页面完全等效。

## 分析方法

1. 检查缺失值、重复用户以及实验分组与实际页面不匹配的记录。
2. 仅保留 `control → old_page` 和 `treatment → new_page` 的有效映射，并按时间保留每位用户的首次有效记录。
3. 计算两组转化率、绝对变化与相对变化。
4. 在显著性水平 $\alpha=0.05$ 下执行双侧两比例 z 检验。
5. 计算转化率差的 95% 置信区间，并将统计结果转换为产品决策建议。

## 项目结构

```text
.
├── data/
│   ├── raw/                 # 原始数据与校验值
│   └── processed/           # 清洗数据与 Tableau 汇总数据
├── notebooks/
│   └── ab_test.ipynb        # 完整分析过程
├── DATA_SOURCES.md          # 数据来源说明
├── README.md
└── requirements.txt
```

## 本地运行

建议使用 Python 3.12。Windows PowerShell 示例：

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter lab notebooks/ab_test.ipynb
```

Notebook 会从 `data/raw/ab_data.csv` 读取数据，并将清洗结果写入 `data/processed/ab_clean.csv`。

## 数据说明

原始数据共 294,478 行，字段如下：

| 字段 | 含义 |
|---|---|
| `user_id` | 用户标识 |
| `timestamp` | 用户进入实验的时间 |
| `group` | `control` 对照组或 `treatment` 实验组 |
| `landing_page` | `old_page` 旧页面或 `new_page` 新页面 |
| `converted` | 是否转化，1 表示转化，0 表示未转化 |

数据来源和下载地址见 [DATA_SOURCES.md](DATA_SOURCES.md)，原始文件校验值见 `data/raw/SHA256SUMS.txt`。

## 分析边界

- 数据不包含订单金额、利润或商品信息，因此结论只讨论转化率，不推断收入影响。
- 数据不包含设备、渠道或用户属性，无法评估用户分层中的异质性效果。
- 本项目采用双侧差异检验；若业务目标是证明新页面“不劣于”旧页面，需要预先设定劣效界值并设计非劣效检验。
- GitHub 会过滤 Notebook 内嵌的 Tableau `iframe`，交互看板请通过本文顶部的 Tableau Public 链接访问。

## 技术栈

Python · pandas · NumPy · statsmodels · Jupyter · Tableau Public
