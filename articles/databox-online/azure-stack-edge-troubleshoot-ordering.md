---
title: 使用 Azure 门户解决 Azure Stack Edge 排序问题 |Microsoft Docs
description: 介绍如何对 Azure Stack 边缘排序问题进行故障排除。
services: databox
author: twooley
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: twooley
ms.openlocfilehash: 226274c52610e24c305400d77dc7737d32c2b722
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783988"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>排查 Azure Stack 边缘排序问题

本文介绍如何解决 Azure Stack 边缘排序问题。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 排序问题疑难解答

## <a name="unsupported-subscription-or-region"></a>不支持的订阅或区域

**错误说明：** 在 Azure 门户中，如果收到错误：

*不支持所选的订阅或区域。请选择其他订阅或区域。*

![不支持的订阅或区域](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**建议的解决方案：**  请确保使用受支持的订阅，例如 [Microsoft 企业协议 (EA) ](https://azure.microsoft.com/overview/sales-number/)、 [云解决方案提供商 (CSP) ](https://docs.microsoft.com/partner-center/azure-plan-lp)或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支持即用即付订阅。 有关详细信息，请参阅 [Azure Stack Edge 资源先决条件](azure-stack-edge-deploy-prep.md#prerequisites)。

Microsoft 可能会根据具体情况，允许订阅类型的升级。 请与 [Microsoft 支持](https://azure.microsoft.com/support/options/) 部门联系，以便他们能够了解你的需求并相应地调整这些限制。

## <a name="selected-subscription-type-not-supported"></a>不支持所选的订阅类型

**错误：** 你有 EA、CSP 或赞助订阅，并收到以下错误：

*所选的订阅类型不受支持。请确保使用支持的订阅。 [了解更多](azure-stack-edge-deploy-prep.md#prerequisites)。如果使用受支持的订阅类型，请确保 `Microsoft.DataBoxEdge` 已注册该提供程序。有关如何注册的详细信息，请参阅 [注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*。

**建议的解决方案：** 请按照以下步骤注册 Azure Stack Edge 资源提供程序：

1. 在 Azure 门户中，请参阅**Home**  >  **订阅**。

2. 选择用于订购设备的订阅。

3. 选择 " **资源提供程序** "，然后搜索 " **DataBoxEdge**"。

    ![注册资源提供程序](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

如果你没有 "所有者" 或 "参与者" 访问权限来注册资源提供程序，则会看到以下错误： *订阅 &lt; 订阅名称没有 &gt; 注册资源提供程序 () ： DataBoxEdge 的权限。*

有关详细信息，请参阅 [注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>DataBoxEdge 未注册订阅

**错误：** 在 Azure 门户中，选择要用于 Azure Stack 边缘或 Data Box Gateway 的订阅，并收到以下错误：

*资源提供程序 () ： DataBoxEdge 未注册订阅 &lt; 订阅名称 &gt; ，并且您没有为订阅 &lt; 订阅名称 &gt; 注册资源提供程序的权限*。

**建议的解决方案：** 提升订阅访问权限或查找具有所有者或参与者访问权限的某人来注册资源提供程序。

## <a name="resource-disallowed-by-policy"></a>策略不允许的资源

**错误：** 在 Azure 门户中，尝试注册资源提供程序并收到以下错误：

*&lt; &gt; 策略不允许资源资源名称。 (代码： RequestDisallowedByPolicy) 。计划：拒绝通常不需要的资源类型。策略：不允许的资源类型。*

**建议的解决方案：** 此错误是由于现有的 Azure 策略阻止资源创建而发生的。 Azure 策略由组织的系统管理员设置，以确保使用或创建 Azure 资源时的符合性。 如果任何此类策略正在阻止 Azure Stack 创建 Edge 资源，请与系统管理员联系，以编辑 Azure 策略。

## <a name="next-steps"></a>后续步骤

* 了解有关如何 [排查 Azure Stack 边缘问题](azure-stack-edge-troubleshoot.md)的详细信息。
