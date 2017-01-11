---
title: "Azure 政府版订阅 |Microsoft Docs"
description: "有关管理 Azure 政府版订阅的信息"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>管理和连接 Azure 政府版订阅
Azure 政府版具有用于管理环境的唯一 URL 和终结点。 请务必使用正确的连接通过门户或 PowerShell 管理环境。 连接到 Azure 政府版环境后，如果组件已部署，则管理服务的正常操作将正常运行。

## <a name="connecting-via-the-portal"></a>通过门户连接
门户是大多数人连接 Azure 政府版的主要方式。  若要连接，请通过 [https://portal.azure.us](https://portal.azure.us) 浏览到门户。  可以通过 [ https://manage.windowsazure.us ](https://manage.windowsazure.us) 访问 Azure 门户的旧版本。

可以通过连接到 [ https://account.windowsazure.us ](https://account.windowsazure.us) 为帐户创建订阅。

## <a name="connecting-via-powershell"></a>通过 PowerShell 进行连接
无论是使用 Azure PowerShell 通过脚本来管理大型订阅还是访问 Azure 门户中当前不可用的功能，都需要连接到 Azure 政府版而不是 Azure 公共版。  如果已在 Azure 公共版中使用 PowerShell，它们之间的差别不大。  Azure 政府版的区别如下：

* 连接帐户
* 区域名称

> [!NOTE]
> 如果尚未使用 PowerShell，请查看 [Azure PowerShell 简介](/powershell/azureps-cmdlets-docs)。
> 
> 

当启动 PowerShell 时，需要通过指定环境参数指示 Azure PowerShell 连接到 Azure 政府版。  该参数确保 PowerShell 连接到正确的终结点。  当连接登录到帐户后，会确定终结点的集合。  不同的 API 需要不同版本的环境交换机：

| 连接类型 | 命令 |
| --- | --- |
| [服务管理](https://msdn.microsoft.com/library/dn708504.aspx)命令 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [资源管理](https://msdn.microsoft.com/library/mt125356.aspx)命令 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 命令 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory 命令 v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 命令行](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

当使用 New-AzureStorageContext 连接到存储帐户时，也可使用环境交换机并指定 AzureUSGovernment。

### <a name="determining-region"></a>确定区域
连接后，还有一个区别 - 用于定位服务的区域。  每个 Azure 云具有不同的区域。  可以在服务可用性页面上查看其列表。  通常将位置参数中的区域用于命令。

这里会有个 catch。  Azure 政府版区域需要设置为不同于其公用名的格式：

| 公用名 | 命令 |
| --- | --- |
| 美国政府弗吉尼亚州 |USGov Virginia |
| 美国政府爱荷华州 |USGov Iowa |

> [!NOTE]
> 使用位置参数时，“US”和“Gov”之间没有空格。
> 
> 

如果想要验证 Azure 政府版中可用区域，可以运行以下命令，并打印当前列表：

    Get-AzureLocation

如果对 Azure 上的可用环境感兴趣，可以运行：

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>通过 Visual Studio 连接
开发人员通过使用 Visual Studio 可以轻松管理 Azure订阅，并构建解决方案。  Visual Studio 当前不允许在用户界面中配置与 Azure 政府版的连接。  

### <a name="updating-visual-studio-for-azure-government"></a>更新适用于 Azure 政府版的 Visual Studio
若要启用 Visual Studio 以连接到 Azure 政府版，需要更新注册表。

1. 关闭 Visual Studio
2. 创建名为 **VisualStudioForAzureGov.reg** 的文本文件
3. 将以下文本复制并粘贴到 **VisualStudioForAzureGov.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. 保存然后通过双击运行该文件。  系统会提示将文件合并到注册表。
5. 启动 Visual Studio 并开始使用 [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> 设置此注册表项后，只可以访问 Azure 政府版订阅。  虽然仍可以看到以前配置的订阅，但这些订阅不可用，因为 Visual Studio 现已连接到 Azure 政府版，而不是 Azure 公共版。  请参阅以下部分的步骤来还原所做的更改。
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>将 Visual Studio 连接还原到 Azure 政府版
若要启用 Visual Studio 来连接 Azure 公共版，需要删除启用 Azure 政府版连接的注册表设置。

1. 关闭 Visual Studio
2. 创建名为 **VisualStudioForAzureGov_Remove.reg** 的文本文件
3. 将以下文本复制并粘贴到 **VisualStudioForAzureGov_Remove.reg**：
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. 保存然后通过双击运行该文件。  系统会提示将文件合并到注册表。
5. 启动 Visual Studio

> [!NOTE]
> 还原此注册表项后，会显示 Azure 政府版订阅，但不可访问此订阅。  可以安全地将其删除。
> 
> 

## <a name="next-steps"></a>后续步骤
如需详细信息，可以查看：

* [GitHub 上的 PowerShell 文档](https://github.com/Azure/azure-powershell)
* [有关连接到资源管理的分步说明](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
* [MSDN 上的 Azure PowerShell 文档](https://msdn.microsoft.com/library/mt619274.aspx)

有关补充信息和更新，请订阅 [Microsoft Azure 政府版博客](https://blogs.msdn.microsoft.com/azuregov/)




<!--HONumber=Dec16_HO2-->


