---
title: 通过 Visual Studio 将 Web 应用发布到 Azure VM
description: 通过 Visual Studio 将 ASP.NET Web 应用程序发布到 Azure 虚拟机
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d864230ff524f6c008f9f05c16505cd9d0e9f229
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583359"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>通过 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM

本文档介绍了如何使用 Visual Studio 2019 中的**Microsoft Azure 虚拟机**发布功能将 ASP.NET web 应用程序发布到 Azure 虚拟机（VM）。  

## <a name="prerequisites"></a>必备条件
若要使用 Visual Studio 将 ASP.NET 项目发布到某个 Azure VM，必须正确设置该 VM。

- 必须将计算机配置为运行 ASP.NET web 应用程序并安装 WebDeploy。 有关详细信息，请参阅[使用 WebDeploy 创建 ASP.NET VM](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md)。

- 必须为 VM 配置 DNS 名称。 有关详细信息，请参阅[在 Azure 门户中创建 Windows VM 的完全限定域名](portal-create-fqdn.md)。

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>使用 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM
以下部分介绍如何将现有的 ASP.NET Web 应用程序发布到 Azure 虚拟机。

1. 在 Visual Studio 2019 中打开自己的 Web 应用解决方案。
2. 在解决方案资源管理器中右键单击该项目，并选择“发布...”。****
3. 使用页面右侧的箭头滚动浏览发布选项，直到出现“Microsoft Azure 虚拟机”。****  

   ![发布页 - 右侧箭头]

4. 选择“Microsoft Azure 虚拟机”图标，然后选择“发布”。********

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

8. 提示输入凭据时，请提供目标 VM 上已配置有发布权限的用户帐户的用户名和密码。 这些凭据通常是创建 VM 时使用的管理员用户名和密码。  

   ![WebDeploy 登录]

9. 接受安全证书。

   ![证书错误]

10. 观察“输出”窗口以检查发布操作的进度。

    ![输出窗口]

11. 如果发布成功，则会启动浏览器并打开新发布的站点的 URL。

**辉煌!**

现已成功将 Web 应用发布到 Azure 虚拟机。

## <a name="publish-page-options"></a>发布页中的选项

完成发布向导后，将在文档中打开“发布”页，其中已选中新的发布配置文件。

### <a name="re-publish"></a>重新发布

若要将更新发布到 Web 应用程序，请在“发布”页上选择“发布”按钮。****  
- 根据提示输入用户名和密码。  
- 随即会开始发布。

![发布页 -“发布”按钮]

### <a name="modify-publish-profile-settings"></a>修改发布配置文件设置

若要查看和修改发布配置文件设置，请选择“设置...”。****  

![发布页-“设置”按钮]

设置应如下所示：  

![发布设置 -“连接”页]

#### <a name="save-user-name-and-password"></a>保存用户名和密码
- 避免在每次发布时提供身份验证信息。 为此，请填写“用户名”**** 和“密码”**** 字段，并选中“保存密码”**** 框。
- 使用“验证连接”按钮确认是否输入了正确的信息。****

#### <a name="deploy-to-clean-web-server"></a>部署到干净的 Web 服务器

- 如果想要确保每次上传后 Web 服务器包含 Web 应用程序的干净副本（不包含先前部署留下的其他文件），可以在“设置”选项卡中选中“删除目标中的其他文件”复选框。********

- 警告：使用此设置进行发布会删除 Web 服务器上存在的所有文件（包括 wwwroot 目录）。 在启用此选项的情况下进行发布之前，请务必了解计算机的状态。 

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
