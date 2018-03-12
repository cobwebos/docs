---
title: "更新 Azure App Service 脱机 |Microsoft 文档"
description: "脱机更新 Azure 堆栈上的 Azure App Service 的详细的指南"
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
ms.openlocfilehash: 61a3169229cc121c078a934f6b979bdaffafd565
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>脱机 Azure 堆栈上的 Azure 应用服务更新

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!IMPORTANT]
> 将 1802年更新应用于你的 Azure 堆栈集成系统，或在部署 Azure App Service 之前部署的最新的 Azure 堆栈开发工具包。
>
>

按照这篇文章中的说明进行操作，你可以升级[App Service 资源提供程序](azure-stack-app-service-overview.md)是 Azure 堆栈环境中部署：

* 未连接到 Internet
* 受 Active Directory 联合身份验证服务 (AD FS) 保护。

> [!IMPORTANT]
> 在运行升级之前, 请确保你已经完成[Azure 堆栈资源提供程序上的 Azure 应用程序服务的部署](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

若要升级的 Azure 堆栈环境中的 App Service 资源提供程序，你必须完成这些任务：

1. 下载[App Service 安装程序](https://aka.ms/appsvcupdate1installer)
2. 创建脱机升级包。
3. 运行应用程序服务安装程序 (appservice.exe) 并完成升级。

在此过程中，将升级：

* 检测以前部署的应用程序服务
* 将上载到存储
* 升级 App Service 的所有角色 (控制器、 管理、 前端、 发布者和辅助角色)
* 更新 App Service 缩放集定义
* 更新 App Service 资源提供程序清单

## <a name="create-an-offline-upgrade-package"></a>创建脱机升级包

若要升级在连接断开的环境的 App Service，必须首先在连接到 Internet 的计算机上创建脱机升级包。

1. 以管理员身份运行 appservice.exe

    ![应用服务安装程序][1]

2. 单击**高级** > **创建脱机包**

    ![高级应用程序服务安装程序][2]

3. App Service 安装程序创建脱机升级包，并显示的路径。  可以单击“打开文件夹”，在文件资源管理器中打开该文件夹。

4. 将安装程序 (AppService.exe) 和脱机的升级包复制到 Azure 堆栈主机上。

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>完成 Azure 堆栈上的 App Service 的升级

> [!IMPORTANT]
> 必须可以访问 Azure 堆栈管理员 Azure 资源管理器终结点的计算机上运行应用程序服务安装程序。
>
>

1. 以管理员身份运行 appservice.exe。  

    ![应用服务安装程序][1]

2. 单击**高级** > **完成脱机安装或升级**。

    ![高级应用程序服务安装程序][2]

3. 浏览到以前创建的脱机升级包的位置，然后单击**下一步**。

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”。

5. 查看并接受第三方许可条款，然后单击“下一步”。

6. 请确保 Azure 堆栈 Azure 资源管理器终结点和 Active Directory 租户信息是否正确。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以接受此处的默认值。 但是，如果在部署 Azure Stack 时自定义了选项，则必须根据自定义的情况编辑此窗口中的值。 例如，如果你使用的域后缀*mycloud.com*，必须将你的 Azure 堆栈 Azure 资源管理器终结点更改为*management.region.mycloud.com*。确认信息后，单击“下一步”。

    ![Azure 堆栈云信息][3]

7. 在下一页上执行以下操作：

   1. 单击“Azure Stack 订阅”框旁边的“连接”按钮。
        * 如果使用 Azure Active Directory (Azure AD)，请输入在部署 Azure Stack 时提供的 Azure AD 管理员帐户和密码。 单击**登录**。
        * 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，*cloudadmin@azurestack.local*。 输入密码，并单击“登录”。
   2. 在“Azure Stack 订阅”框中，选择订阅。
   3. 在“Azure Stack 位置”框中，选择要部署到的区域所对应的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。
   4. 如果发现了现有 App Service 部署，然后的资源组和存储帐户将会填充而灰显状态。
   5. 单击**下一步**以查看升级摘要。

    ![检测到的应用程序服务安装][4]

8. 在摘要页上执行以下操作：
   1. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页面。
   2. 如果配置正确，则选中此复选框。
   3. 若要开始升级，请单击**下一步**。

       ![App Service 升级摘要][5]

9. 升级进度页：
    1. 跟踪升级的进度。 升级 Azure 堆栈上的 App Service 的持续时间随部署的角色实例数。
    2. 升级成功完成后，单击**退出**。

        ![App Service 升级进度][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)。

* [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
* [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
