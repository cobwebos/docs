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
ms.openlocfilehash: 58e21e46edfe1d03d42bf2202bcf1f22282631a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "84870449"
---
# <a name="client-application-registration"></a>客户端应用程序注册
在上一篇教程中，你已部署并设置了 Azure API for FHIR。 既然已设置 Azure API for FHIR，接下来可以注册一个公共客户端应用程序。 可以通读[注册公共客户端应用](register-public-azure-ad-client-app.md)操作指南来了解更多详细信息或进行故障排除，下面重点列出了此教程的主要步骤。

1. 导航到 Azure Active Directory
1. 选择“应用注册” --> “新建注册”
1. 为应用程序命名，并将重定向 URI 设置为 https://www.getpostman.com/oauth2/callback


![客户端应用程序注册](media/tutorial-web-app/reg-public-app.png)

## <a name="client-application-settings"></a>客户端应用程序设置
注册客户端应用程序后，复制“概述”页中的“应用程序(客户端) ID”。 稍后在访问客户端时需要此值。

![复制应用 ID](media/tutorial-web-app/app-id.png)

接下来，设置正确的身份验证选项。 在左侧选择“身份验证”。  选中“访问令牌”和“ID 令牌”框。   还可以设置重定向 URI，以备在本教程的第四部分中创建 Web 应用程序时使用。 为此，请将 https://\<WEB 应用名称>.azurewebsites.net 添加到重定向 URI 列表。 如果在执行[编写 Web 应用](tutorial-web-app-write-web-app.md)的步骤中选择了其他名称，则需要返回并更新此Web 应用名称。

![应用身份验证设置](media/tutorial-web-app/app-authentication.png)

设置正确的身份验证后，请设置 API 权限。 
1. 选择“API 权限”，然后单击“添加权限”  
1. 在“我的组织使用的 API”下，搜索“Azure 医疗保健 API” 
1. 选择“user_impersonation”，然后单击“添加权限”  

## <a name="next-steps"></a>后续步骤
现已创建一个公共客户端应用程序。 下一篇教程将逐步介绍如何通过 Postman 测试此应用程序并获取其访问权限。

>[!div class="nextstepaction"]
>[在 Postman 中测试客户端应用程序](tutorial-web-app-test-postman.md)
