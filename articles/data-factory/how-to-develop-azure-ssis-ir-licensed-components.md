---
title: 为 Azure-SSIS 集成运行时安装许可的组件 | Microsoft Docs
description: 了解 ISV 如何为 Azure-SSIS 集成运行时开发和安装付费或许可的自定义组件
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 6351381e525d256ef5e9693ea1fb5e3a6f4e5ea3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298570"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时安装付费或许可的自定义组件

本文介绍 ISV 如何为 Azure-SSIS 集成运行时的 Azure 中运行的 SQL Server Integration Services (SSIS) 包开发和安装付费或许可的自定义组件。

## <a name="the-problem"></a>问题

Azure-SSIS 集成运行时的性质带来了多种挑战，使得在本地安装自定义组件所用的典型许可方法并不足以解决问题。 因此，Azure-SSIS IR 需要不同的方法。

-   Azure-SSIS IR 的节点是易失性的，随时可以分配或释放。 例如，可以启动或停止节点来管理成本，或者通过不同的节点大小进行纵向扩展和缩减。 因此，使用计算机特定的信息（例如 MAC 地址或 CPU ID）将第三方组件许可证绑定到特定节点不再可行。

-   还可以横向扩展或缩减 Azure-SSIS IR，使节点数目能够随时缩减或扩展。

## <a name="the-solution"></a>解决方案

由于上一部分中介绍的传统许可方法的局限性，Azure-SSIS IR 提供了一种新的解决方案。 此解决方案使用 Windows 环境变量和 SSIS 系统变量来绑定许可证和验证第三方组件。 ISV 可以使用这些变量来获取 Azure-SSIS IR 的唯一和持久信息，例如群集 ID 和群集节点计数。 有了此信息，ISV 就可以将其组件的许可证*作为群集*绑定到 Azure-SSIS IR。 此绑定使用的 ID 不会在客户启动或停止、增加或减少、缩小或扩大或以任何方式重新配置 Azure-SSIS IR 时发生变化。

下图显示了使用这些新变量对第三方组件执行的典型安装、激活和许可证绑定以及验证流：

![许可组件的安装](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>说明
1. ISV 可在各种 SKU 或层（例如，单个节点、最多 5 个节点、最多 10 个节点，等等）中提供其许可组件。 当客户购买某个产品时，ISV 会提供相应的产品密钥。 ISV 还可以提供包含 ISV 安装脚本和关联文件的 Azure 存储 Blob 容器。 客户可将这些文件复制到其自己的存储容器中，并使用其自己的产品密钥修改这些文件（例如运行 `IsvSetup.exe -pid xxxx-xxxx-xxxx`）。 然后，客户可以使用容器的 SAS URI 作为参数，来预配或重新配置 Azure-SSIS IR。 有关详细信息，请参阅 [Azure-SSIS 集成运行时的自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。

2. 在预配或重新配置 Azure-SSIS IR 时，ISV 安装程序将在每个节点上运行，以查询 Windows 环境变量 `SSIS_CLUSTERID` 和 `SSIS_CLUSTERNODECOUNT`。 然后，Azure-SSIS IR 会将许可产品的群集 ID 和产品密钥提交到 ISV 激活服务器，以生成激活密钥。

3. 收到激活密钥后，ISV 安装程序可在每个节点的本地存储密钥（例如，在注册表中）。

4. 当客户在 Azure-SSIS IR 的某个节点上运行使用 ISV 许可组件的包时，该包将读取本地存储的激活密钥，并根据节点的群集 ID 验证该密钥。 该包还可以选择性地向 ISV 激活服务器报告群集节点计数。

    下面是验证激活密钥并报告群集节点计数的代码示例：

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>ISV 合作伙伴

可以在此博客文章 [ADF 中 SSIS 的企业版、自定义安装和第三方扩展性](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)末尾找到已为 Azure-SSIS IR 改编了组件和扩展的 ISV 合作伙伴列表。

## <a name="next-steps"></a>后续步骤

-   [Azure-SSIS 集成运行时的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
