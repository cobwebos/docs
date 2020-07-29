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
ms.openlocfilehash: 4ac65e85e05f408b8d2f37a1d6845dc9e28e2bab
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373058"
---
## <a name="health-entity-categories"></a>运行状况实体类别：

[用于运行状况的文本分析](../../how-tos/text-analytics-for-health.md)返回以下实体类别。  请注意，此容器预览版仅支持英文文本，每个容器映像中只提供一个模型版本。

### <a name="named-entity-recognition"></a>命名实体识别

|类别  |说明   |
|----------|--------------|
| 年 | 年龄段. |
| BODY_STRUCTURE | 人为体，包括元件和其他结构。 | 
| CONDITION_QUALIFIER | 条件级别，如*轻度*、*扩展*或*扩散*。 | 
| 诊断 | 医疗条件。 例如*hypertension* 。 | 
| 方向键 | *左*或*anterior*等方向。 | 
| 剂量 | 药物的大小或数量。  | 
| EXAMINATION_NAME | 方法或检查过程。 | 
| EXAMINATION_RELATION | 度量单位和考试之间的关联。  | 
| EXAMINATION_UNIT | 用于检查的测量单位。 | 
| EXAMINATION_VALUE | 检查度量单位的值。 | 
| FAMILY_RELATION | Familial 关系，如*备用*。  | 
| 频率 | 射频.   | 
| 性别 | 性别. | 
| 基因 | 基因实体，如*TP53*。   | 
| MEDICATION_CLASS | 药物类。 例如*抗生素*。  | 
| MEDICATION_NAME  | 名为用药的通用品牌和品牌。| 
| ROUTE_OR_MODE  | 管理药物的方法。 | 
| SYMPTOM_OR_SIGN  | 病假症状。 | 
| TIME  | 随时. 例如 "8 年" 或 "2：上午9:30 这一上午" |
| TREATMENT_NAME  | 处理名称。 | 
| VARIANT  | 基因实体的遗传变体 | 

### <a name="relation-extraction"></a>关系提取

关系提取标识文本中所述概念之间的有意义的连接。 例如，通过将条件名称与时间关联来查找 "条件时间" 关系。 运行状况文本分析可以识别以下关系：

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* 缩写 
