---
title: Azure Functions 的连续部署
description: 使用 Azure App Service 的持续部署功能发布函数。
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123649"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions 的连续部署

通过使用[源代码管理集成](functions-deployment-technologies.md#source-control)，你可以使用 Azure Functions 连续部署代码。 源代码管理集成启用了一个工作流，其中代码更新会触发到 Azure 的部署。 如果你不熟悉 Azure Functions，请通过查看[Azure Functions 概述](functions-overview.md)开始。

对于您集成多个和频繁发布的项目，持续部署是一个不错的选择。 当你使用连续部署时，你可以为你的代码维护一个真实来源，使团队可以轻松地进行协作。 你可以从以下源代码位置 Azure Functions 中配置连续部署：

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure 中函数的部署单位是 function app。 函数应用中的所有函数同时部署。 启用连续部署后，将 Azure 门户中的函数代码访问配置为*只读*，因为诚实源设置为其他位置。

## <a name="requirements-for-continuous-deployment"></a>连续部署的要求

若要成功进行连续部署，你的目录结构必须与 Azure Functions 需要的基本文件夹结构兼容。

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 对于在消耗计划中运行的 Linux 应用，尚不支持连续部署。 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>设置连续部署

若要为现有函数应用配置连续部署，请完成以下步骤。 这些步骤演示了与 GitHub 存储库的集成，但类似的步骤适用于 Azure Repos 或其他源代码存储库。

1. 在[Azure 门户](https://portal.azure.com)的函数应用中，选择 "**部署中心**"，选择 " **GitHub**"，并选择 "**授权**"。 如果已授权 GitHub，请选择 "**继续**"，然后跳过下一步。 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service 部署中心":::

3. 在 GitHub 中，选择 "**授权 AzureAppService**"。

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="授权 Azure App Service":::

    输入 GitHub 密码，并选择 "**继续**"。

4. 选择以下生成提供程序之一：

    * **应用服务生成服务**：在不需要生成或需要泛型生成时最好。
    * **Azure Pipelines （预览版）**：当需要更好地控制生成时，最好这样做。 此提供程序当前处于预览阶段。

    选择“继续”。 

5. 配置特定于指定的源代码管理选项的信息。 对于 GitHub，必须输入或选择 "**组织**"、"**存储库**" 和 "**分支**" 的值。 这些值基于您的代码的位置。 然后选择 "**继续**"。

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="配置 GitHub":::

6. 查看所有详细信息，然后选择 "**完成**" 以完成部署配置。

完成此过程后，指定源中的所有代码都将部署到你的应用。 此时，部署源中的更改会触发将这些更改部署到 Azure 中的函数应用。

> [!NOTE]
> 配置持续集成后，不能再在函数门户中编辑源文件。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 最佳做法](functions-best-practices.md)
