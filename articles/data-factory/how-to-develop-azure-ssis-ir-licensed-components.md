---
title: 为 Azure-SSIS 集成运行时开发付费或许可的组件 | Microsoft Docs
description: 本文介绍 ISV 如何为 Azure-SSIS 集成运行时开发和安装付费或许可的自定义组件
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: e22ca4bd5b749e8752f800590938199e06abbd34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="develop-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时开发付费或许可的自定义组件

## <a name="problem---the-azure-ssis-ir-requires-a-different-approach"></a>问题 - Azure-SSIS IR 需要不同的方法

Azure-SSIS 集成运行时的性质带来了多种挑战，使得在本地安装自定义组件所用的典型许可方法并不足以解决问题。

-   Azure-SSIS IR 的节点是易失性的，随时可以分配或释放。 例如，可以启动或停止节点来管理成本，或者通过不同的节点大小进行纵向扩展和缩减。 因此，使用计算机特定的信息（例如 MAC 地址或 CPU ID）将第三方组件许可证绑定到特定节点不再可行。

-   还可以横向扩展或缩减 Azure-SSIS IR，使节点数目能够随时缩减或扩展。

## <a name="solution---windows-environment-variables-and-ssis-system-variables-for-license-binding-and-validation"></a>解决方案 - 用于许可证绑定和验证的 Windows 环境变量和 SSIS 系统变量

由于传统许可方法存在上一部分所述的限制，Azure-SSIS IR 提供了 Windows 环境变量和 SSIS 系统变量，用于对第三方组件进行许可证绑定和验证。 ISV 可以使用这些变量来获取 Azure-SSIS IR 的唯一和持久信息，例如群集 ID 和群集节点计数。 使用此信息，ISV 可以通过某个 ID 将其组件的许可证作为群集绑定到 Azure-SSIS IR；当客户以任何方式启动或停止、纵向扩展或缩减、横向缩减或扩展或者重新配置 Azure-SSIS IR 时，该 ID 不会发生变化。

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

## <a name="next-steps"></a>后续步骤

-   [Azure-SSIS 集成运行时的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)