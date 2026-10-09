# Field Documentation / 字段说明

This document describes the fields of the JobDemand-GX dataset and the keyword
dictionaries used to derive the aggregated features.

本文档说明 JobDemand-GX 数据集的字段含义，以及聚合特征所依据的枚举字典。

---

## 1. Files / 文件

| File | Description |
|---|---|
| `dataset/demand/demand_monthly.csv` | Wide demand matrix / 需求宽表：one row per city–category series, one column per month (2020-01 … 2024-12), values are monthly job posting counts. Empty cells indicate unobserved series-months (see `is_observed` below). |
| `dataset/features/features_monthly.csv` | Long feature table / 特征长表：one row per city–category–month with the 19-dimensional aggregated features. |
| `dataset/entity_map/city_map.csv` | Mapping between `city_id` and city names (Chinese / English). |
| `dataset/entity_map/category_map.csv` | Mapping between `category_id`, the original `keyword_id`, and category names (Chinese / English). |

## 2. Keys / 主键

| Column | Description |
|---|---|
| `city_id`, `city_name_zh` | City in Guangxi, China (15 cities) / 广西壮族自治区地级市 |
| `category_id`, `category_name_zh` | Job category (25 categories) / 职位类别 |
| `year_month` | Month in `YYYY-MM` format, 2020-01 to 2024-12 / 月份 |

## 3. Demand fields / 需求字段

| Column | Description |
|---|---|
| `position_count` | Number of job postings of the category in the city during the month / 当月该城市该类别的岗位发布条数 |
| `log1p_position_count` | `ln(1 + position_count)`, i.e., the Job Demand Intensity (JDI) used as the forecasting target / 岗位需求强度（JDI），预测目标 |
| `is_observed` | 1 if the series-month was observed in the raw data, 0 otherwise. Unobserved cells carry 0 in this table and empty values in the wide demand matrix. / 该"城市-类别-月份"是否在原始数据中观测到；未观测格在宽表中为空 |

## 4. Aggregated features / 聚合特征

All features are aggregated over the raw postings of one city–category–month
cell. Cells with no postings have all features equal to 0.

以下特征均按"城市-类别-月份"对原始岗位记录聚合；无岗位记录的单元格所有特征为 0。

| Column | Description |
|---|---|
| `gender_preference_strength` | Share of postings with an explicit gender requirement (`RequirementOfSex` ∈ {male, female}) / 明确要求性别的岗位占比 |
| `education_level_score` | Mean of the education ordinal weights (Table A) / 学历要求序数得分均值（见表 A） |
| `work_property_score` | Mean of the work-property weights (Table B) / 工作属性得分均值（见表 B） |
| `work_title_score` | Mean of the professional-title weights (Table C) / 职称要求得分均值（见表 C） |
| `computer_degree_score` | Mean of the computer-proficiency weights (Table D) / 计算机水平得分均值（见表 D） |
| `enterprise_property_score` | Normalized Herfindahl concentration of the enterprise-type distribution (Table E), in [0, 1]; higher values mean the employers are concentrated in fewer enterprise types / 企业性质分布的归一化赫芬达尔集中度，越高表示招聘单位类型越集中 |
| `pay_package_from_mean` | Mean of monthly salary lower bounds (`PayPackageFrom`) / 月薪下限均值 |
| `pay_package_to_mean` | Mean of monthly salary upper bounds (`PayPackageTo`) / 月薪上限均值 |
| `pay_month_mean` | Mean number of salary months per year (`PayMonth`, e.g., 13 for "13-month salary") / 年薪月数均值（如 13 薪） |
| `welfare_count_mean` | Mean number of welfare tags per posting (Table F) / 每条岗位的平均福利标签数（见表 F） |
| `position_description_len_mean` | Mean character length of job description texts / 职位描述文本的平均字符数 |
| `requirement_low_age_mean` | Mean of lower age requirements / 年龄要求下限均值 |
| `requirement_high_age_mean` | Mean of upper age requirements / 年龄要求上限均值 |
| `position_amount_sum` | Total headcount of the postings (`PositionAmount`) / 招聘人数总和 |
| `receive_graduate_ratio` | Share of postings accepting fresh graduates (`IsReceiveGraduate` = 1) / 接收应届毕业生的岗位占比 |
| `emergency_recruitment_ratio` | Share of postings flagged as urgent recruitment / 标记为紧急招聘的岗位占比 |
| `custom_keywords_count_mean` | Mean number of custom keyword tags per posting / 每条岗位的平均自定义关键词数 |

## 5. Keyword dictionaries / 枚举字典

The score fields above are derived from the `keywordID` enumerations of the
source recruitment platform, with the following ordinal weights.

### Table A. Education / 学历要求 (`RequirementOfEducationDegree`)

| keywordID | Name | Weight |
|---:|---|---:|
| 0 | 不限学历 (No requirement) | 0 |
| 351 | 初中以下 (Below junior high) | 1 |
| 352 | 初中 (Junior high) | 2 |
| 353 | 高中 (Senior high) | 3 |
| 354 | 中专 (Technical secondary) | 4 |
| 355 | 大专 (Junior college) | 5 |
| 356 | 本科 (Bachelor) | 6 |
| 357 | 研究生 (Postgraduate) | 7 |
| 358 | 硕士 (Master) | 8 |
| 359 | 博士 (PhD) | 9 |
| 360 | 博士后 (Postdoc) | 10 |

### Table B. Work property / 工作属性 (`WorkProperty`)

| keywordID | Name | Weight |
|---:|---|---:|
| 915 | 全职 (Full-time) | 1.0 |
| 916 | 兼职/零工 (Part-time) | 0.5 |
| 1009 | 实习 (Internship) | 0.25 |

### Table C. Professional title / 职称要求 (`RequirementOfWorkTitle`)

| keywordID | Name | Weight |
|---:|---|---:|
| 1 | 无 (None) | 0 |
| 2 | 高级职称 (Senior title) | 1 |
| 3 | 中级职称 (Intermediate title) | 2 |
| 4 | 初级职称 (Junior title) | 3 |

### Table D. Computer proficiency / 计算机水平 (`RequirementOfComputerDegree`)

| keywordID | Name | Weight |
|---:|---|---:|
| 601 | 无 (None) | 0 |
| 602 | 一般 (Basic) | 1 |
| 603 | 良好 (Good) | 2 |
| 604 | 精通 (Proficient) | 3 |
| 605 | 计算机一级 (NCRE Level 1) | 4 |
| 606 | 计算机二级 (NCRE Level 2) | 5 |
| 607 | 计算机三级 (NCRE Level 3) | 6 |
| 608 | 计算机四级 (NCRE Level 4) | 7 |
| 609 | 初级程序员 (Junior programmer) | 8 |
| 610 | 中级程序员 (Intermediate programmer) | 9 |
| 611 | 高级程序员 (Senior programmer) | 10 |
| 612 | 系统分析员 (System analyst) | 11 |

### Table E. Enterprise property / 企业性质 (`EnterpriseProperty`)

Used only for the concentration score; no ordinal weights.

| keywordID | Name |
|---:|---|
| 871 | 国有单位 (State-owned) |
| 872 | 个体经营 (Self-employed) |
| 873 | 民营企业 (Private) |
| 874 | 股份制公司 (Joint-stock) |
| 875 | 三资企业 (Foreign-invested) |
| 876 | 集体企业 (Collective) |
| 878 | 不限 (Unspecified) |
| 879 | 有限责任公司 (Limited liability) |
| 880 | 中外合资企业 (Sino-foreign joint venture) |
| 881 | 民办非企业性质 (Private non-enterprise) |
| 882 | 事业单位 (Public institution) |
| 883 | 有限公司 (Limited company) |
| 1563 | 社会团体法人 (Social organization) |
| 1564 | 党政机关 (Party/government organ) |
| 1565 | 行政机关 (Administrative organ) |
| 1566 | 行政机构 (Administrative agency) |

### Table F. Welfare tags / 福利标签 (`PositionWelfareIDs`)

A comma-separated list of tags per posting; `welfare_count_mean` counts them.

| keywordID | Name | keywordID | Name |
|---:|---|---:|---|
| 1063 | 周末双休 (Weekends off) | 1075 | 住房补贴 (Housing allowance) |
| 1064 | 五险 (Social insurance) | 1076 | 差旅费补贴 (Travel allowance) |
| 1065 | 住房公积金 (Housing fund) | 1077 | 员工旅游 (Staff trips) |
| 1066 | 带薪年假 (Paid annual leave) | 1078 | 员工培训 (Staff training) |
| 1067 | 年终奖金 (Year-end bonus) | 1079 | 包吃 (Meals provided) |
| 1068 | 绩效奖金 (Performance bonus) | 1080 | 包住 (Accommodation provided) |
| 1069 | 季度奖金 (Quarterly bonus) | 1096 | 补充养老保险 (Supplementary pension) |
| 1070 | 节日福利 (Holiday benefits) | 1097 | 补充医疗保险 (Supplementary medical insurance) |
| 1071 | 生日福利 (Birthday benefits) | 1098 | 企业年金 (Enterprise annuity) |
| 1072 | 通讯补贴 (Communication allowance) | 1099 | 定期体检 (Regular health check) |
| 1073 | 餐饮补贴 (Meal allowance) | | |
| 1074 | 交通补贴 (Transport allowance) | | |
