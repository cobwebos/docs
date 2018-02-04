---
title: "为 Azure Stack 上的应用服务配置部署源 | Microsoft 文档"
description: "服务管理员如何为 Azure Stack 上的应用服务配置部署源（Git、GitHub、BitBucket、DropBox 和 OneDrive）"
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
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: e73a607a7f8ad948ab821b0aeda164418509f076
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="configure-deployment-sources"></a>配置部署源
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*


Azure Stack 上的应用服务支持从多个源代码管理提供程序执行按需部署。 此功能允许应用程序开发人员从其源控件存储库部署直接。 如果用户想要配置 App Service，连接到其存储库，云操作员必须首先配置 Azure 堆栈上的 App Service 和源代码管理提供程序之间的集成。  

除了本地 Git 以外，支持的源代码管理提供程序还有：

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>视图在应用程序服务管理中的部署源

1. 服务管理员身份登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external)。
2. 浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。![应用服务资源提供程序管理][1]
3. 单击“源代码管理配置”。  在此处可以看到所有已配置的部署源的列表。
    ![应用服务资源提供程序管理中的源代码管理配置][2]

## <a name="configure-github"></a>配置 GitHub

你必须具有 GitHub 帐户来完成此任务。 你可能想要为你的组织，而不是个人帐户使用的帐户。

1. 登录到 GitHub 中，浏览到 https://www.github.com/settings/developers 并单击**注册新应用程序**。
    ![GitHub-注册新的应用程序][3]
2. 输入**应用程序名称**例如-Azure 堆栈上的 App Service。
3. 输入“主页 URL”。 主页 URL 必须是 Azure 堆栈门户地址。 例如，https://portal.local.azurestack.external。
4. 输入**应用程序说明**。
5. 输入“授权回调 URL”。  在默认 Azure 堆栈部署中，该 Url 采用窗体 https://portal.local.azurestack.external/tokenauthorize，如果你使用的运行不同的域替代在域中的查找 local.azurestack.external ![GitHub-注册一个新使用值填充的应用程序][4]
6. 单击“注册应用程序”。  此时会出现一个页面，其中列出了应用程序的“客户端 ID”和“客户端机密”。
    ![GitHub - 已完成应用程序注册][5]
7.  在新浏览器选项卡或窗口中登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) 作为服务管理员。
8.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
9. 单击“源代码管理配置”。
10. 将“客户端 ID”和“客户端机密”复制并粘贴到 GitHub 的相应输入框中。
11. 单击“ **保存**”。

## <a name="configure-bitbucket"></a>配置 BitBucket

您必须拥有 BitBucket 帐户才能完成此任务。 你可能想要为你的组织，而不是个人帐户使用的帐户。

1. 登录到 BitBucket 并浏览到**集成**你的帐户。
    ![BitBucket 仪表板-集成][7]
2. 单击**OAuth**下访问管理和**添加使用者**。
    ![BitBucket 添加 OAuth 使用者][8]
3. 输入**名称**的使用者，例如 Azure 堆栈上的 App Service。
4. 输入**说明**应用程序。
5. 输入“回调 URL”。  默认 Azure 堆栈部署，在中，回调 Url 是位于窗体 https://portal.local.azurestack.external/TokenAuthorize，如果你使用的运行不同的域替代在域中的查找 azurestack.local。  该 URL 的大小写必须与此处所列相同，这样才能成功完成 BitBucket 集成。
6. 输入**URL** -此 Url 应为 Azure 堆栈门户 URL，例如 https://portal.local.azurestack.external。
7. 选择**权限**需要：
    - **存储库**:*读取*
    - **Webhook**:*读取和写入*
8. 单击“ **保存**”。  现在，“OAuth 使用者”下面会显示此新应用程序以及**密钥**和**机密**。
    ![BitBucket 应用程序列表][9]
9.  在新浏览器选项卡或窗口中登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) 作为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将“密钥”和“机密”分别复制并粘贴到 BitBucket 的“客户端 ID”和“客户端机密”输入框。
13. 单击“ **保存**”。


## <a name="configure-onedrive"></a>配置 OneDrive

你必须具有 Microsoft 帐户链接到 OneDrive 帐户来完成此任务。  你可能想要为你的组织，而不是个人帐户使用的帐户。

> [!NOTE]
> 目前不支持 OneDrive for Business 帐户。

1. 浏览到 https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm，并使用 Microsoft 帐户登录。
2. 下**我的应用程序**，单击**添加应用程序**。
![OneDrive 应用程序][10]
3. 输入新应用程序注册的**名称**，输入“Azure Stack 上的应用服务”，并单击“创建应用程序”
4. 以下屏幕列出了新应用程序的属性。 记录**应用程序 Id**。![OneDrive 应用程序属性][11]
5. 下**应用程序机密**，单击**生成新密码**。 记下**生成的新密码**。 这是你的应用程序密码，则在单击后不可检索**确定**在此阶段。
6. 下**平台**单击**添加平台**和选择**Web**。
7. 输入“重定向 URI”。  在默认 Azure 堆栈部署中，重定向 URI 是在窗体 https://portal.local.azurestack.external/tokenauthorize，如果你使用的运行不同的域替代在域中的查找 azurestack.local ![OneDrive 应用程序-添加Web 平台][12]
8. 添加**Microsoft Graph 权限** - **委派权限**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive 应用程序 - Graph 权限][13]
9. 单击“ **保存**”。
10.  在新浏览器选项卡或窗口中登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) 作为服务管理员。
11.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
12. 单击“源代码管理配置”。
13. 将“应用程序 ID”和“密码”分别复制并粘贴到 OneDrive 的“客户端 ID”和“客户端机密”输入框。
14. 单击“ **保存**”。

## <a name="configure-dropbox"></a>配置 DropBox

> [!NOTE]
> 需要使用一个 DropBox 帐户来完成此任务。  可能需要使用组织的帐户，而不是个人帐户。

1. 浏览到 https://www.dropbox.com/developers/apps 和日志中使用你的 DropBox 帐户。
2. 单击**创建应用**。

    ![Dropbox 应用程序][14]

3. 选择**DropBox API**。
4. 设置的访问级别**应用文件夹**。
5. 输入应用程序的**名称**。
![Dropbox 应用程序注册][15]
6. 单击“创建应用”。  此时会出现一个页面，其中列出了应用的设置，包括**应用密钥**和**应用机密**。
7. 检查**应用程序文件夹名称**设置为**Azure 堆栈上的 App Service**。
8. 设置“OAuth 2 重定向 URI”，并单击“添加”。  在默认 Azure 堆栈部署中，重定向 URI 是在窗体 https://portal.local.azurestack.external/tokenauthorize，如果你使用的运行不同的域替代在域中的查找 azurestack.local。
![Dropbox 应用程序配置][16]
9.  在新浏览器选项卡或窗口中登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external) 作为服务管理员。
10.  浏览到“资源提供程序”并选择“应用服务资源提供程序管理”。
11. 单击“源代码管理配置”。
12. 将“应用程序密钥”和“应用机密”分别复制并粘贴到 DropBox 的“客户端 ID”和“客户端机密”输入框。
13. 单击“ **保存**”。


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

## <a name="next-steps"></a>后续步骤

用户现在可以使用的部署源如下事物[连续部署](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment)，[本地 Git 部署](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git)，和[云文件夹同步](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync)。
