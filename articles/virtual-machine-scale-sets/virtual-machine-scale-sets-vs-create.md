---
title: 使用 Visual Studio 部署虚拟机规模集 | Microsoft Docs
description: 使用 Visual Studio 和 Resource Manager 模板部署虚拟机规模集
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: d397f81ce29e0ec738156b755948985a4edfc70b
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802259"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>如何使用 Visual Studio 创建虚拟机规模集

本文介绍如何使用 Visual Studio 资源组部署来部署 Azure 虚拟机规模集。

[Azure 虚拟机规模集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是一种 azure 计算资源，可通过自动缩放和负载平衡来部署和管理类似虚拟机的集合。 可使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)预配和部署虚拟机规模集。 可以使用 Azure CLI、PowerShell、REST 部署 Azure 资源管理器模板，也可以直接从 Visual Studio 部署。 Visual Studio 提供了一组示例模板，这些模板可以作为 Azure 资源组部署项目的一部分进行部署。

Azure 资源组部署是一种通过单个部署操作将相关的一组 Azure 资源组合并进行发布的方式。 有关详细信息，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="prerequisites"></a>先决条件

若要开始在 Visual Studio 中部署虚拟机规模集，需要满足以下先决条件：

* Visual Studio 2013 或更高版本
* Azure SDK 2.7、2.8 或 2.9

>[!NOTE]
>本文通过[AZURE SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)使用 Visual Studio 2019。

## 创建项目<a name="creating-a-project"></a> 

1. 打开 Visual Studio 并选择“创建新项目”。

1. 在 "**创建新项目**" 中，选择 " **Azure 资源组** C# "，然后选择 "**下一步**"。

1. 在 "**配置新项目**" 中，输入名称，然后选择 "**创建**"。

    ![命名并创建项目](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 从模板列表中，选择 " **Windows 虚拟机规模集**" 或 " **Linux 虚拟机规模集**" 模板。 选择“确定”。

   ![选择虚拟机模板](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

创建项目后，**解决方案资源管理器**包含 PowerShell 部署脚本、Azure 资源管理器模板和虚拟机规模集的参数文件。

## <a name="customize-your-project"></a>自定义项目

现在可以编辑模板以根据应用程序的需求对其进行自定义。 你可以添加虚拟机扩展属性或编辑负载均衡规则。 默认情况下，虚拟机规模集模板已配置为部署**AzureDiagnostics**扩展，这使添加自动缩放规则变得简单。 模板还部署了具有公共 IP 地址的负载均衡器，并配置了入站 NAT 规则。

负载均衡器可让你通过 SSH （Linux）或 RDP （Windows）连接到虚拟机实例。 前端端口范围从 50000 开始。 对于 Linux，如果 SSH 连接到端口50000，则负载均衡会将你路由到规模集中第一个虚拟机的端口22。 连接到端口50001将路由到第二个虚拟机的端口22，依此类推。

 使用 Visual Studio 编辑模板的一种好方法是使用**JSON 大纲**。 可以组织参数、变量和资源。 了解架构后，Visual Studio 可以在部署前指出模板中的错误。

![JSON 资源管理器](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>部署项目

部署 Azure 资源管理器模板以创建虚拟机规模集资源：

1. 在**解决方案资源管理器**中，右键单击项目并选择 "**部署** > **新**的"。

    ![部署项目](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. 在 "**部署到资源组**" 中，选择要使用的订阅并选择一个资源组。 如果需要，可以创建资源组。

1. 接下来，选择 "**编辑参数**" 以输入传递给模板的参数。

   ![输入订阅和资源组](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 提供操作系统的用户名和密码。 需要这些值来创建部署。 如果尚未安装 PowerShell Tools for Visual Studio，请选择 "**保存密码**"，以避免隐藏的 PowerShell 命令提示符，或使用[Key Vault 支持](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)。 选择 "**保存**" 继续。

    ![编辑部署参数](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. 在 "**部署到资源组**" 中，选择 "**部署**"。 操作将运行**deploy-azureresourcegroup.ps1**脚本。 “输出”窗口显示部署进度。

   ![输出显示结果](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## 探索虚拟机规模集<a name="exploring-your-virtual-machine-scale-set"></a>

选择 "**查看** > **Cloud Explorer**以查看新的虚拟机规模集。 如有必要，请使用 "**全部刷新**"。

![云资源管理器](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer**使你可以在开发应用程序时管理 Visual Studio 中的 Azure 资源。 还可以在 [Azure 门户](https://portal.azure.com)和 [Azure 资源浏览器](https://resources.azure.com/)中查看虚拟机规模集。

 门户提供了使用 web 浏览器管理 Azure 基础结构的最佳方式。 Azure 资源浏览器提供了一种简单的方法来浏览和调试 Azure 资源。 Azure 资源浏览器提供实例视图，还显示所查看资源的 PowerShell 命令。

## <a name="next-steps"></a>后续步骤

通过 Visual Studio 成功部署虚拟机规模集后，便可进一步自定义项目以满足应用程序需求。 例如，通过添加**Insights**资源来配置自动缩放。 你可以将基础结构添加到模板（例如独立虚拟机），或者使用自定义脚本扩展部署应用程序。 可以在[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)GitHub 存储库中找到好的示例模板。 搜索 `vmss`。
