---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963315"
---
### <a name="launch-the-publish-wizard"></a>启动发布向导

在“解决方案资源管理器”中，右键单击 SharingService 项目，然后选择“发布”。

发布向导是自动启动的。 选择“应用服务” > “发布”以打开“创建应用服务”对话框。

### <a name="sign-in-to-azure"></a>登录 Azure

在“创建应用服务”对话框中单击“添加帐户”，并登录到 Azure 订阅。 如果已登录，请从下拉列表中选择所需的帐户。

> [!NOTE]
> 如果已经登录，请先不要选择“创建”。
>

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](resource-group.md)]

在“资源组”旁边，选择“新建”。

将资源组命名为 **myResourceGroup**，然后选择“确定”。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [app-service-plan](app-service-plan.md)]

在“托管计划”旁边，选择“新建”。

在“配置托管计划”对话框中，使用该表中的设置。

| 设置 | 建议的值 | 描述 |
|-|-|-|
|应用服务计划| MySharingServicePlan | 应用服务计划的名称。 |
| 位置 | 美国西部 | 托管 Web 应用的数据中心。 |
| 大小 | 免费 | [定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)确定托管功能。 |

选择“确定”。

### <a name="create-and-publish-the-web-app"></a>创建并发布 Web 应用

在“应用名称”中，键入唯一的应用名称（有效字符为 `a-z`、`0-9` 和 `-`），或接受自动生成的唯一名称。 Web 应用的 URL 为 `https://<app_name>.azurewebsites.net`，其中 `<app_name>` 是应用名称。

选择“创建”开始创建 Azure 资源。

向导完成后，它会将 ASP.NET Core Web 应用发布到 Azure，然后在默认浏览器中启动该应用。

![已在 Azure 中发布的 ASP.NET Web 应用](./media/spatial-anchors-azure/web-app-running-live.png)

在[创建和发布步骤](#create-and-publish-the-web-app)中指定的应用名称用作 `https://<app_name>.azurewebsites.net` 格式的 URL 前缀。 记下此 URL，因为稍后会使用它。
