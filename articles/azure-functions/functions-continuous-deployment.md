---
title: Azure Functions 的连续部署
description: 使用 Azure 应用服务的连续部署功能发布函数。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277019"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署

可以使用 Azure 函数使用[源代码管理集成](functions-deployment-technologies.md#source-control)连续部署代码。 源代码管理集成支持一个工作流，其中代码更新将触发部署到 Azure。 如果您是 Azure 函数的新功能，则通过查看[Azure 函数概述](functions-overview.md)开始。

对于集成多个频繁贡献的项目而言，持续部署是一个不错的选择。 使用连续部署时，您可以为代码维护一个真实来源，从而允许团队轻松协作。 可以从以下源代码位置配置 Azure 函数中的连续部署：

* [Azure 存储库](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [比特桶](https://bitbucket.org/)

Azure 中函数的部署单元是函数应用。 同时部署函数应用中的所有函数。 启用连续部署后，对 Azure 门户中的函数代码的访问配置为*只读，* 因为真相源设置为位于其他位置。

## <a name="requirements-for-continuous-deployment"></a>持续部署的要求

要使连续部署成功，目录结构必须与 Azure 函数期望的基本文件夹结构兼容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 在消费计划上运行的 Linux 应用尚不支持持续部署。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>设置连续部署

要配置现有功能应用的连续部署，请完成这些步骤。 这些步骤演示了与 GitHub 存储库的集成，但类似的步骤适用于 Azure 存储库或其他源代码存储库。

1. 在[Azure 门户](https://portal.azure.com)中的函数应用中，选择**平台功能** > **部署中心**。

    ![开放式部署中心](./media/functions-continuous-deployment/platform-features.png)

2. 在**部署中心**中，选择**GitHub，** 然后选择 **"授权**"。 如果您已授权 GitHub，请选择"**继续**"。 

    ![Azure 应用服务部署中心](./media/functions-continuous-deployment/github.png)

3. 在 GitHub 中，选择 **"授权 AzureApp 服务"** 按钮。 

    ![授权 Azure 应用服务](./media/functions-continuous-deployment/authorize.png)
    
    在 Azure 门户中的**部署中心**中，选择 **"继续**"。

4. 选择以下生成提供程序之一：

    * **应用服务生成服务**：当您不需要生成或需要通用生成时，最佳。
    * **Azure 管道（预览）：** 当您需要对生成进行更多控制时，最佳。 此提供程序当前处于预览状态。

    ![选择生成提供程序](./media/functions-continuous-deployment/build.png)

5. 配置特定于您指定的源代码管理选项的信息。 对于 GitHub，必须输入或选择**组织**、**存储库**和**分支**的值。 这些值基于代码的位置。 然后选择“继续”。****

    ![配置 GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. 查看所有详细信息，然后选择 **"完成"** 以完成部署配置。

    ![总结](./media/functions-continuous-deployment/summary.png)

该过程完成后，指定源中的所有代码将部署到你的应用。 此时，部署源中的更改将触发这些更改部署到 Azure 中的函数应用。

## <a name="deployment-scenarios"></a>部署方案

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>将现有函数移至连续部署

如果在[Azure 门户](https://portal.azure.com)中已写入函数，并且希望在切换到连续部署之前下载应用的内容，请转到函数应用的 **"概述"** 选项卡。 选择 **"下载应用内容"** 按钮。

![下载应用内容](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> 配置连续集成后，您无法在"功能"门户中编辑源文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳做法](functions-best-practices.md)
