---
title: 医疗保健的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 10/02/2020
ms.author: aahi
ms.openlocfilehash: 44bce7c6dd2443ea0ed1851325a5b84f325f5fae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779724"
---
## <a name="text-analytics-for-health-categories-entities-and-attributes"></a>运行状况类别、实体和属性的文本分析

[用于运行状况的文本分析](../../how-tos/text-analytics-for-health.md) 检测到以下类别的医疗概念。   (请注意，此容器预览版仅支持英文文本，每个容器映像中只提供一个模型版本。 ) 


| 类别  | 说明  |
|---------|---------|
| 分析 | 捕获有关正文和 anatomic 系统、站点、位置或区域信息的概念。 |
 | 特征 | 捕获有关性别和年龄的信息的概念。 |
 | 试卷 | 捕获有关诊断过程和测试的信息的概念。 |
 | 基因组学 | 捕获有关基因和变体的信息的概念。 |
 | 卫生 | 捕获有关管理事件、护理环境和医疗保健职业信息的概念。 |
 | 医疗条件 | 捕获有关诊断、症状或符号信息的概念。 |
 | 量 | 捕获有关药物的信息（包括药物名称、类、剂量和管理路线）的概念。 |
 | 公关 | 捕获有关 medically 相关社交领域（如族关系）的信息的概念。 |
 | 处理 | 捕获有关 therapeutic 过程的信息的概念。 |
  
每个类别可能包括两个概念组：

* **实体** -捕获医疗概念（如诊断、药物名称或治疗名称）的术语。  例如， *bronchitis*是一个 "aspirin"，而 " *aspirin* " 是一个药物名称。  此组中的提到可以链接到 UMLS 概念 ID。
* **属性** -提供有关检测到的实体的详细信息的短语，例如， *严重* 是 *bronchitis* 的条件限定符或 *81 mg* 是 *aspirin*的剂量。  此类别中的提及不会链接到 UMLS 概念 ID。

此外，该服务还识别不同概念之间的关系，包括属性和实体之间的关系，例如 *，到**正文结构*的*方向**或用于*实体之间的关系，例如在缩写检测中。

## <a name="anatomy"></a>结构

### <a name="entities"></a>实体

**BODY_STRUCTURE** 体系统、anatomic 位置或区域，以及正文站点。 例如，arm、knee、abdomen、鼻子、liver、head、respiratory system、lymphocytes。

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="正文结构实体的示例。":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

**方向** 性术语，例如： left、侧向、upper、后验，这种术语反映了正文结构。

:::image type="content" source="../../media/ta-for-health/anatomy-attributes.png" alt-text="正文结构实体的示例。":::

### <a name="supported-relations"></a>支持的关系

* **DIRECTION_OF_BODY_STRUCTURE**

## <a name="demographics"></a>人口统计数据

### <a name="entities"></a>实体

**Age** -所有年龄条款和短语，包括患者、家庭成员等。 例如，40岁、51 yo、3个月过去、成人、幼儿、老年、年轻人、次要、中级过期。

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="正文结构实体的示例。":::


**性别** -公开主题性别的条款。 例如，男、女性、女士、gentleman、雄伟。

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

用于表达人口统计信息实体与其他信息之间关系的**RELATIONAL_OPERATOR**短语。

:::image type="content" source="../../media/ta-for-health/relational-operator.png" alt-text="正文结构实体的示例。":::

## <a name="examinations"></a>必要

### <a name="entities"></a>实体

**EXAMINATION_NAME** –诊断过程和测试。 例如，MRI、ECG、HIV、hemoglobin、platelets 计数、扩展系统（如 *Bristol stool*）。

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

描述检查特征的**方向**性术语。

:::image type="content" source="../../media/ta-for-health/exam-direction-attribute.png" alt-text="正文结构实体的示例。":::

**MEASUREMENT_UNIT** –检查的单位。 例如，在 *hemoglobin > 9.5 g/dl*中，术语 *g/dL* 是 *hemoglobin* 测试的单位。

:::image type="content" source="../../media/ta-for-health/exam-unit-attribute.png" alt-text="正文结构实体的示例。":::

**MEASUREMENT_VALUE** –检查的值。 例如，在 *hemoglobin > 9.5 g/dL*中，术语 *9.5* 是 *hemoglobin* 测试的值。

:::image type="content" source="../../media/ta-for-health/exam-value-attribute.png" alt-text="正文结构实体的示例。":::

**RELATIONAL_OPERATOR** –表示检查和其他信息之间的关系的短语。 例如，目标检查所需的度量值。

:::image type="content" source="../../media/ta-for-health/exam-relational-operator-attribute.png" alt-text="正文结构实体的示例。":::

**TIME** –与检查的开始和/或长度 (持续时间) 相关的临时术语。 例如，测试发生时。

:::image type="content" source="../../media/ta-for-health/exam-time-attribute.png" alt-text="正文结构实体的示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_EXAMINATION**
+   **RELATION_OF_EXAMINATION**
+   **TIME_OF_EXAMINATION**
+   **UNIT_OF_EXAMINATION**
+   **VALUE_OF_EXAMINATION**

## <a name="genomics"></a>基因组学

### <a name="entities"></a>实体

**基因** –基因的所有提及。 例如，MTRR、F2。

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="正文结构实体的示例。":::

**VARIANT** –基因变体的全部提及。 例如，524C>T， (MTRR) ： r.1462_1557del96
  
## <a name="healthcare"></a>医疗保健

### <a name="entities"></a>实体
  
**ADMINISTRATIVE_EVENT** –与医疗保健系统相关但具有管理/半管理性质的事件。 例如，"注册"、"许可"、"试用"、"研究" 条目、"传输"、"放电"、"住院" 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="正文结构实体的示例。":::

**CARE_ENVIRONMENT** –提供患者的环境或位置。 例如，急诊房间、医师的办公室、cardio 单位、hospice、医院。

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="正文结构实体的示例。":::

**HEALTHCARE_PROFESSION** –医疗保健实践者许可或非许可。 例如，牙医、pathologist、neurologist、radiologist、pharmacist、nutritionist、物理 therapist、chiropractor。

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="正文结构实体的示例。":::

## <a name="medical-condition"></a>医疗条件

### <a name="entities"></a>实体

**诊断** -疾病、症状、中毒。 例如，乳腺癌症，Alzheimer，HTN，CHF，痛苦缆线。

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="正文结构实体的示例。":::

**SYMPTOM_OR_SIGN** -疾病或其他诊断的主观或目标证据。 例如，披肩难点、麻烦、眩晕、鲁莽、男女、abdomen 软、良好的 bowel 声音、良好的 nourished。

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

用于描述医疗条件的**CONDITION_QUALIFIER**质量术语。 以下所有子类别均被视为限定符：

1.  与时间相关的表达式：这些是描述时间维度定性的术语，例如突然、锐音符、经常性、长久持续。 
2.  质量表达式：这些是描述医疗条件的 "性质" 的术语，如烧、清晰。
3.  严重性表达式：严重、轻度、有点不受控制。
4.  Extensivity 表达式：本地、焦点、扩散。
5.  辐射表达式： radiates、辐射。
6.  条件比例：在某些情况下，条件的特征是一个规模，这是一个有限的排序值列表。 例如，具有阶段 III 的患者胰腺癌癌症。
7.  条件课程：与某个条件的课程或进度相关的一项术语，例如改进、恶化、解决、remission。 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="正文结构实体的示例。":::

表现身体情况的**方向**性术语。

:::image type="content" source="../../media/ta-for-health/medical-condition-direction-attribute.png" alt-text="正文结构实体的示例。":::

**频率** -发生医疗情况、发生或应发生的频率。

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medical-condition-frequency-attribute-2.png" alt-text="正文结构实体的示例。":::

**MEASUREMENT_UNIT** -反映医学情况的单位。 例如，在 *1.5 x2x1 cm tumor*中 *，term 为* *tumor*的度量单位。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-unit-attribute.png" alt-text="正文结构实体的示例。":::

**MEASUREMENT_VALUE** -反映医学条件的值。 例如，在 *1.5 x2x1 cm tumor*中，术语 *1.5 x2x1* 是 *tumor*的度量值。 

:::image type="content" source="../../media/ta-for-health/medical-condition-measure-value-attribute.png" alt-text="正文结构实体的示例。":::

**RELATIONAL_OPERATOR** -在医疗条件之间表达关系的短语其他信息。 例如，时间或度量值。 

:::image type="content" source="../../media/ta-for-health/medical-condition-relational-operator.png" alt-text="正文结构实体的示例。":::

**时间** -与医疗条件的开始和/或长度 (持续时间) 相关的临时术语。 例如，当启动症状时 (开始) 或发生疾病。

:::image type="content" source="../../media/ta-for-health/medical-condition-time-attribute.png" alt-text="正文结构实体的示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_CONDITION**
+   **QUALIFIER_OF_CONDITION**
+   **TIME_OF_CONDITION**
+   **UNIT_OF_CONDITION**
+   **VALUE_OF_CONDITION**

## <a name="medication"></a>量

### <a name="entities"></a>实体

**MEDICATION_CLASS** –一组具有类似操作机制、一种相关操作模式、一个类似化学结构和/或用于处理相同疾病的药物。 例如，ACE inhibitor、opioid、抗生素、难点 relievers。

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="正文结构实体的示例。":::

**MEDICATION_NAME** –药物提及，其中包括版权品牌名称和非品牌名称。 例如，Advil、Ibuprofen。

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

**剂量** -已订购的药物量。 例如，注入 Sodium Chloride solution *1000 mL*。

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="正文结构实体的示例。":::

**FREQUENCY** -应采用的频率。

:::image type="content" source="../../media/ta-for-health/medication-frequency.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medication-frequency-2.png" alt-text="正文结构实体的示例。":::

**MEDICATION_FORM** -药物的形式。 例如，解决方案，欣然，胶囊，tablet，patch，gel，粘贴，泡沫，喷涂，删除，冰淇淋，syrup。

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="正文结构实体的示例。":::

**MEDICATION_ROUTE** -药物的管理方法。 例如，口头、vaginal、IV、epidural、主题、inhaled。

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="正文结构实体的示例。":::

**RELATIONAL_OPERATOR** 短语，用于表达药物与其他信息之间的关系。 例如，所需的度量值。

:::image type="content" source="../../media/ta-for-health/medication-relational-operator.png" alt-text="正文结构实体的示例。":::

:::image type="content" source="../../media/ta-for-health/medication-time.png" alt-text="正文结构实体的示例。":::

### <a name="supported-relations"></a>支持的关系

+ **DOSAGE_OF_MEDICATION**
+   **FORM_OF_MEDICATION**
+   **FREQUENCY_OF_MEDICATION**
+   **ROUTE_OF_MEDICATION**
+   **TIME_OF_MEDICATION**
  
## <a name="treatment"></a>处理方式

### <a name="entities"></a>实体

**TREATMENT_NAME** – Therapeutic 的过程。 例如，knee 替换外科，骨骼 marrow transplant，TAVI，低糖。

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="正文结构实体的示例。":::

### <a name="attributes"></a>特性

反映处理情况的**方向**性术语。

:::image type="content" source="../../media/ta-for-health/treatment-direction.png" alt-text="正文结构实体的示例。":::

**FREQUENCY** -处理发生或应发生的频率。

:::image type="content" source="../../media/ta-for-health/treatment-frequency.png" alt-text="正文结构实体的示例。":::
 
**RELATIONAL_OPERATOR** -表示处理和其他信息之间的关系的短语。  例如，从上一个过程传入了多少时间。

:::image type="content" source="../../media/ta-for-health/treatment-relational-operator.png" alt-text="正文结构实体的示例。":::

**时间** -与处理 (持续时间) 的开始和/或长度相关的临时术语。 例如，给定处理的日期。

:::image type="content" source="../../media/ta-for-health/treatment-time.png" alt-text="正文结构实体的示例。":::


### <a name="supported-relations"></a>支持的关系

+ **DIRECTION_OF_TREATMENT**
+   **TIME_OF_TREATMENT**
+   **FREQUENCY_OF_TREATMENT**

## <a name="social"></a>社交

### <a name="entities"></a>实体

**FAMILY_RELATION** -主题的家庭亲属提及。 例如，父亲、女儿、同级、父级。

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="正文结构实体的示例。":::
