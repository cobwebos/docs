---
title: VM 更新和管理 Azure Stack |Microsoft Docs
description: 了解如何在 Azure 自动化中使用更新管理、 更改跟踪和清单解决方案来管理 Azure Stack 中部署的 Windows 虚拟机。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.openlocfilehash: be793fa5d346d05e6b7bd9f93f1108b7a3542fa6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959166"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack VM 更新和管理
可以使用以下 Azure 自动化解决方案功能来管理使用 Azure Stack 部署的 Windows Vm:

- **[更新管理](https://docs.microsoft.com/azure/automation/automation-update-management)**。 使用更新管理解决方案中，可以快速评估所有代理计算机上的可用更新的状态和管理适用于这些 Windows Vm 中安装所需的更新的过程。

- **[更改跟踪](https://docs.microsoft.com/azure/automation/automation-change-tracking)**。 对已安装的软件、 Windows 服务、 Windows 注册表和受监视服务器上的文件的更改发送到云中的 Log Analytics 服务进行处理。 逻辑应用于接收的数据，云服务则记录数据。 通过使用“更改跟踪”仪表板上的信息，可以轻松查看服务器基础结构中所做的更改。

- **[库存](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**。 库存跟踪为 Azure Stack Windows 虚拟机提供了用于设置和配置清单收集的基于浏览器的用户界面。 

> [!IMPORTANT]
> 这些解决方案都与用于管理 Azure Vm 的相同。 Azure 和 Azure Stack Windows Vm 中进行管理的相同方式，从相同的接口，使用相同的工具。 Azure Stack Vm 与 Azure Stack 配合使用更新管理、 更改跟踪和清单解决方案时还进行定价与 Azure Vm 相同。

## <a name="prerequisites"></a>必备组件
使用这些功能更新和管理 Azure Stack Windows Vm 之前，必须满足几个先决条件。 在 Azure 门户和 Azure Stack 管理门户，其中包括必须采取的步骤。

### <a name="in-the-azure-portal"></a>在 Azure 门户中
若要使用 Azure Stack Windows Vm 的清单、 更改跟踪和更新管理 Azure 自动化功能，首先需要启用 Azure 中的这些解决方案。

> [!TIP]
> 如果已为 Azure Vm 启用这些功能，可以使用预先存在的 LogAnalytics 工作区凭据。 如果已 LogAnalytics 工作区 Id 和你想要使用的主键，跳到[下一步部分](./vm-update-management.md#in-the-azure-stack-administration-portal)。 否则，继续在此部分中创建新的 LogAnalytics 工作区和自动化帐户。

启用这些解决方案的第一步是向[创建 LogAnalytics 工作区](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)在 Azure 订阅中。 Log Analytics 工作区是一个具有其自己的数据存储库、 数据源和解决方案的独特 Log Analytics 环境。 创建一个工作区后，请注意工作区 Id 和密钥。 若要查看此信息，请转到工作区边栏选项卡，单击**高级设置**，并查看**工作区 ID**并**Primary Key**值。 

接下来，必须[创建自动化帐户](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)。 自动化帐户是 Azure 自动化资源的容器。 它使您能够隔离环境，或者进一步组织您的自动化工作流和资源。 创建自动化帐户后，您需要以启用清单，更改跟踪，并更新管理功能。 若要执行此操作，请执行以下步骤启用每个功能：

1. 在 Azure 门户中，转到你想要使用自动化帐户。

2. 选择要启用的解决方案 (任一**清单**，**更改跟踪**，或**更新管理**)。

3. 使用**选择工作区...** 下拉列表选择要使用 Log Analytics 工作区。

4. 验证所有剩余信息是否正确，然后依次**启用**以启用此解决方案。

5. 重复步骤 2-4，以启用所有三种解决方案。 

   [![](media/vm-update-management/1-sm.PNG "启用自动化帐户功能")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>在 Azure Stack 管理门户
启用后在 Azure 门户中的 Azure 自动化解决方案，接下来需要登录到云管理员在 Azure Stack 管理门户并下载**Azure 更新和配置管理**扩展 AzureStack marketplace 项。 

   ![Azure 更新和配置管理扩展 marketplace 项](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>为 Azure Stack 虚拟机启用更新管理
按照以下步骤来为 Azure Stack Windows Vm 启用更新管理。

1. 登录到 Azure Stack 用户门户。

2. 在 Azure Stack 用户门户，转到 Windows 的扩展边栏选项卡你想要启用这些解决方案，请单击虚拟机 **+ 添加**，选择**Azure 更新和配置管理**扩展，然后单击**创建**:

   [![](media/vm-update-management/3-sm.PNG "Windows VM 扩展边栏选项卡")](media/vm-update-management/3-lg.PNG#lightbox)

3. 提供将先前创建的工作区 Id 和主密钥，若要链接的代理具有 LogAnalytics 工作区，然后单击**确定**将扩展部署。

   [![](media/vm-update-management/4-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/4-lg.PNG#lightbox) 

4. 如中所述[自动化更新管理文档](https://docs.microsoft.com/azure/automation/automation-update-management)，您需要为你想要管理每个 VM 启用更新管理解决方案。 若要启用向工作区报告的所有 Vm 解决方案，选择**更新管理**，单击**管理机**，然后选择**上所有可用的和未来计算机都启用**选项。

   [![](media/vm-update-management/5-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > 重复此步骤，为 Azure Stack Windows Vm 的报表工作区中启用每个解决方案。 
  
Azure 更新和配置管理扩展已启用后，扫描是每天执行两次为每个托管的 Windows VM。 查询上次更新时间，以确定是否已更改状态每隔 15 分钟，将调用 Windows API。 如果状态已更改，则会启动符合性扫描。

虚拟机进行扫描后，它们将显示在更新管理解决方案中的 Azure 自动化帐户中： 

   [![](media/vm-update-management/6-sm.PNG "提供工作区 Id 和密钥")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 可能需要 30 分钟到 6 小时，仪表板才会显示受托管计算机提供的已更新数据。

现在可以在计划的更新部署以及 Azure Vm 中包含 Azure Stack Windows Vm。

## <a name="enable-update-management-using-a-resource-manager-template"></a>启用更新管理使用资源管理器模板
如果有大量的 Azure Stack Windows Vm，则可以使用[此 Azure 资源管理器模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)更轻松地部署 Windows Vm 上的解决方案。 该模板将 Microsoft Monitoring Agent 扩展部署到现有 Windows VM，并将其添加到现有 Azure LogAnalytics 工作区。
 
## <a name="next-steps"></a>后续步骤
[优化 SQL Server 性能](azure-stack-sql-server-vm-considerations.md)
