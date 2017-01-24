---
title: "上传 Azure Management API 证书 | Microsoft Docs"
description: "了解如何为 Azure 经典门户上载 Management API 证书。"
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
ms.date: 04/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 783390791f27a9bf8ea400403a8b0f866f8c5cee
ms.openlocfilehash: 0f1ae486340270cac5cfdb5d44485d05cb996388


---
# <a name="upload-an-azure-management-api-management-certificate"></a>上载 Azure Management API 管理证书
管理证书允许你使用 Azure 提供的服务管理 API 进行身份验证。 许多程序和工具（如 Visual Studio 或 Azure SDK）将使用这些证书来自动配置和部署各种 Azure 服务。 **这仅适用于 Azure 经典门户**。

> [!WARNING]
> 请小心！ 这些类型的证书允许任何使用它们进行身份验证的人管理与它们相关联的订阅。
>
>

有关 Azure 证书（包括创建自签名证书）的详细信息可在你需要时向你[提供](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)。

还可以使用 [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) 对客户端代码进行身份验证，以用于自动化目的。

## <a name="upload-a-management-certificate"></a>上载管理证书
一旦您创建了一个管理证书（仅使用公开密钥的 .cer 文件），您可以将其上载到门户。 当该证书在门户中可用时，任何拥有匹配证书（私钥）的人可以通过 Management API 连接并访问与订阅相关联的资源。

1. 登录到 [Azure 经典门户](http://manage.windowsazure.com)。
2. 请确保选择想要关联证书的正确订阅。 在门户的右上方，点按“订阅”。

    ![设置](./media/azure-api-management-certs/subscription.png)
3. 选择正确订阅后，在门户的左侧（可能需要向下滚动），点按“设置”。

    ![设置](./media/azure-api-management-certs/settings.png)
4. 点按“管理证书”选项卡。

    ![设置](./media/azure-api-management-certs/certificates-tab.png)
5. 点按“上传”按钮。

    ![设置](./media/azure-api-management-certs/upload.png)
6. 填写对话框信息并点按完成“复选标记”。

    ![设置](./media/azure-api-management-certs/upload-dialog.png)

## <a name="next-steps"></a>后续步骤
现在你已将管理证书与订阅关联，（在本地安装匹配的证书后）可以编程的方式连接到[服务管理 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) 并自动执行各种与该订阅关联的 Azure 资源。



<!--HONumber=Dec16_HO1-->


