---
title: 为 Azure 虚拟机添加扩展指标 | Microsoft Docs
description: 本文帮助你为 Azure VM 启用和配置扩展的诊断指标。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>为 Azure 虚拟机添加扩展指标

成本管理使用 Azure VM 中的 Azure 指标数据来显示有关其资源的详细信息。 成本管理使用指标数据（也称为性能计数器）生成报表。 但是，成本管理不会自动从来宾 VM 收集所有的 Azure 指标数据 - 我们必须启用指标收集。 本文将会帮助你为 Azure VM 启用和配置附加的诊断指标。

启用指标收集后，可以：

- 知道 VM 何时达到其内存、磁盘和 CPU 限制。
- 检测用量趋势和异常。
- 根据用量调整大小，以控制成本。
- 获取成本管理提供的经济高效的大小调整优化建议。

例如，你可能想要监视 Azure VM 的 CPU 百分比和内存百分比。 Azure VM 指标对应于“[主机] CPU 百分比”和“[来宾] 内存百分比”。

## <a name="verify-that-metrics-are-enabled-on-vms"></a>验证是否在 VM 上启用了指标

1. 通过 http://portal.azure.com 登录到 Azure 门户。
2. 在“虚拟机”下选择一个 VM，然后在“监视”下选择“指标”。 此时会显示可用指标的列表。
3. 选择一些指标，随后有一个图形会显示这些指标的数据。  
    ![示例指标 – 主机 CPU 百分比](./media/azure-vm-extended-metrics/metric01.png)

在上面的示例中，为主机提供了有限的一组标准指标，但未提供内存指标。 内存指标是扩展指标的一部分。 必须执行一些附加的步骤才能启用扩展指标。 以下信息将引导你启用扩展指标。

## <a name="enable-extended-metrics-in-the-azure-portal"></a>在 Azure 门户中启用扩展指标

标准指标属于主机指标。 “[主机] CPU 百分比”指标就是一个例子。 来宾 VM 的某些基本指标也称为扩展指标。 扩展指标的示例包括“[来宾] 内存百分比”和“[来宾] 可用内存”。

启用扩展指标的过程非常简单。 对每个 VM 启用来宾级监视。 启用来宾级监视时，将在 VM 上安装 Azure 诊断代理。 以下过程在经典和常规 VM，以及 Windows 和 Linux VM 上都是相同的。

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>在现有 VM 上启用来宾级监视

1. 在“虚拟机”中查看 VM 列表，然后选择一个 VM。
2. 在“监视”下，选择“指标”。
3. 单击“诊断设置”。
4. 在“诊断设置”页上，单击“启用来宾级监视”。 Linux VM 需要现有的存储帐户。 如果未选择 Windows VM 的存储帐户，系统会自动创建一个。  
    ![启用来宾级监视](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. 几分钟后，即会在 VM 上安装 Azure 诊断代理。 刷新页面，可用指标列表将会更新，其中包含了来宾指标。  
    ![扩展指标](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>在新 VM 上启用来宾级监视

创建新 VM 时，请确保选择“来宾 OS 诊断”。 Linux VM 需要现有的存储帐户。 如果未选择 Windows VM 的存储帐户，系统会自动创建一个。

![启用来宾 OS 诊断](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>资源管理器凭据

启用扩展指标后，请确保成本管理有权访问你的[资源管理器凭据](activate-subs-accounts.md)。 成本管理需要使用你的凭据来收集和显示 VM 的性能数据。 这些凭据还将用于创建成本优化建议。 成本管理至少需要从实例中收集三天的性能数据，才能确定该实例是否适合减小大小。

## <a name="enable-vm-metrics-with-a-script"></a>使用脚本启用 VM 指标

可以使用 Azure PowerShell 脚本启用 VM 指标。 若要在许多 VM 上启用指标，可以使用脚本自动完成该过程。 GitHub 上的[启用 Azure 诊断](https://github.com/Cloudyn/azure-enable-diagnostics)中提供了示例脚本。

## <a name="view-azure-performance-metrics"></a>查看 Azure 性能指标

若要在 Cloudyn 门户中查看 Azure 实例的性能指标，请导航到“资产” > “计算” > “实例资源管理器”。 在 VM 实例列表中展开某个实例，然后展开某个资源查看详细信息。

![实例资源管理器](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>后续步骤

- 如果尚未为帐户启用 Azure 资源管理器 API 访问权限，请转到[激活 Azure 订阅和帐户](activate-subs-accounts.md)
