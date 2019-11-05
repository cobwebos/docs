---
title: 在 Azure 安全中心中比较基线与文件完整性监视 |Microsoft Docs
description: 了解如何在 Azure 安全中心中比较基线与文件完整性监视。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: memildin
ms.openlocfilehash: 0e7ef558ec75622b804aef96781b549f1a833e21
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518866"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>使用文件完整性监视（FIM）比较基线

文件完整性监视（FIM）会在资源中的敏感区域发生更改时通知你，因此你可以调查和处理未经授权的活动。 FIM 监视 Windows 文件、Windows 注册表和 Linux 文件。

本主题介绍如何对文件和注册表启用 FIM。 有关 FIM 的详细信息，请参阅[Azure 安全中心中的文件完整性监视](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>为什么要使用 FIM？

操作系统、应用程序和关联的配置控制资源的行为和安全状态。 因此，攻击者以控制资源的文件为目标，以便 overtake 资源的操作系统和/或执行活动，而不会被检测到。

事实上，许多法规遵从标准（如 PCI-DSS & ISO 17799）都需要实施 FIM 控件。  

## <a name="enable-built-in-recursive-registry-checks"></a>启用内置的递归注册表检查

FIM 注册表 hive 默认值提供了一种简便的方法来监视公共安全区域内的递归更改。  例如，攻击者可以通过在启动或关闭时配置执行，将脚本配置为在 LOCAL_SYSTEM 上下文中执行。  若要监视此类型的更改，请启用内置检查。  

![注册表](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 递归检查仅适用于建议的安全配置单元，不适用于自定义注册表路径。  

## <a name="adding-a-custom-registry-check"></a>添加自定义注册表检查

FIM 基线首先确定操作系统和支持应用程序的已知良好状态的特征。  在此示例中，我们将重点介绍 Windows Server 2008 和更高版本的密码策略配置。


|策略名称                 | 注册表设置|
|---------------------------------------|-------------|
|域控制器：拒绝计算机帐户密码更改| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|域成员：对安全通道数据进行数字加密或签名（始终）|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|域成员：对安全通道数据进行数字加密（如果可能）|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|域成员：对安全通道数据进行数字签名（如果可能）|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|域成员：禁用计算机帐户密码更改|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|域成员：计算机帐户密码最长使用期限|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|域成员：需要强（Windows 2000 或更高版本）会话密钥|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|网络安全：限制 NTLM：此域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|网络安全：限制 NTLM：在此域中添加服务器例外|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|网络安全：限制 NTLM：审核此域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 若要详细了解各种操作系统版本支持的注册表设置，请参阅[组策略设置参考电子表格](https://www.microsoft.com/download/confirmation.aspx?id=25250)。

*若要配置 FIM 以监视注册表基线：*

1. 在 "**为更改跟踪添加 Windows 注册表**" 窗口的 " **windows 注册表项**" 文本框中，输入注册表项。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![在注册表中启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>跟踪对 Windows 文件的更改

1. 在 "**为更改跟踪添加 Windows 文件**" 窗口的 "**输入路径**" 文本框中，输入包含要跟踪的文件的文件夹。在下图的示例中， **Contoso Web 应用**驻留在 D:\ 中。**ContosWebApp**文件夹结构内的驱动器。  
1. 通过提供设置类的名称，启用递归，并使用通配符（*）指定顶级文件夹来创建自定义 Windows 文件条目。

    ![对文件启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>检索变更数据

文件完整性监视数据位于 Azure Log Analytics/ConfigurationChange 表集内。  

 1. 设置时间范围以检索资源的更改摘要。
在下面的示例中，我们将在注册表和文件的类别中检索过去14天内的所有更改：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 查看注册表更改的详细信息：

    1. 从**where**子句中删除**文件**， 
    1. 删除摘要行并将其替换为排序子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

可以将报表导出到 CSV 进行存档，并/或将其作为 Power BI 报表。  

![FIM 数据](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)