---
title: 连接到 GitHub
description: 使用 GitHub 指定你的通用数据模型实体引用
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84771030"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>使用 GitHub 读取公共数据模型实体引用

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的 GitHub 连接器仅用于接收映射数据流中 [常见数据模型](format-common-data-model.md) 格式的实体引用架构。

## <a name="linked-service-properties"></a>链接服务属性

GitHub 链接服务支持以下属性。

| 属性 | 说明 | 必须 |
|:--- |:--- |:--- |
| type | Type 属性必须设置为 **GitHub**。 | 是
| userName | GitHub 用户名 | 是 |
| password | GitHub 密码 | 是 |

## <a name="next-steps"></a>后续步骤

在映射数据流中创建 [源数据集](data-flow-source.md) 。