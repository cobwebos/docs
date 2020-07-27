---
title: Web 应用教程 - 客户端应用程序设置
description: 本教程引导你完成注册公共应用程序的步骤，以准备好部署 Web 应用程序
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: mihansen
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 8414a84190659ff31596bc202d29fe45eefdc588
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536642"
---
# <a name="client-application-registration"></a>客户端应用程序注册
在上一篇教程中，你已部署并设置了 Azure API for FHIR。 既然已设置 Azure API for FHIR，接下来可以注册一个公共客户端应用程序。 可以通读[注册公共客户端应用](register-public-azure-ad-client-app.md)操作指南来了解更多详细信息或进行故障排除，下面重点列出了此教程的主要步骤。

1. 导航到 Azure Active Directory
1. 选择“应用注册” --> “新建注册”
1. 为应用程序命名，并将重定向 URI 设置为 https://www.getpostman.com/oauth2/callback

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="“注册应用程序”窗格的屏幕截图，其中显示示例应用程序名称和重定向 URL。":::

## <a name="client-application-settings"></a>客户端应用程序设置

注册客户端应用程序后，从“概述”页中复制应用程序（客户端）ID 和租户 ID。 稍后在访问客户端时，将需要这两个值。

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="“客户端应用程序设置”窗格的屏幕截图，其中突出显示了应用程序和目录 ID。":::

### <a name="connect-with-web-app"></a>与 Web 应用连接

如果[已编写 Web 应用](tutorial-web-app-write-web-app.md)以连接 Azure API for FHIR，还需要设置正确的身份验证选项。 

1. 在左侧菜单中的“管理”下，选择“身份验证” 。 

1. 若要添加新的平台配置，请选择“Web”。

1. 设置重定向 URI，为在本教程的第四部分中创建 Web 应用程序做好准备。 要执行此操作，请将 `https://\<WEB-APP-NAME>.azurewebsites.net` 添加到重定向 URI 列表。 如果在[编写 Web 应用](tutorial-web-app-write-web-app.md)的步骤中选择了其他名称，则需要返回并更新此名称。

1. 选择“访问令牌”和“ID 令牌”复选框 。

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="应用的“身份验证设置”边栏选项卡的屏幕截图，其中突出显示了添加平台的步骤。":::

## <a name="add-api-permissions"></a>添加 API 权限

现在已设置正确的身份验证，请设置 API 权限：

1. 选择“API 权限”并单击“添加权限” 。
1. 在“我的组织使用的 API”下，搜索 Azure 医疗保健 API。
1. 选择“user_impersonation”并点击“添加权限” 。

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="添加 API 权限”边栏选项卡的屏幕截图，其中突出显示了添加 API 权限的步骤。":::

## <a name="next-steps"></a>后续步骤
你现在已拥有了一个公用客户端应用程序。 在下一教程中，我们将介绍如何通过 Postman 进行测试并获取此应用程序的访问权限。

>[!div class="nextstepaction"]
>[在 Postman 中测试客户端应用程序](tutorial-web-app-test-postman.md)
