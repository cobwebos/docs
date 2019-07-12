---
title: Azure Functions 的连续部署 | Microsoft Docs
description: 使用 Azure 应用服务的连续部署功能发布你的函数。
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594543"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署

可以使用 Azure Functions 通过连续部署代码[源代码管理集成](functions-deployment-technologies.md#source-control)。 源代码管理集成可以实现代码更新触发 Azure 上部署的工作流。 如果您是初次接触 Azure Functions，开始通过查看[Azure Functions 概述](functions-overview.md)。

持续部署是项目的一个不错的选择将多个集成和频繁的发布内容。 当使用连续部署时，您可以为你的代码，使团队可以轻松地进行协作维护单一事实来源。 可以从以下源的代码位置的 Azure Functions 中配置持续部署：

* [Azure 存储库](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Functions 在 Azure 中单位是部署的 function app。 在同一时间部署函数应用中的所有函数。 启用连续部署后，在 Azure 门户中的函数代码的访问权限配置为*只读*因为真相的来源设置为其他位置。

## <a name="requirements-for-continuous-deployment"></a>连续部署的要求

为连续部署成功，你的目录结构必须与 Azure Functions 需要的基本文件夹结构兼容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>设置连续部署

若要配置一个现有 function app 的连续部署，完成这些步骤。 步骤演示了与 GitHub 存储库的集成，但类似的步骤适用于 Azure 存储库或其他源代码存储库。

1. 在函数应用中[Azure 门户](https://portal.azure.com)，选择**平台功能** > **部署中心**。

    ![打开部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 在中**部署中心**，选择**GitHub**，然后选择**Authorize**。 如果您已经授权 GitHub，请选择**继续**。 

    ![Azure 应用服务部署中心](./media/functions-continuous-deployment/github.png)

3. 在 GitHub 中，选择**授权 AzureAppService**按钮。 

    ![授权 Azure 应用服务](./media/functions-continuous-deployment/authorize.png)
    
    在中**部署中心**在 Azure 门户中，选择**继续**。

4. 选择以下生成提供程序之一：

    * **应用服务构建服务**:当不需要在生成时，或者需要泛型生成最佳。
    * **Azure 管道 （预览版）** :你需要加强控制在生成时的最佳。 此提供程序目前处于预览状态。

    ![选择一个生成提供程序](./media/functions-continuous-deployment/build.png)

5. 配置特定于你指定的源控件选项的信息。 Github，必须输入或选择值**组织**，**存储库**，并**分支**。 值基于代码的位置。 然后选择“继续”。 

    ![配置 GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. 查看所有详细信息，并选择**完成**完成你的部署配置。

    ![总结](./media/functions-continuous-deployment/summary.png)

完成该过程后，从指定的源的所有代码都部署到您的应用程序。 此时，部署源中的更改触发到 Azure 中的函数应用这些更改的部署。

## <a name="deployment-scenarios"></a>部署方案

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>将现有函数移至连续部署

如果您已经编写过函数[Azure 门户](https://portal.azure.com)并且你想要下载您的应用程序的内容，然后切换到连续部署，请转到**概述**函数应用的选项卡。 选择**下载应用内容**按钮。

![下载应用内容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 配置持续集成后，不再可以编辑在 Functions 门户中的源文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳实践](functions-best-practices.md)
