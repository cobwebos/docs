---
title: 使用 Visual Studio 部署虚拟机规模集
description: 使用 Visual Studio 和 Resource Manager 模板部署虚拟机规模集
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 826dc1858984508e54e160675dd10f6dfad88af8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124357"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>如何使用 Visual Studio 创建虚拟机规模集

本文介绍如何使用 Visual Studio 资源组部署来部署 Azure 虚拟机规模集。

[Azure 虚拟机规模集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是一种 Azure 计算资源，可通过自动缩放和负载均衡部署和管理一组类似的虚拟机。 可使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)预配和部署虚拟机规模集。 可以使用 Azure CLI、PowerShell、REST 来部署 Azure 资源管理器模板，也可直接从 Visual Studio 部署。 Visual Studio 提供了一组示例模板，这些模板可以作为 Azure 资源组部署项目的一部分进行部署。

Azure 资源组部署是一种通过单个部署操作将相关的一组 Azure 资源组合并进行发布的方式。 有关详细信息，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="prerequisites"></a>必备条件

若要开始在 Visual Studio 中部署虚拟机规模集，需要具备以下先决条件：

* Visual Studio 2013 或更高版本
* Azure SDK 2.7、2.8 或 2.9

>[!NOTE]
>本文将 Visual Studio 2019 与 [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) 配合使用。

## <a name="create-a-project"></a>创建项目 <a name="creating-a-project"></a> 

1. 打开 Visual Studio 并选择“创建新项目”  。

1. 在“创建新项目”  中，选择用于 C# 的“Azure 资源组”  ，然后选择“下一步”  。

1. 在“配置新项目”中输入名称，然后选择“创建”。  

    ![命名并创建项目](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 在模板列表中，选择“Windows 虚拟机规模集”或“Linux 虚拟机规模集”模板。   选择“确定”  。

   ![选择虚拟机模板](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

创建项目后，**解决方案资源管理器**会包含 PowerShell 部署脚本、Azure 资源管理器模板和虚拟机规模集的参数文件。

## <a name="customize-your-project"></a>自定义项目

现在可以编辑模板以根据应用程序的需求自定义它。 可以添加虚拟机扩展属性或编辑负载均衡规则。 默认情况下，虚拟机规模集模板已配置为部署 **AzureDiagnostics** 扩展，如此即可轻松添加自动缩放规则。 这些模板还部署了具有公共 IP 地址且配置有入站 NAT 规则的负载均衡器。

通过负载均衡器，可以使用 SSH (Linux) 或 RDP (Windows) 连接到虚拟机实例。 前端端口范围从 50000 开始。 对于 Linux，如果通过 SSH 连接到端口 50000，则会路由到规模集中第一个虚拟机的端口 22。 连接到端口 50001 将路由到第二个虚拟机的端口 22，依此类推。

 使用 Visual Studio 编辑模板的一种好方法是使用“JSON 概要”。  根据组织参数、变量和资源。 了解架构后，Visual Studio 可以在部署前指出模板中的错误。

![JSON 资源管理器](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>部署项目

部署 Azure 资源管理器模板来创建虚拟机规模集资源：

1. 在“解决方案资源管理器”  中，右键单击该项目并选择“部署” **“新建”**  >   。

    ![部署项目](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. 在“部署到资源组”中，选择要使用的订阅并选择资源组。  可以根据需要创建资源组。

1. 接下来，选择“编辑参数”  ，输入要传递给模板的参数。

   ![输入订阅和资源组](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 为操作系统提供用户名和密码。 这些值是创建部署所需的。 如果未安装用于 Visual Studio 的 PowerShell 工具，请选择“保存密码”  ，以避免隐藏的 PowerShell 命令提示符，或使用 [Key Vault 支持](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)。 选择“保存”以继续操作。 

    ![编辑部署参数](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. 在“部署到资源组”中，选择“部署”。   该操作运行 **Deploy-AzureResourceGroup.ps1** 脚本。 “输出”  窗口显示部署进度。

   ![输出显示结果](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>探索虚拟机规模集 <a name="exploring-your-virtual-machine-scale-set"></a>

选择“查看”   >   “Cloud Explorer”，查看新的虚拟机规模集。 根据需要使用“全部刷新”  。

![云资源管理器](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** 让你可以在开发应用程序时管理 Visual Studio 中的 Azure 资源。 还可以在 [Azure 门户](https://portal.azure.com)和 [Azure 资源浏览器](https://resources.azure.com/)中查看虚拟机规模集。

 门户是使用 Web 浏览器时管理 Azure 基础结构的最佳方法。 可以使用 Azure 资源浏览器轻松地浏览和调试 Azure 资源。 Azure 资源浏览器提供实例视图，并为你要查看的资源显示 PowerShell 命令。

## <a name="next-steps"></a>后续步骤

通过 Visual Studio 成功部署虚拟机规模集后，便可进一步自定义项目以满足应用程序需求。 例如，通过添加 **Insights** 资源来配置自动缩放。 可以将基础结构（例如独立虚拟机）添加到模板，或者使用自定义脚本扩展来部署应用程序。 可以在 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates) GitHub 存储库中找到很好的示例模板。 搜索 `vmss`。
