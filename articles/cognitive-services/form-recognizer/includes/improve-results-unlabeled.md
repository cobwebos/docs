---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379279"
---
检查 `"pageResults"` 节点下每个键/值结果的 `"confidence"` 值。 还应查看 `"readResults"` 节点中的置信度分数，这些分数对应于文本读取操作。 读取结果的置信度不影响键/值提取结果的置信度，因此二者都应检查。
* 如果读取操作的置信度分数低，请尝试提高输入文档的质量（请参阅[输入要求](../overview.md#input-requirements)）。
* 如果键/值提取操作的置信度分数低，请确保所分析的文档与训练集中使用的文档是相同的类型。 如果训练集中的文档在外观上有差异，请考虑将它们拆分成不同的文件夹，针对每项差异训练一个模型。

目标置信度分数将取决于用例，但通常情况下，最好将分数设定为 80% 或更高。 对于更敏感的情况，如读取医疗记录或账单，建议将分数设置为 100%。