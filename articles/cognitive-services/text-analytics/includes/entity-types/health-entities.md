---
title: 医疗保健的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122458"
---
## <a name="health-entity-categories"></a>运行状况实体类别：

[用于运行状况的文本分析](../../how-tos/text-analytics-for-health.md)返回以下实体类别。  请注意，此容器预览版仅支持英文文本，每个容器映像中只提供一个模型版本。

### <a name="named-entity-recognition"></a>命名实体识别

|类别  |说明   |
|----------|--------------|
| Age | 年龄段. 例如*30 年*。 |
| AdministrativeEvent | 管理事件。 |
| BodyStructure | 人为体，包括元件和其他结构。 例如， *arm*或*心形*。 | 
| CareEnvironment | 管理的环境。 例如，*紧急房间* | 
| ConditionQualifier | 条件级别。 例如，*轻度*、*扩展*或*扩散*。 | 
| 诊断 | 医疗条件。 例如*hypertension*。 | 
| 方向 | 趋势. 例如*left*或*anterior*。 | 
| 剂量 | 药物的大小或数量。 例如*25mg*。  | 
| ExaminationName | 方法或检查过程。 例如， *X 射线*。 | 
| RelationalOperator | 定义两个实体之间的关系的运算符。 例如，*小于*或 `>=` 。  | 
| MeasurementUnit | 度量单位 (如百分比) 。 | 
| MeasurementValue | 度量单位的数值。 | 
| FamilyRelation | Familial 关系。 例如*备用*。  | 
| 频率 | 射频.   | 
| 性别 | 性别. | 
| 基因 | 基因实体，如*TP53*。   | 
| HealthcareProfession | 管理药物的方法。 例如，*口头管理*。 | 
| MedicationClass | 药物类。 例如*抗生素*。  | 
| MedicationForm | 药物的形式。 例如*胶囊*。 | 
| MedicationName  | 名为用药的通用品牌和品牌。 例如*ibuprofen*。 | 
| MedicationRoute | 管理药物的方法。 例如，*口头管理*。 | 
| SymptomOrSign  | 病假症状。 例如*核心 throat*。 | 
| 时间 | 随时. 例如*8 年*或*2：今天早上上午 9:30* |
| TreatmentName  | 处理名称。 例如*治疗*。 | 
| 变量 | 基因实体的遗传变体。 | 

### <a name="relation-extraction"></a>关系提取

关系提取标识文本中所述概念之间的有意义的连接。 例如，通过将条件名称与时间关联来查找 "条件时间" 关系。 运行状况文本分析可以识别以下关系：

|类别  |说明   |
|----------|--------------|
| DirectionOfBodyStructure | 正文结构的方向。 |
| DirectionOfCondition | 条件的方向。 |
| DirectionOfExamination | 检查的方向。 |
| DirectionOfTreatment | 处理方向。 |
| TimeOfCondition | 与条件的开始相关联的时间。 |
| QualifierOfCondition | 条件的关联限定符。 |
| DosageOfMedication | 药物的剂量。 |
| FormOfMedication | 药物的形式。 |
| RouteOfMedication | 使用医科的路线或模式。 例如，*口头*。 |
| FrequencyOfMedication | 药物消耗的频率。 | 
| ValueOfCondition | 与条件关联的数值。 |
| UnitOfCondition | 单位 (如时间) 与条件相关联。 |
| TimeOfMedication | 药物消耗的时间。 |
| TimeOfTreatment | 管理处理的时间。 | 
| FrequencyOfTreatment | 管理处理的频率。 |
| ValueOfExamination | 与测验关联的数值。 | 
| UnitOfExamination | 单位 (如与考试关联的百分比) 。 |
| RelationOfExamination | 实体和检查之间的关系。 | 
| TimeOfExamination | 与考试关联的时间。 |
| 缩写 | 缩写。  | 
