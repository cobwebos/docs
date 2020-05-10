---
title: Azure Automation State Configuration 概述
description: Azure 自动化状态配置、其术语和已知问题的概述
keywords: powershell dsc, 所需状态配置, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dbe617e6614eb69f0a7f6e31c89c1f645804fe1b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993856"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概述

Azure 自动化状态配置是一项 Azure 配置管理服务，可用于为任何云或本地数据中心内的节点编写、管理和编译 PowerShell Desired State Configuration （ [DSC）配置](/powershell/scripting/dsc/configurations/configurations)。 该服务还会导入[DSC 资源](/powershell/scripting/dsc/resources/resources)，并将配置分配给目标节点，所有这些都是在云中。 可以通过在 "**配置管理**" 下选择 "**状态配置（DSC）** "，在 Azure 门户中访问 Azure 自动化状态配置。 

可以使用 Azure 自动化状态配置来管理各种计算机：

- Azure 虚拟机
- Azure 虚拟机（经典）
- 本地或 Azure 以外的云中的物理/虚拟 Windows 计算机（包括 AWS EC2 实例）
- 位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

如果未准备好从云管理计算机配置，可以使用 Azure 自动化状态配置作为仅报告终结点。 此功能允许通过 DSC 设置（推送）配置并在 Azure 自动化中查看报表详细信息。

> [!NOTE]
> 如果已安装的 Azure VM Desired State Configuration 扩展版本大于2.70，则不需要额外付费地管理 Azure Vm。 有关详细信息，请参阅[**自动化定价页**](https://azure.microsoft.com/pricing/details/automation/)。

## <a name="why-use-azure-automation-state-configuration"></a>为何使用 Azure Automation State Configuration

与在 Azure 外部使用 DSC 相比，azure 自动化状态配置具有多项优势。 通过此服务，可从一个中心安全位置快速轻松地跨数千台计算机。 你可以轻松启用计算机，为其分配声明性配置，并查看显示每台计算机的符合性所指定的所需状态的报告。

适用于 DSC 的 Azure Automation State Configuration 服务类似于 PowerShell 脚本中的 Azure 自动化 Runbook。 换句话说，Azure 自动化以帮助你管理 PowerShell 脚本的相同方式帮助你管理 DSC 配置。 

### <a name="built-in-pull-server"></a>内置拉取服务器

Azure 自动化状态配置提供了类似于[Windows 功能 Dsc 服务](/powershell/scripting/dsc/pull-server/pullserver)的 DSC 请求服务器。 目标节点可以自动接收配置，符合所需状态，并报告其符合性。 Azure 自动化中的内置拉取服务器消除了设置和维护你自己的拉取服务器的需要。 Azure 自动化的目标可以是云中或本地的虚拟机，或物理 Windows 或 Linux 计算机。

### <a name="management-of-all-your-dsc-artifacts"></a>管理所有 DSC 项目

Azure 自动化状态配置为 powershell[所需状态配置](/powershell/scripting/dsc/overview/overview)提供了相同的管理层，因为它提供了 powershell 脚本。 从 Azure 门户或 PowerShell，你可以管理所有 DSC 配置、资源和目标节点。

![“Azure 自动化”页的屏幕截图](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>将报表数据导入 Azure Monitor 日志

使用 Azure Automation State Configuration 进行管理的节点将详细的报表状态数据发送到内置拉取服务器。 可以将 Azure Automation State Configuration 配置为将此数据发送到 Log Analytics 工作区。 请参阅[将 Azure 自动化状态配置报告数据转发到 Azure Monitor 日志](automation-dsc-diagnostics.md)。

## <a name="prerequisites-for-using-azure-automation-state-configuration"></a>使用 Azure 自动化状态配置的先决条件

使用 Azure 自动化状态配置时，请考虑本节中的要求。

### <a name="operating-system-requirements"></a>操作系统要求

运行 Windows 的节点支持以下版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016)独立产品 SKU 不包含 DSC 的实现。 因此，它不能由 PowerShell DSC 或 Azure 自动化状态配置进行管理。

对于运行 Linux 的节点，DSC Linux 扩展支持在[支持的 Linux 发行](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)版下列出的所有 linux 分发版。

### <a name="dsc-requirements"></a>DSC 要求

对于在 Azure 中运行的所有 Windows 节点，在启用计算机时安装[WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure) 。 对于运行 Windows Server 2012 和 Windows 7 的节点，启用了[WinRM](https://docs.microsoft.com/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) 。

对于在 Azure 中运行的所有 Linux 节点，在启用计算机时，将安装适用于[linux 的 POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>专用网络的配置

如果节点位于专用网络中，则需要以下端口和 Url。 这些资源为托管节点提供网络连接，并允许 DSC 与 Azure Automation 通信。

* 端口：仅适用于出站 internet 访问所需的 TCP 443
* 全局 URL： ***. azure-automation.net**
* US Gov 弗吉尼亚州的全局 URL： ***. azure-automation.us**
* 代理服务： **Https://\<workspaceId\>. agentsvc.azure-automation.net**

如果使用在节点之间进行通信的 DSC 资源，如[WaitFor * 资源](https://docs.microsoft.com/powershell/scripting/dsc/reference/resources/windows/waitForAllResource)，则还需要允许节点之间的流量。 请参阅每个 DSC 资源的文档，以了解这些网络要求。

#### <a name="proxy-support"></a>代理支持

Windows 版本 1809 及更高版本中提供了适用于 DSC 代理的代理支持。 通过在用于注册节点的[元配置脚本](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)中`ProxyCredential`设置`ProxyURL`和的值，可启用此选项。

>[!NOTE]
>Azure 自动化状态配置不为以前版本的 Windows 提供 DSC 代理支持。

对于 Linux 节点，DSC 代理支持代理，并使用`http_proxy`变量来确定 URL。

#### <a name="azure-automation-state-configuration-network-ranges-and-namespace"></a>Azure 自动化状态配置网络范围和命名空间

建议在定义异常时使用下面列出的地址。 对于 IP 地址，你可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新，包含当前部署的范围以及即将对 IP 范围进行的更新。

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的通信。 下表提供了每个区域的 DNS 记录：

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美国东部    | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西欧 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

有关区域 IP 地址列表（非区域名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) XML 文件。

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。
>
>每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 建议每周下载新的 XML 文件。 然后更新站点以正确标识在 Azure 中运行的服务。 

Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

## <a name="next-steps"></a>后续步骤

- 若要开始在 Azure 自动化状态配置中使用 DSC，请参阅[Azure 自动化状态配置](automation-dsc-getting-started.md)入门。
- 若要了解如何启用节点，请参阅[启用虚拟机以通过 Azure 自动化状态配置进行管理](automation-dsc-onboarding.md)。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 进行连续部署](automation-dsc-cd-chocolatey.md)。
