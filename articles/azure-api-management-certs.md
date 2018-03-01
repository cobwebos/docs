---
title: "上传 Azure Service Management 证书 | Microsoft Docs"
description: "了解如何为 Azure 门户上传 Service Management 证书。"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: a865290602f64335e8b7cbfadf00d83234ae812d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="upload-an-azure-service-management-certificate"></a>上传 Azure Service Management 证书
管理证书使你可以使用 Azure 提供的经典部署模型进行身份验证。 许多程序和工具（如 Visual Studio 或 Azure SDK）会使用这些证书来自动配置和部署各种 Azure 服务。 

> [!WARNING]
> 请小心！ 这些类型的证书允许任何使用它们进行身份验证的人管理与它们相关联的订阅。
>
>

若要深入了解 Azure 证书（包括创建自签名的证书）的详细信息，请参阅 [Azure 云服务的证书概述](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)。

还可以使用 [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) 对客户端代码进行身份验证，以用于自动化目的。

**注意：**必须是订阅的共同管理员，才能在“管理证书”下执行任何操作。 [了解](https://go.microsoft.com/fwlink/?linkid=849300)有关如何在新的 Azure 门户中添加或删除共同管理员 

## <a name="upload-a-management-certificate"></a>上传管理证书
创建管理证书后（仅使用公钥的 .cer 文件），可将其上传到门户。 当该证书在门户中可用时，任何拥有匹配证书（私钥）的人都可通过 Management API 连接并访问与订阅相关联的资源。

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 在底部的 Azure 服务列表中，单击“所有服务”，然后在“常规”服务组中选择“订阅”。

    ![订阅菜单](./media/azure-api-management-certs/subscriptions_menu.png)

3. 请确保选择要与证书关联的正确订阅。     
4. 选择了正确的订阅后，点按“设置”组中的“管理证书”。

    ![设置](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. 点按“上传”按钮。

    ![在“证书”页上传](./media/azure-api-management-certs/certificates_page.png)
6. 填写对话框信息并点按“上传”。

    ![设置](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>后续步骤
将管理证书与订阅关联之后，（在本地安装匹配的证书后）可以编程的方式连接到[经典部署模型 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) 并自动执行各种与该订阅关联的 Azure 资源。
