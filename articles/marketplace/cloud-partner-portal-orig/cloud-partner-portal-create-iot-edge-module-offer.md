---
title: 创建 IoT Edge 模块套餐 | Microsoft Docs
description: 如何发布适用于市场的新 IoT Edge 模块。
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: d3cc1a09963c5f7fee613af24c63fd15b1cfffee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805235"
---
# <a name="how-to-publish-a-new-iot-edge-module-in-the-cloud-partner-portal"></a>如何在云合作伙伴门户中发布新的 IoT Edge 模块

本文介绍发布新 IoT Edge 模块套餐的步骤。

## <a name="prerequisites"></a>先决条件

以下先决条件与发布适用于 Azure 市场的 IoT Edge 模块相关。

-   访问[云合作伙伴门户 (CPP)](https://cloudpartner.azure.com/#alloffers)。 有关详细信息，请参阅[发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

-   在 Azure 容器注册表中托管 IoT Edge 模块。

-   准备好 IoT Edge 模块元数据（包括非详尽列表）：

    -   标题

    -   说明（基本 HTML 格式）

    -   采用 png 图像格式并且大小为 40 x 40、90 x 90、115 x 115 或 255 x 115 像素的徽标。

    -   使用条款和隐私政策

<a name="prepare-your-iot-edge-module-listing-in-cpp"></a>在 CPP 中准备 IoT Edge 模块列表
-------------------------------------------

### <a name="create-a-new-offer-of-the-type-iot-edge-module"></a>创建 IoT Edge 模块类型的新套餐 

遵循以下步骤准备 IoT Edge 模块列表：

-   登录到 [CPP 帐户](https://cloudpartner.azure.com/)。

>[!Note]
>有关云合作伙伴门户的一般信息，可以查看[学习文档](https://cloudpartner.azure.com/#learn)

-   依次选择“新建套餐”、“IoT Edge 模块”。

>[!NOTE]
>IoT Edge 模块是专门在 IoT Edge 上运行的容器。 IoT Edge 模块解决的方案必须在 IoT Edge 上下文中有效。 该模块还包括默认配置设置，使部署到 IoT Edge 设备的过程变得简单直接。 容器还可以包含 IoT Edge 模块 SDK，以便与 edgeHub 和 IoT 中心通信。

### <a name="define-your-offer-settings"></a>定义套餐设置

在“套餐设置”选项卡中输入套餐的信息。

![套餐标识](./media/cloud-partner-portal-create-iot-edge-module-offer/offer-identity.png)


-   “套餐 ID”唯一标识 CPP 中的套餐，并可在面向客户的 URL 中使用。

-   只有在 CPP 中引用此套餐时，才能看到“名称”。

### <a name="create-one-or-more-skus"></a>创建一个或多个 SKU

每个 SKU 对应于一个容器映像。 必须至少有一个 SKU，可以添加多个 SKU。 SKU 由两个部分组成：

-   SKU 元数据

-   容器元数据

**创建 SKU：**

依次选择“SKU”选项卡、“新建 SKU”。

![新建 SKU](./media/cloud-partner-portal-create-iot-edge-module-offer/SKUs.png)

SKU 元数据包含以下必填字段：
- SKU ID - 唯一标识符。
- 标题 - SKU 标题，最多 50 个字符。
- 摘要 - 简短说明，最多 100 个字符。
- 说明 - 较长的说明。
- 隐藏此 SKU - 默认值为“否”。
   
![SKU 详细信息](./media/cloud-partner-portal-create-iot-edge-module-offer/SKU-settings.png)

#### <a name="iot-edge-module-metadata-and-the-container-registry"></a>IoT Edge 模块元数据和容器注册表

IoT Edge 模块的元数据包含 Azure 容器注册表 (ACR) 中存储的映像参考信息。 Azure 市场将映像复制到公共市场注册表，经认证后可供客户使用。 由市场容器注册表满足对使用 IoT Edge 模块映像的所有用户请求。

![容器映像](./media/cloud-partner-portal-create-iot-edge-module-offer/container-images.png)

**容器映像**

“映像存储库详细信息”包含以下必填字段：

-   **订阅 ID** - ACR 注册表所在的 Azure 订阅 ID。

-   **资源组名称** – ACR 注册表的资源组名称。

-   **注册表名称** – ACR 注册表名称。

-   **存储库名称** – 存储库名称。 设置此值后，将来无法对其进行更改。 名称应该唯一，即，帐户中的其他任何套餐都不能使用相同的名称。

-   **用户名** – 与 ACR 关联的用户名（管理员用户名）。

-   **密码** – 与 ACR 关联的密码。

    >[!Note]
    >必须指定用户名和密码，以确保合作伙伴在发布过程中能够访问所述的 ACR。

发布 IoT Edge 模块映像时，可以提供一个或多个映像标记。 请务必将“latest”标记（默认值）添加到模块，以便在测试期间轻松识别该模块。

还可以指定以下 IoT Edge 模块细节：

-   **createOptions** - 要传递的默认 createOptions，以便能够即时启动原有的 IoT Edge 模块。

-   **twin:** - 要传递的默认孪生，以便在使用 IoT 模块 SDK 时，能够即时启动原有的 IoT Edge 模块。

-   **routes:** - 要传递的默认路由，以便在使用 IoT 模块 SDK 时，能够即时启动原有的 IoT Edge 模块。

### <a name="describe-your-iot-edge-module-for-your-customers"></a>向客户描述 IoT Edge 模块

在“市场”选项卡中，添加营销相关的内容。 此信息将在 Azure 市场中公开显示和列出。

![IoT Edge 模块概述](./media/cloud-partner-portal-create-iot-edge-module-offer/overview.png)

-   **标题** - 公开的 IoT Edge 模块标题。

-   **摘要** - 在大多数页面（例如浏览页面）中公开的 IoT Edge 模块摘要。

-   **长摘要** - 在 IoT Edge 模块被选作特色产品时公开的模块摘要。 

-   **说明** - 在产品详细信息页上公开的 IoT Edge 模块的说明。 （可以使用基本 HTML 标记来设置说明的格式。）

-   **营销标识符** - 用于创建产品 URL 的唯一标识符。 此 URL 采用以下格式：*azuremarketplace.microsoft.com/enus/marketplace/apps/yourpublisherid.youriotedgemodulemarketingidentifier*。

-   **预览订阅 ID** - 在 IoT Edge 模块完成认证步骤之后、上线之前，有权访问这些订阅的用户能够查看该模块。

-   **有用链接** - 最多可以添加 10 个要显示在产品详细信息页上的链接。

-   **建议的类别** - 最多选择 5 个类别。 这些类别将显示在产品详细信息页上。 目前，所有 IoT Edge 模块将显示在浏览页面中的“物联网”\>“IoT Edge 模块”类别下。

-   **徽标** - 上传 PNG 格式的 IoT Edge 模块徽标图像。 使用以下确切大小：40 x 40、90 x 90、115 x 115 或 255 x 115 像素。

-   **屏幕截图** - 屏幕截图将显示在产品详细信息页上。 它们能够十分直观地展示 IoT Edge 模块的作用及其工作原理。 例如，可以显示体系结构关系图或用例插图。

-   **视频** - 视频将显示产品详细信息页上。 它们能够十分直观地展示 IoT Edge 模块的作用及其工作原理。

-   **潜在顾客管理** - 可以选择一个系统来收集对你的产品感兴趣的所有潜在顾客。

-   **隐私** - 必须提供一个指向隐私政策的 URL。

-   **使用条款** - 必须提供使用条款。 可以使用 HTML 标记来设置此页面的格式，或指向其他页面之一。

### <a name="enter-your-support-contact-information"></a>输入支持联系人信息

在“支持”选项卡中，提供“工程联系人”和“客户支持”信息。

![支持联系人](./media/cloud-partner-portal-create-iot-edge-module-offer/support-contact-info.png)


## <a name="certify-your-iot-edge-module"></a>认证 IoT Edge 模块

提供所需的全部信息后，选择“发布”以发送 IoT Edge 模块进行认证。 此时会显示一个时间线，其中显示了认证过程的步骤。

**模块认证**

我们的认证团队将会验证该模块。 认证模块后，你会收到一个用于测试模块的专用链接。 如果测试后需要做出更改，请编辑套餐的元数据，并将模块重新提交给认证团队。 

## <a name="publish-your-iot-edge-module"></a>发布 IoT Edge 模块

完成测试并准备好发布后，选择“上线”以发布 IoT Edge 模块。

>[!Important]
>若要在 Ignite 事件中宣告推出 IoT Edge 模块，必须在 2018 年 9 月 23 日之前公开该模块。
