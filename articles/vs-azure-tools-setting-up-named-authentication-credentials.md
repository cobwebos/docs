---
title: "设置命名的身份验证凭据 | Microsoft Docs"
description: "了解如何提供 Visual Studio 可用于验证对 Azure 的请求的凭据，以便从 Visual Studio 将应用程序发布到 Azure 或者监视现有云服务。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="set-up-named-authentication-credentials"></a>设置命名的身份验证凭据
要从 Visual Studio 中将应用程序发布到 Azure，或是要监视现有云服务，必须提供 Visual Studio 可用于验证对 Azure 的请求的凭据。 Visual Studio 中有几个位置可供你登录来提供这些凭据。 例如，从服务器资源管理器，可以打开“Azure”节点的快捷菜单，并选择“连接到 Microsoft Azure 订阅”。 登录时，Visual Studio 中会提供与 Azure 帐户关联的订阅信息。 用户无需执行任何操作。

Azure Tools 还支持以前提供凭据的方式，即使用订阅文件（.publishsettings 文件）提供。 本文介绍了此方法，它在 Azure SDK 2.2 中仍受支持。

向 Azure 进行身份验证需要以下项：

* 订阅 ID
* 有效的 X.509 v3 证书

> [!NOTE]
> X.509 v3 证书的密钥长度必须至少为 2,048 位。 Azure 拒绝所有不符合此要求或无效的证书。
>
>

Visual Studio 使用订阅 ID 和证书数据作为凭据。 订阅文件（.publishsettings 文件）中引用了适当的凭据，该文件包含证书的公共密钥。 该订阅文件可以包含多个订阅的凭据。

从“新建订阅”或“编辑订阅”对话框中，可以编辑订阅信息，如本文下文中所述。

如果要自行创建证书，可以参阅[创建并上传 Azure 管理证书](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)中的说明，然后将证书手动上传到 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)。

> [!NOTE]
> Visual Studio 管理云服务所需的凭据不同于针对 Azure 存储服务验证请求所需的凭据。
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>在 Visual Studio 中导入、设置或编辑身份验证凭据

1. 在服务器资源管理器中，打开“Azure”节点的快捷菜单，并选择“管理和筛选订阅”。
2. 选择“证书”选项卡，然后使用以下任一方法：

    * 选择“导入”，打开“导入 Microsoft Azure 订阅”对话框。 可在其中下载当前已加载订阅的订阅文件，浏览到其下载位置并将其导入，用于进行身份验证。
    * 选择“新建”，打开“新建订阅”对话框。 可在其中设置用于身份验证的新订阅。
    * 选择活动订阅后，选择“编辑”，打开“编辑订阅”对话框。 可在其中编辑用于身份验证的现有订阅。 

以下过程假设“新建订阅”对话框已打开。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>在 Visual Studio 中设置身份验证凭据
1. 在“选择用于身份验证的现有证书”列表中选择一个证书。
2. 选择“复制完整路径”链接。 证书（.cer 文件）的路径将复制到剪贴板。

   > [!IMPORTANT]
   > 要从 Visual Studio 中发布 Azure 应用程序，必须将此证书上传到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
   >
   >
3. 将证书上传到 Azure 门户：

   a. 打开 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
   
   b. 如果系统提示，请登录到门户，然后浏览到“设置” > “管理证书”。
   
   c. 在“管理证书”窗格中，选择“上传”。
   
   d.单击“下一步”。 选择 Azure 订阅，粘贴刚刚创建的 .cer 文件的完整路径，并选择“上传”。

## <a name="next-steps"></a>后续步骤
* [Web 应用一般概述](https://docs.microsoft.com/azure/app-service/)
* [将应用部署到 Azure 应用服务](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [使用 Visual Studio 部署 WebJobs](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [创建和部署云服务](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
