---
title: Azure 安全中心内的文件完整性监视
description: 了解如何使用 Azure 安全中心的文件完整性监视来比较基线。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 89230b6aca9e225c28a1efd0b99476b35f4d8db0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439556"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>使用文件完整性监视 (FIM) 比较基线

当资源中的敏感区域发生更改时，文件完整性监视 (FIM) 会通知你，以便你调查和处理未经授权的活动。 FIM 可监视 Windows 文件、Windows 注册表和 Linux 文件。

本主题介绍如何对文件和注册表启用 FIM。 有关 FIM 的详细信息，请参阅 [Azure 安全中心内的文件完整性监视](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>为何使用 FIM？

操作系统、应用程序和关联配置可控制资源的行为和安全状态。 因此，攻击者将控制你资源的文件作为目标，以便突袭资源的操作系统和/或执行活动而不被检测到。

实际上，许多合规性标准（例如 PCI-DSS 和 ISO 17799）都要求实施 FIM 控制。  

## <a name="enable-built-in-recursive-registry-checks"></a>启用内置的递归注册表检查

FIM 注册表配置单元的默认设置提供了一种简便的方法来监视公共安全区域内的递归更改。  例如，攻击者可能通过在启动或关闭时配置执行，来配置要在 LOCAL_SYSTEM 上下文中执行的脚本。  若要监视此类更改，请启用内置检查。  

![注册表](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 递归检查仅适用于建议的安全配置单元，不适用于自定义注册表路径。  

## <a name="add-a-custom-registry-check"></a>添加自定义注册表检查

FIM 基线首先确定操作系统和支持应用程序的已知良好状态的特征。  在此示例中，我们将重点介绍 Windows Server 2008 及更高版本的密码策略配置。


|策略名称                 | 注册表设置|
|---------------------------------------|-------------|
|域控制器：拒绝计算机帐户密码更改| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|域成员：对安全通道数据进行数字加密或数字签名(始终)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|域成员：对安全通道数据进行数字加密(如果可能)|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|域成员：对安全通道数据进行数字签名(如果可能)|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|域成员：禁用计算机帐户密码更改|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|域成员：计算机帐户密码最长使用期限|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|域成员：需要强(Windows 2000 或更高版本)会话密钥|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|网络安全:限制 NTLM:此域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|网络安全:限制 NTLM:添加此域中的服务器例外|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|网络安全:限制 NTLM:审核此域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 若要详细了解各种操作系统版本支持的注册表设置，请参阅[组策略设置参考电子表格](https://www.microsoft.com/download/confirmation.aspx?id=25250)。

将 FIM 配置为监视注册表基线：

1. 在“添加 Windows 注册表以跟踪更改”窗口的“Windows 注册表项”文本框中，输入注册表项。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![对注册表启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="track-changes-to-windows-files"></a>跟踪 Windows 文件的更改

1. 在“添加 Windows 文件以跟踪更改”窗口的“输入路径”文本框中，输入包含要跟踪的文件的文件夹。在下图的示例中，Contoso Web 应用位于 D:\ 驱动器的 ContosWebApp 文件夹结构中。  
1. 通过提供设置类的名称、启用递归并使用通配符 (*) 后缀指定顶级文件夹，来创建自定义 Windows 文件条目。

    ![对文件启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieve-change-data"></a>检索更改数据

文件完整性监视数据位于 Azure Log Analytics / ConfigurationChange 表集中。  

 1. 设置时间范围以按资源检索更改摘要。
在下面的示例中，我们将检索过去十四天内注册表和文件类别中的所有更改：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 查看注册表更改的详细信息：

    1. 从 where 子句中删除 Files， 
    1. 删除摘要行，并将其替换为 ordering 子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

可以将报告导出到 CSV 进行存档以及/或者导出为 Power BI 报告。  

![FIM 数据](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)