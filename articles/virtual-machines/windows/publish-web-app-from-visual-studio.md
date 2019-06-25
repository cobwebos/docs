---
title: 通过 Visual Studio 将 Web 应用发布到 Azure VM
description: 通过 Visual Studio 将 ASP.NET Web 应用程序发布到 Azure 虚拟机
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 4b8e3ddf1cf5d61f730ce01a35ee0813b47ad2d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66305930"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>通过 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM

本文档介绍了如何发布到 Azure 虚拟机 (VM) 使用 ASP.NET web 应用程序**Microsoft Azure 虚拟机**在 Visual Studio 2019 发布功能。  

## <a name="prerequisites"></a>必备组件
若要使用 Visual Studio 将 ASP.NET 项目发布到某个 Azure VM，必须正确设置该 VM。

- 计算机必须配置为运行 ASP.NET Web 应用程序，并且必须装有 WebDeploy。

- 必须为 VM 配置 DNS 名称。 有关详细信息，请参阅[在 Azure 门户中创建 Windows VM 的完全限定域名](portal-create-fqdn.md)。

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>使用 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM
以下部分介绍如何将现有的 ASP.NET Web 应用程序发布到 Azure 虚拟机。

1. 在 Visual Studio 2019 中打开你的 web 应用解决方案。
2. 在解决方案资源管理器中右键单击该项目，并选择“发布...”。 
3. 使用页面右侧的箭头滚动浏览发布选项，直到出现“Microsoft Azure 虚拟机”。   

   ![发布页 - 右侧箭头]

4. 选择“Microsoft Azure 虚拟机”图标，然后选择“发布”。  

   ![发布页 -“Microsoft Azure 虚拟机”图标]

5. 选择相应的帐户（包含与虚拟机连接的 Azure 订阅）。  
   - 如果已登录 Visual Studio，则帐户列表中会填充所有已经过身份验证的帐户。  
   - 如果未登录，或者所需的帐户未列出，请选择“添加帐户...”并遵照提示登录。  
   ![Azure 帐户选择器]  

6. 从“现有虚拟机”列表中选择相应的 VM。

   > [!Note]
   > 填充此列表可能需要一段时间。

   ![Azure VM 选择器]

7. 单击“确定”开始发布。

8. 当系统提示输入凭据，请提供用户名和密码的目标配置有发布权限的 VM 上的用户帐户。 这些凭据通常是管理员用户名和密码创建 VM 时使用。  

   ![WebDeploy 登录]

9. 接受安全证书。

   ![证书错误]

10. 观察“输出”窗口以检查发布操作的进度。

    ![输出窗口]

11. 如果发布成功，则会启动浏览器并打开新发布的站点的 URL。

**成功！**

现已成功将 Web 应用发布到 Azure 虚拟机。

## <a name="publish-page-options"></a>发布页中的选项

完成发布向导后，将在文档中打开“发布”页，其中已选中新的发布配置文件。

### <a name="re-publish"></a>重新发布

若要将更新发布到 Web 应用程序，请在“发布”页上选择“发布”按钮。   
- 根据提示输入用户名和密码。  
- 随即会开始发布。

![发布页 -“发布”按钮]

### <a name="modify-publish-profile-settings"></a>修改发布配置文件设置

若要查看和修改发布配置文件设置，请选择“设置...”。   

![发布页-“设置”按钮]

设置应如下所示：  

![发布设置 -“连接”页]

#### <a name="save-user-name-and-password"></a>保存用户名和密码
- 避免每次发布时提供身份验证信息。 若要执行此操作，请填充**用户名**并**密码**字段，然后选择**保存密码**框。
- 使用“验证连接”按钮确认是否输入了正确的信息。 

#### <a name="deploy-to-clean-web-server"></a>部署到干净的 Web 服务器

- 如果你想要确保 web 服务器具有全新的 web 应用程序每次上传后，并且，没有其他文件保留在以前的部署，可以检查**删除目标处的其他文件**中的复选框**设置**选项卡。

- 警告：使用此设置进行发布删除 web 服务器 （包括 wwwroot 目录） 存在的所有文件。 在启用此选项的情况下进行发布之前，请务必了解计算机的状态。 

![发布设置 -“设置”页]

## <a name="next-steps"></a>后续步骤

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>设置 CI/CD 以自动部署到 Azure VM

若要使用 Azure Pipelines 设置持续交付管道，请参阅[部署到 Windows 虚拟机](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)。

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[发布页 - 右侧箭头]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[发布页 -“Microsoft Azure 虚拟机”图标]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure 帐户选择器]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM 选择器]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy 登录]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[证书错误]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[输出窗口]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[发布页 -“发布”按钮]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[发布页-“设置”按钮]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[发布设置 -“连接”页]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[发布设置 -“设置”页]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
