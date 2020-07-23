---
title: 为 Azure 虚拟机添加扩展指标
description: 本文帮助你为 Azure VM 启用和配置扩展的诊断指标。
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: b4c646f3d42edc39f457cd735f16409f4ef05d2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481613"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>为 Azure 虚拟机添加扩展指标

Cloudyn 使用 Azure VM 中的 Azure 指标数据来显示有关其资源的详细信息。 Cloudyn 使用指标数据（也称为性能计数器）生成报表。 但是，Cloudyn 不会自动从来宾 VM 收集所有的 Azure 指标数据 — 我们必须启用指标收集。 本文将会帮助你为 Azure VM 启用和配置附加的诊断指标。

启用指标收集后，可以：

- 知道 VM 何时达到其内存、磁盘和 CPU 限制。
- 检测用量趋势和异常。
- 根据用量调整大小，以控制成本。
- 获取 Cloudyn 提供的经济高效的大小调整优化建议。

例如，你可能想要监视 Azure VM 的 CPU 百分比和内存百分比。 Azure VM 指标对应于“CPU 百分比”和“\内存\使用中的已提交字节百分比”。

> [!NOTE]
> 只有 Azure 来宾级监视支持扩展的指标数据收集。 Cloudyn 与 [Log Analytics 代理](../../azure-monitor/platform/agents-overview.md)不兼容。

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>确定是否启用了扩展的指标

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在“虚拟机”下选择一个 VM，然后在“监视”下选择“指标”。    此时会显示可用指标的列表。
3. 选择一些指标，随后有一个图形会显示这些指标的数据。  
    ![示例指标 – 主机 CPU 百分比](./media/azure-vm-extended-metrics/metric01.png)

在上面的示例中，为主机提供了有限的一组标准指标，但未提供内存指标。 内存指标是扩展指标的一部分。 在这种情况下，没有为 VM 启用扩展的指标。 必须执行一些附加的步骤才能启用扩展指标。 以下信息将引导你启用扩展指标。

## <a name="enable-extended-metrics-in-the-azure-portal"></a>在 Azure 门户中启用扩展指标

标准指标属于主机指标。 “CPU 百分比”指标就是一个例子。  来宾 VM 的某些基本指标也称为扩展指标。 扩展指标的示例包括“\内存\使用中的已提交字节百分比”和“\内存\可用字节”。

启用扩展指标的过程非常简单。 对每个 VM 启用来宾级监视。 启用来宾级监视时，将在 VM 上安装 Azure 诊断代理。 默认情况下会添加一组基本的扩展指标。 以下过程在经典和常规 VM，以及 Windows 和 Linux VM 上都是相同的。

请记住，Azure 和 Linux 来宾级监视都需要一个存储帐户。 启用来宾级监视时，如果没有选择现有的存储帐户，则会为你创建一个。

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>在现有 VM 上启用来宾级监视

1. 在“虚拟机”中查看 VM 列表，然后选择一个 VM。 
2. 在“监视”下，选择“诊断设置”   。
3. 在“诊断设置”页上，单击“启用来宾级监视”。   
    ![在“概述”页面上启用访客级别监控](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. 几分钟后，即会在 VM 上安装 Azure 诊断代理。 将添加一组基本的指标。 刷新页面。 添加的性能计数器将出现在“概述”选项卡上。
5. 在“监视”下，选择“指标”。 
6. 在“指标命名空间”  下的指标图表中，选择“来宾(经典)”  。
7. 在“指标”列表中，可以查看来宾 VM 的所有可用性能计数器。  
    ![示例扩展指标列表](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>在新 VM 上启用来宾级监视

创建新的 VM 时，在“管理”选项卡上，针对“OS 来宾诊断”选择“开启”。  

![将来宾 OS 诊断设置为“开”](./media/azure-vm-extended-metrics/new-enable-diag.png)

有关为 Azure 虚拟机启用扩展指标的详细信息，请参阅[了解和使用 Azure Linux 代理](../../virtual-machines/extensions/agent-linux.md)和 [Azure 虚拟机代理概述](../../virtual-machines/extensions/agent-windows.md)。

## <a name="resource-manager-credentials"></a>资源管理器凭据

启用扩展指标后，请确保 Cloudyn 有权访问你的[资源管理器凭据](../../cost-management/activate-subs-accounts.md)。 Cloudyn 需要使用你的凭据来收集和显示 VM 的性能数据。 这些凭据还将用于创建成本优化建议。 Cloudyn 至少需要从实例中收集三天的性能数据，才能确定该实例是否适合减小大小。

## <a name="enable-vm-metrics-with-a-script"></a>使用脚本启用 VM 指标

可以使用 Azure PowerShell 脚本启用 VM 指标。 若要在许多 VM 上启用指标，可以使用脚本自动完成该过程。 GitHub 上的[启用 Azure 诊断](https://github.com/Cloudyn/azure-enable-diagnostics)中提供了示例脚本。

## <a name="view-azure-performance-metrics"></a>查看 Azure 性能指标

若要在 Cloudyn 门户中查看 Azure 实例的性能指标，请导航到“资产” > “计算” > “实例资源管理器”。 在 VM 实例列表中展开某个实例，然后展开某个资源查看详细信息。

![实例资源管理器中显示的示例信息](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>后续步骤

- 如果还没有为帐户启用 Azure 资源管理器 API 访问权限，请转到[激活 Azure 订阅和帐户](../../cost-management/activate-subs-accounts.md)。
