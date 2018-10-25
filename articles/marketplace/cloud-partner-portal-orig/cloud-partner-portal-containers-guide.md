---
title: 容器 | Microsoft Docs
description: 用于发布容器映像的步骤。
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
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5653e4b2ec9aa5e21a107611b9d4142168630d4a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805733"
---
<a name="publishing-a-container-image-in-the-cloud-partner-portal"></a>在云合作伙伴门户中发布容器映像
========================================================

本文介绍了如何在云合作伙伴门户中发布新的容器映像。

可以使用以下步骤发布新的容器映像。

1. 选择“新建产品/服务”，然后选择“容器”。

    ![为“新建产品/服务”选择“容器”选项](media/cpp-containers-guide/azure-container-offer.png)

2. 在“产品/服务设置”选项卡中，在“产品/服务标识”下，输入**产品/服务 ID**、**发布者 ID** 和**名称**。

    ![产品/服务标识](media/cpp-containers-guide/containers-offer-settings.png)

3. 在“SKU”选项卡中，选择“新建 SKU”。
    >[!NOTE]
    >可以添加多个 SKU。

    ![新建 SKU 提示](media/cpp-containers-guide/containers-sku-settings.png)

4. 提供 SKU 和容器信息。 每个 SKU 对应于一个容器映像。 SKU 由两个部分组成：

    -   SKU 元数据
    -   容器元数据

    SKU 元数据包含容器映像的显示信息。

    ![SKU 元数据](media/cpp-containers-guide/containers-sku-details.png)

    容器元数据包含你的映像存储库在 Azure 容器注册表 (ACR) 内的详细信息的引用信息。 然后，Azure 市场将此映像复制到公共市场注册表，经认证后可供客户使用。 Azure 用户发出的要使用容器映像的所有请求都将由 Marketplace 容器注册表来提供服务。

   ![容器元数据](media/cpp-containers-guide/containers-image-repository.png)

    上一屏幕截图中的“映像存储库详细信息”包含以下字段：

    -   **订阅 ID** - ACR 注册表所在的 Azure 订阅 ID。
    -   **资源组名称** - ACR 注册表的资源组名称。
    -   **注册表名称** - ACR 注册表名称。
    -   **存储库名称** - 存储库名称。 设置此值后，将来无法对其进行更改。 应当为其提供一个唯一名称，也就是说，帐户中的其他任何产品/服务都不能使用相同的名称。
    -   **用户名** - 与 ACR 关联的用户名（管理员用户名）。
    -   **密码** - 与 ACR 关联的密码。

    >[!NOTE]
    >必须指定用户名和密码，以确保合作伙伴在发布过程中能够访问所述的 ACR。

    在发布容器映像时，还可以提供一个或多个映像标记。 除了映像标记之外，合作伙伴还可以提供 SHA 摘要。 请务必向你的映像添加一个**测试标记**，以便在测试期间可以标识该映像。

5. 在“市场”选项卡中，添加特定于市场营销的内容。

    ![“市场”信息](media/cpp-containers-guide/containers-marketplace-tab.png)

6. 在“支持”选项卡中，输入你的工程联系人和客户支持信息。

   ![支持信息](media/cpp-containers-guide/containers-support-tab.png)

7. 选择“发布”以发布产品/服务。 选择“发布”后，可以看到一个时间表，其中显示了发布你的容器映像时涉及的步骤。
