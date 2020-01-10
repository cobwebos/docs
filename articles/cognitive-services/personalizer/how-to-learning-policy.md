---
title: 管理学习策略-Personalizer
description: 本文包含有关 Personalizer 的常见问题的解答。
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843478"
---
# <a name="manage-learning-policy"></a>管理学习策略

学习策略设置确定模型定型的_超参数_。 学习策略在 `.json` 文件中定义。

## <a name="import-a-new-learning-policy"></a>导入新的学习策略

1. 打开[Azure 门户](https://portal.azure.com)，然后选择 Personalizer 资源。
1. 选择 "**资源管理**" 部分中的 "**模型和学习设置**"。
1. 对于 "**导入学习设置**"，选择你用上面指定的 JSON 格式创建的文件，然后选择 "**上传**" 按钮。

    等待学习策略上传成功的通知。

## <a name="export-a-learning-policy"></a>导出学习策略

1. 打开[Azure 门户](https://portal.azure.com)，然后选择 Personalizer 资源。
1. 选择 "**资源管理**" 部分中的 "**模型和学习设置**"。
1. 对于 "**导入学习设置**"，请选择 "**导出学习设置**" 按钮。 这会将 `json` 文件保存到本地计算机。

## <a name="next-steps"></a>后续步骤

了解学习策略[概念](concept-active-learning.md#learning-settings)

[了解适用地区](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)