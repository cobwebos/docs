---
title: "更新 Azure 堆栈上的 Azure App Service |Microsoft 文档"
description: "更新 Azure 堆栈上的 Azure App Service 的详细的指南"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 151456bbb7f9331730e640e4bece3872c3c92f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="update-azure-app-service-on-azure-stack"></a>更新 Azure 堆栈上的 Azure App Service

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!IMPORTANT]
> 将 1802年更新应用于你的 Azure 堆栈集成系统，或在部署 Azure App Service 之前部署的最新的 Azure 堆栈开发工具包。
>
>

按照这篇文章中的说明进行操作，你可以升级[App Service 资源提供程序](azure-stack-app-service-overview.md)连接到 Internet 的 Azure 堆栈环境中部署。

> [!IMPORTANT]
> 在运行升级之前, 请确保你已经完成[Azure 堆栈资源提供程序上的 Azure 应用程序服务的部署](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

在此过程中，将升级：

* 检测以前部署的应用程序服务
* 准备所有更新包和所有 OSS 库要部署的新版本
* 将上载到存储
* 升级 App Service 的所有角色 (控制器、 管理、 前端、 发布者和辅助角色)
* 更新 App Service 缩放集定义
* 更新 App Service 资源提供程序清单

> [!IMPORTANT]
> 必须可以访问 Azure 堆栈管理员 Azure 资源管理器终结点的计算机上运行应用程序服务安装程序。
>
>

若要升级你的部署的 Azure 堆栈上的 App Service，请按照下列步骤：

1. 下载[App Service 安装程序](https://aka.ms/appsvcupdate1installer)

2. 以管理员身份运行 appservice.exe

    ![应用服务安装程序][1]

3. 单击**部署 App Service 或升级到最新版本。**

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”。

5. 查看并接受第三方许可条款，然后单击“下一步”。

6. 请确保 Azure 堆栈 Azure 资源管理器终结点和 Active Directory 租户信息是否正确。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以接受此处的默认值。 但是，如果在部署 Azure Stack 时自定义了选项，则必须根据自定义的情况编辑此窗口中的值。 例如，如果你使用的域后缀*mycloud.com*，必须将你的 Azure 堆栈 Azure 资源管理器终结点更改为*management.region.mycloud.com*。确认信息后，单击“下一步”。

    ![Azure 堆栈云信息][2]

7. 在下一页上执行以下操作：

   1. 单击“Azure Stack 订阅”框旁边的“连接”按钮。
        * 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack 时提供的 Azure AD 管理员帐户和密码。 单击**登录**。
        * 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，*cloudadmin@azurestack.local*。 输入密码，并单击“登录”。
   2. 在“Azure Stack 订阅”框中，选择订阅。
   3. 在“Azure Stack 位置”框中，选择要部署到的区域所对应的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。
   4. 如果发现了现有 App Service 部署，然后的资源组和存储帐户将会填充而灰显状态。
   5. 单击**下一步**以查看升级摘要。

    ![检测到的应用程序服务安装][3]

8. 在摘要页上执行以下操作：
   1. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页面。
   2. 如果配置正确，则选中此复选框。
   3. 若要开始升级，请单击**下一步**。

       ![App Service 升级摘要][4]

9. 升级进度页：
    1. 跟踪升级的进度。 升级 Azure 堆栈上的 App Service 的持续时间随部署的角色实例数。
    2. 升级成功完成后，单击**退出**。

        ![App Service 升级进度][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
