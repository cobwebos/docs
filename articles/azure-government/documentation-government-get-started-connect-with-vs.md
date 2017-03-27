---
title: "使用 Visual Studio 连接到 Azure 政府 | Microsoft Docs"
description: "有关通过使用 Visual Studio 连接在 Azure 政府中管理订阅的信息"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>通过 Visual Studio 连接
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
> 设置此注册表项后，只可以访问 Azure 政府订阅。  虽然仍可以看到以前配置的订阅，但这些订阅不可用，因为 Visual Studio 现已连接到 Azure 政府版，而不是 Azure 公共版。  请参阅以下部分的步骤来还原所做的更改。
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
> 还原此注册表项后，会显示 Azure 政府订阅，但不可访问此订阅。  可以安全地将其删除。
> 
> 

