---
title: Azure Stack 上的应用服务配置部署源 |Microsoft Docs
description: 服务管理员如何为 Azure Stack 上的应用服务配置部署源 （Git、 GitHub、 BitBucket、 DropBox 和 OneDrive）
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: d65c8653bc039b591f1c0fb711dfe68e3fbacd88
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353552"
---
# <a name="configure-deployment-sources"></a>配置部署源

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 上的应用服务支持从多个源代码管理提供程序的按需部署。 应用程序开发人员可以使用此功能直接从其源代码管理存储库部署。 如果用户想要配置应用服务以连接到其存储库，云操作员必须先配置 Azure Stack 上的应用服务和源代码管理提供程序之间的集成。  

除了本地 Git，支持以下源代码管理提供程序：

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>在应用服务管理中查看部署源

1. 以服务管理员身份登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external))。
2. 浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。![应用服务资源提供程序管理][1]
3. 单击“源代码管理配置”。 您可以查看所有配置的部署源的列表。
    ![应用服务资源提供程序管理中的源代码管理配置][2]

## <a name="configure-github"></a>配置 GitHub

必须具有 GitHub 帐户才能完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 登录到 GitHub，浏览到 https://www.github.com/settings/developers，然后单击**注册新应用程序**。
    ![GitHub - 注册新应用程序][3]
2. 输入**应用程序名称**; 例如， **Azure Stack 上的应用服务**。
3. 输入“主页 URL”。 主页 URL 必须是 Azure Stack 门户地址。 例如， https://portal.local.azurestack.external。
4. 输入**应用程序说明**。
5. 输入“授权回调 URL”。 在默认的 Azure Stack 部署，该 URL 将采用在窗体 https://portal.local.azurestack.external/TokenAuthorize。 如果在不同的域下运行，替换你的域名为 local.azurestack.external。
6. 单击“注册应用程序”。 会显示一个页面，其中列出**客户端 ID**并**客户端机密**应用程序。
    ![GitHub - 已完成应用程序注册][5]
7.  在新浏览器选项卡或窗口中，登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)作为服务管理员。
8.  浏览到**资源提供程序**，然后选择**应用服务资源提供程序管理**。
9. 单击“源代码管理配置”。
10. 将“客户端 ID”和“客户端机密”复制并粘贴到 GitHub 的相应输入框中。
11. 单击“ **保存**”。

## <a name="configure-bitbucket"></a>配置 BitBucket

必须具有 BitBucket 帐户才能完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 登录到 BitBucket 并浏览到帐户下面的“集成”。
    ![BitBucket 仪表板 - 集成][7]
2. 单击“访问管理”下面的“OAuth”，并单击“添加使用者”。
    ![BitBucket - 添加 OAuth 使用者][8]
3. 输入**名称**使用者; 例如， **Azure Stack 上的应用服务**。
4. 输入应用程序的**说明**。
5. 输入“回调 URL”。 在默认 Azure Stack 部署中，回调 URL 是在窗体 https://portal.local.azurestack.external/TokenAuthorize。 如果在不同的域下运行，替换请将 azurestack.local 你域的名称。 若要成功完成 BitBucket 集成，该 URL 必须遵循此处列出的大小写。
6. 输入**URL**。 此 URL 应是 Azure Stack 门户 URL;例如， https://portal.local.azurestack.external。
7. 选择所需**权限**：
    - **存储库**：*读取*
    - **Webhook**：*读取和写入*
8. 单击“ **保存**”。 您现在看到此新的应用程序，连同**键**并**机密**下**OAuth 使用者**。
    ![BitBucket 应用程序列表][9]
9.  在新浏览器选项卡或窗口中，登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)作为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将“密钥”和“机密”分别复制并粘贴到 BitBucket 的“客户端 ID”和“客户端机密”输入框。
13. 单击“ **保存**”。

## <a name="configure-onedrive"></a>配置 OneDrive

必须将一个 Microsoft 帐户链接到 OneDrive 帐户才能完成此任务。  建议使用组织帐户，而不是个人帐户。

> [!NOTE]
> 当前不支持 OneDrive for Business 帐户。

1. 浏览到 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm并使用你的 Microsoft 帐户登录。
2. 在“我的应用程序”下，单击“添加应用”。
![OneDrive 应用程序][10]
3. 输入**名称**的新应用程序注册： 输入**Azure Stack 上的应用服务**，然后单击**创建应用程序**
4. 以下屏幕列出了新应用程序的属性。 保存**应用程序 ID**到某个临时位置。
![OneDrive 应用程序属性][11]
5. 在“应用程序密码”下，单击“生成新密码”。 请记下的**生成的新密码**。 这是你的应用程序密码，则在单击后无法检索**确定**。
6. 下**平台**，单击**添加平台**，然后选择**Web**。
7. 输入“重定向 URI”。 在默认 Azure Stack 部署中，重定向 URI 采用形式 https://portal.local.azurestack.external/TokenAuthorize。 如果在不同的域下运行，替换请将 azurestack.local 你域的名称。
![OneDrive 应用程序-添加 Web 平台][12]
8. 添加“Microsoft Graph 权限 - 委派权限”
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive 应用程序 - Graph 权限][13]
9. 单击“ **保存**”。
10.  在新浏览器选项卡或窗口中，登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)作为服务管理员。
11.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
12. 单击“源代码管理配置”。
13. 将“应用程序 ID”和“密码”分别复制并粘贴到 OneDrive 的“客户端 ID”和“客户端机密”输入框。
14. 单击“ **保存**”。

## <a name="configure-dropbox"></a>配置 DropBox

> [!NOTE]
> 必须具有一个 DropBox 帐户来完成此任务。 建议使用组织帐户，而不是个人帐户。

1. 浏览到 https://www.dropbox.com/developers/apps并使用你的 DropBox 帐户凭据登录。
2. 单击“创建应用”。

    ![Dropbox 应用程序][14]

3. 选择“DropBox API”。
4. 将访问级别设置为“应用文件夹”。
5. 输入应用程序的**名称**。
![Dropbox 应用程序注册][15]
6. 单击“创建应用”。 此时会显示列出的应用程序中，设置页包括**应用密钥**并**应用程序密码**。
7. 请确保**应用程序文件夹名称**设置为**Azure Stack 上的应用服务**。
8. 设置**OAuth 2 重定向 URI** ，然后单击**添加**。 在默认 Azure Stack 部署中，重定向 URI 采用形式 https://portal.local.azurestack.external/TokenAuthorize。 如果在不同的域下运行，请替换你的域。 请将 azurestack.local。
![Dropbox 应用程序配置][16]
9.  在新浏览器选项卡或窗口中，登录到 Azure Stack 管理员门户 (https://adminportal.local.azurestack.external)作为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将“应用程序密钥”和“应用机密”分别复制并粘贴到 DropBox 的“客户端 ID”和“客户端机密”输入框。
13. 单击“ **保存**”。

## <a name="next-steps"></a>后续步骤

用户现在可以使用部署源进行[持续部署](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment)、[本地 Git 部署](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git)和[云文件夹同步](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync)等操作。

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
