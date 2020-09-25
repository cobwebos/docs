---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 11b9c553573d9e6188ba634b4cb966d6a9b850b4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943537"
---
## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>创建 Azure 通信资源

若要创建 Azure 通信服务资源，请先登录到 [Azure 门户](https://portal.azure.com)。 在页面的左上角，选择“+ 创建资源”****。 

:::image type="content" source="../media/create-a-communication-resource/create-resource-plus-sign.png" alt-text="突出显示 Azure 门户中“创建资源”按钮的屏幕截图。":::

在“搜索市场”输入字段中或门户顶部的搜索栏中输入“通信” 。

:::image type="content" source="../media/create-a-communication-resource/searchbar-communication-portal.png" alt-text="屏幕截图显示搜索栏中的通信服务搜索内容。":::

在结果中选择“通信服务”，然后选择“添加” 。

:::image type="content" source="../media/create-a-communication-resource/add-communication-portal.png" alt-text="屏幕截图显示 Azure 面板并突出显示添加按钮。":::

现在可以配置通信服务资源。 在创建进程的第一页上，系统将要求你指定：

* 订阅
* 资源组（可以新建或选择现有的资源组）
* Azure 通信服务资源的名称
* 将要与资源关联的地理位置

在下一步中，可以为资源分配标记。 使用标记组织 Azure 资源。 有关标记的详细信息，请参阅[资源标记文档](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-resources)。

最后，你可以查看配置并“创建”资源。 请注意部署将需要几分钟时间才能完成。

## <a name="manage-your-communication-services-resource"></a>管理通信服务资源

若要管理通信服务资源，请转到 [Azure 门户](https://portal.azure.com)，然后搜索并选择“Azure 通信服务”。

在“通信服务”页上，选择资源的名称。

资源的“概述”页包含用于基本管理（例如浏览、停止、启动、重启和删除）的选项。 你可以在资源页的左侧菜单中找到更多配置选项。