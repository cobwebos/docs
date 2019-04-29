---
title: Azure 容器映像的 SKU | Microsoft Docs
description: 配置 Azure 容器的 SKU。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 81f7e46e626bb061881be53e8cace36e1478e0e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472804"
---
# <a name="container-skus-tab"></a>“容器 SKU”选项卡

在“新建套餐”页的“SKU”选项卡中，可以创建一个或多个 SKU 并将其关联到新套餐。  可以使用不同的 SKU 按功能集、计费模型或其他特征来区分解决方案。

## <a name="sku-settings"></a>SKU 设置

开始创建新套餐时，该套餐没有任何关联的 SKU。 若要创建新 SKU，请执行以下步骤：

1. 在“SKU”选项卡中，选择“新建 SKU”

   ![新建 SKU 提示](./media/containers-sku-settings.png)

2. 提供必需的 SKU 和容器信息。 每个 SKU 对应于一个容器映像。 SKU 由两个部分组成：

    -   SKU 元数据
    -   容器元数据

### <a name="sku-metadata"></a>SKU 元数据

SKU 元数据包含用于列出容器的店面显示信息。

![SKU 元数据](./media/containers-sku-details.png)

### <a name="container-metadata"></a>容器元数据

容器元数据包含你的映像存储库在 Azure 容器注册表 (ACR) 内的详细信息的引用信息。 Azure 市场将此映像复制到特定于市场的公共注册表中，然后此映像在认证后可供客户使用。 Azure 用户发出的要使用 Azure 市场容器映像的所有请求都将通过市场的公共注册表（而不是 ACR）来进行处理。

![容器元数据](./media/containers-image-repository.png)
    
上一屏幕截图中的“映像存储库详细信息”包含以下字段：

-   **订阅 ID** - ACR 所在的 Azure 订阅 ID。
-   **资源组名称** - ACR 的资源组名称。
-   **注册表名称** - ACR 名称。
-   **存储库名称** - 存储库名称。 设置此名称后，无法更改此值。 请使用唯一名称以避免与你的帐户中的其他产品/服务冲突。
-   **用户名** - 与 ACR 映像关联的用户名（管理员用户名）。
-   **密码** - 与 ACR 映像关联的密码。

    >[!NOTE]
    >必须指定用户名和密码，以确保合作伙伴在发布过程中能够访问所述的 ACR。

### <a name="image-version"></a>映像版本

在发布容器映像时，可以提供一个或多个映像标记和 SHA 摘要。

**映像标记或摘要**
 
- 此标记或摘要必须包括一个 `latest` 标记和一个版本标记（例如，从 `xx.xx.xx-` 开始，其中 xx 是一个数字）。 它们应是面向多个平台的[清单标记](https://github.com/estesp/manifest-tool)。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 
- 可以使用标记添加容器的多个版本。 所有清单标记（`latest` 除外）必须以 `X.Y-` 或 `X.Y.Z-` 开头，其中，X、Y、Z 为整数。 <br/> 例如，如果 `latest` 标记指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32` 和 `1.0.1-windows-arm32`，则需要在此处添加这些标记。

>[!NOTE]
>请记得向你的映像添加一个**测试标记**，以便在测试期间可以标识该映像。

## <a name="next-steps"></a>后续步骤

使用[“市场”选项卡](./cpp-marketplace-tab.md)创建套餐的市场说明。 