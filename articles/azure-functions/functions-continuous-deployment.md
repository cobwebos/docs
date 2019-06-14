---
title: Azure Functions 的连续部署 | Microsoft Docs
description: 使用 Azure 应用服务的连续部署功能来发布你的函数。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068386"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署

Azure Functions 可将代码通过持续部署[源代码管理集成](functions-deployment-technologies.md#source-control)。 这样，代码将更新触发器的工作流部署到 Azure。 如果您是初次接触 Azure Functions，开始使用[Azure Functions 概述](functions-overview.md)。

连续部署会很适合项目的选择将集成多个和频繁的发布内容。 它还允许您维护单一的函数代码的事实来源。 可以从以下源的代码位置的 Azure Functions 中配置持续部署：

* [Azure 存储库](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure functions 单位是部署的 function app。 这意味着函数应用中的所有函数都部署在同一时间。 启用连续部署后，在 Azure 门户中的函数代码的访问权限配置为*只读*，因为源设置为其他位置。

## <a name="requirements-for-continuous-deployment"></a>连续部署的要求

为连续部署成功，你的目录结构必须是兼容的 Azure Functions 需要的以下基本文件夹结构：

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>设置连续部署

使用此过程来配置现有 Function App 的连续部署。 这些步骤演示了与 GitHub 存储库的集成，但类似的步骤适用于 Azure 存储库或其他源代码存储库。

1. 在函数应用中[Azure 门户](https://portal.azure.com)，选择**平台功能** > **部署中心**。

    ![打开部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 上**部署中心**，选择**GitHub**有关**源代码管理** > **Authorize**。

    ![部署中心](./media/functions-continuous-deployment/github.png)

3. 选择**授权 AzureAppService** > **继续**。

    ![授权](./media/functions-continuous-deployment/authorize.png)

4. 选择以下生成提供程序之一：

    * **应用服务构建服务**-不需要在生成时，或者需要泛型生成最佳。
    * **Azure 管道 （预览版）** -最佳时需要更好地控制生成。 此提供程序目前处于预览状态。

    ![选择一个生成提供程序](./media/functions-continuous-deployment/build.png)

5. 配置特定于你指定的源控件选项的信息。 Github，必须提供**组织**，**存储库**，并**分支**代码所在的位置。 然后选择“继续”。 

    ![将 GitHub 配置](./media/functions-continuous-deployment/github-specifics.png)

6. 最后，查看所有详细信息，然后选择**完成**完成你的部署配置。

    ![摘要](./media/functions-continuous-deployment/summary.png)

在完成此过程，从指定的源的所有代码会都部署到您的应用程序。 此时，部署源中的更改触发到 Azure 中的函数应用这些更改的部署。

## <a name="deployment-scenarios"></a>部署方案

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>将现有函数移至连续部署

如果您已经编写过函数[Azure 门户](https://portal.azure.com)并且想要切换为连续部署前下载您的应用程序的内容，应导航到**概述**函数的选项卡应用程序，并单击**下载应用内容**按钮。

![下载应用内容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 配置持续集成后，不再可以编辑在 Functions 门户中的源文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳实践](functions-best-practices.md)
