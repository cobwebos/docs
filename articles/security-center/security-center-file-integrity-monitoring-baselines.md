---
title: 比较基线与 Azure 安全中心中的文件完整性监视 |Microsoft Docs
description: 了解如何比较基线与 Azure 安全中心中的文件完整性监视。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358433"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>比较基准，使用文件完整性监视 (FIM)

文件完整性监视 (FIM) 通知发生更改时进入敏感区域在你的资源，因此可以调查和处理未经授权的活动。 FIM 监视 Windows 文件、 Windows 注册表和 Linux 文件。

本主题说明如何启用 FIM 上的文件和注册表。 有关 FIM 的详细信息，请参阅[Azure 安全中心中的文件完整性监视](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>为何使用 FIM？

操作系统、 应用程序和关联的配置控制资源的行为和安全的状态。 因此，攻击者以控制您的资源，以取代资源的操作系统和/或执行活动，而不被检测到的文件。

事实上，许多法规符合性标准，如 PCI DSS 和 ISO 17799 需要实现 FIM 控件。  

## <a name="enable-built-in-recursive-registry-checks"></a>启用内置递归注册表检查

FIM 注册表配置单元的默认值提供监视常见的安全领域内的递归更改的简便方法。  例如，攻击者可能会配置通过在启动或关闭配置执行 LOCAL_SYSTEM 上下文中执行的脚本。  若要监视此类型的更改，请启用内置的检查。  

![注册表](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 递归检查仅适用于建议的安全配置单元而不是为自定义注册表路径。  

## <a name="adding-a-custom-registry-check"></a>添加自定义注册表检查

FIM 基线入手，标识操作系统的已知良好状态的特征和支持性的应用程序。  对于此示例中，我们将重点介绍 Windows Server 2008 或更高版本的密码策略配置。


|策略名称                 | 注册表设置|
|---------------------------------------|-------------|
|域控制器：拒绝计算机帐户的密码更改| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|域成员：进行数字加密或签名 （始终） 安全通道数据|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|域成员：进行数字加密安全通道数据 （如果可能）|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|域成员：进行数字签名安全通道数据 （如果可能）|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|域成员：禁用计算机帐户密码更改|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|域成员：最大的计算机帐户密码使用期限|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|域成员：需要强 (Windows 2000 或更高版本) 会话密钥|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|网络安全：限制 NTLM:在这个域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|网络安全：限制 NTLM:在此域中添加服务器例外|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|网络安全：限制 NTLM:审核此域中的 NTLM 身份验证|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> 若要了解有关支持的各种操作系统版本的注册表设置的详细信息，请参阅[组策略设置参考电子表格](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250)。

*若要配置 FIM 以监视注册表基线：*

1. 在**用于更改跟踪添加 Windows 注册表**窗口，请在**Windows 注册表项**文字框中，输入的注册表项。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![启用对注册表的 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Windows 文件跟踪更改

1. 在**用于更改跟踪添加 Windows 文件**窗口，请在**Enter 路径**文字框中，输入包含你想要跟踪的文件的文件夹。在下图中的示例**Contoso Web 应用**驻留在 D:\在驱动器**ContosWebApp**文件夹结构。  
1. 通过设置类的名称、 启用递归，并使用通配符 （*） 后缀指定顶部文件夹中创建的自定义的 Windows 文件条目。

    ![启用对文件的 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>正在检索变更数据

文件完整性监视数据驻留在 Azure Log Analytics 中 / ConfigurationChange 表集。  

 1. 设置要检索资源的更改摘要的时间范围。
在以下示例中，注册表和文件的类别中在过去 14 天内检索的所有更改：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 若要查看注册表更改的详细信息：

    1. 删除**文件**从**其中**子句， 
    1. 删除摘要行，并将其替换为一个排序子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

可以将报表导出到 CSV 存档和/或传递到 Power BI 报表。  

![FIM 数据](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)