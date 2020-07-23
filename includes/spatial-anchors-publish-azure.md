---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 0dab71b6d169e26a3d7dc208dd09efe1143fbe13
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67172639"
---
### <a name="open-the-publish-wizard"></a>打开发布向导

在“解决方案资源管理器”中，右键单击“SharingService”项目，然后选择“发布”    。

“发布向导”启动。 选择“应用服务” > “发布”以打开“创建应用服务”对话框。

### <a name="sign-in-to-azure"></a>登录 Azure

在“创建应用服务”对话框中，选择“添加帐户”，然后登录到你的 Azure 订阅   。 如果已登录，请从下拉列表中选择所需的帐户。

> [!NOTE]
> 如果已经登录，请先不要选择“创建”  。
>

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](resource-group.md)]

在“资源组”  旁边，选择“新建”  。

将资源组命名为 **myResourceGroup**，然后选择“确定”  。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [app-service-plan](app-service-plan.md)]

在“托管计划”旁边  ，选择“新建”  。

在“配置托管计划”对话框中，使用以下设置： 

| 设置 | 建议的值 | 说明 |
|-|-|-|
|应用服务计划| MySharingServicePlan | 应用服务计划的名称。 |
| 位置 | 美国西部 | 托管 Web 应用的数据中心。 |
| 大小 | 免费 | 确定托管功能的[定价层](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 |

选择“确定”  。

### <a name="create-and-publish-the-web-app"></a>创建并发布 Web 应用

在“应用名称”中，输入唯一的应用名称（有效字符为 `a-z`、`0-9` 和 `-`），或接受自动生成的唯一名称。 Web 应用的 URL 为 `https://<app_name>.azurewebsites.net`，其中 `<app_name>` 是应用名称。

选择“创建”  开始创建 Azure 资源。

向导完成后，它会将 ASP.NET Core Web 应用发布到 Azure，然后在默认浏览器中打开该应用。

![已在 Azure 中发布的 ASP.NET Web 应用](./media/spatial-anchors-azure/web-app-running-live.png)

本部分中使用的应用名称将用作 URL 前缀，格式为 `https://<app_name>.azurewebsites.net`。 请记下此 URL，因为稍后需要。
