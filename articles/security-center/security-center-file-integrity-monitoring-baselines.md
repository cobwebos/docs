---
title: Azure 安全中心内的文件完整性监视
description: 了解如何将基线与 Azure 安全中心中的文件完整性监视进行比较。
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
ms.openlocfilehash: bb45e1d1ee17a6daf16bd688982f79fda986bde5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73664408"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>使用文件完整性监视 (FIM) 比较基线

当资源中的敏感区域发生更改时，文件完整性监视 （FIM） 会通知您，以便您可以调查和解决未经授权的活动。 FIM 监视 Windows 文件、Windows 注册表和 Linux 文件。

本主题介绍如何在文件和注册表上启用 FIM。 有关 FIM 的详细信息，请参阅[Azure 安全中心中的文件完整性监视](security-center-file-integrity-monitoring.md)。

## <a name="why-use-fim"></a>为什么要使用 FIM？

操作系统、应用程序和相关配置控制资源的行为和安全状态。 因此，攻击者将目标锁定控制资源的文件，以便超越资源的操作系统和/或执行活动而不检测到。

事实上，许多法规遵从性标准（如 PCI-DSS & ISO 17799）都需要实施 FIM 控制。  

## <a name="enable-built-in-recursive-registry-checks"></a>启用内置递归注册表检查

FIM 注册表配置单元默认值提供了一种监视公共安全区域中的递归更改的便捷方法。  例如，攻击者可以通过在启动或关闭时配置执行来配置脚本以在LOCAL_SYSTEM上下文中执行。  要监视此类型的更改，请启用内置检查。  

![注册表](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> 递归检查仅适用于建议的安全配置单元，不适用于自定义注册表路径。  

## <a name="adding-a-custom-registry-check"></a>添加自定义注册表检查

FIM 基线首先确定操作系统和支持应用程序的已知良好状态的特征。  在此示例中，我们将重点介绍 Windows Server 2008 及更高版本的密码策略配置。


|策略名称                 | 注册表设置|
|---------------------------------------|-------------|
|域控制器：拒绝计算机帐户密码更改| MACHINE_系统_当前控制集_服务 [Netlogon]参数\拒绝密码更改|
|域成员：对安全通道数据进行数字加密或数字签名（始终）|MACHINE_系统_电流控制集_服务[净]参数\需要签名或密封|
|域成员：对安全通道数据进行数字加密（如果可能）|MACHINE_系统_电流控制集_服务[Netlogon]参数\SealSecure通道|
|域成员：对安全通道数据进行数字签名（如果可能）|MACHINE_系统_当前控制集_服务 [Netlogon]参数\SignSecure通道|
|域成员：禁用计算机帐户密码更改|MACHINE_系统\当前控制集_服务 [Netlogon]参数\禁用密码更改|
|域成员：计算机帐户密码最长使用期限|MACHINE_系统_电流控制集_服务[Netlogon]参数\最大密码|
|域成员：需要强（Windows 2000 或更高版本）会话密钥|MACHINE_系统_电流控制集_服务[净日志]参数\需要强键|
|网络安全：限制 NTLM：在此域中的 NTLM 身份验证|MACHINE_系统_当前控制集_服务 [Netlogon]参数\限制NTLMInDomain|
|网络安全：限制 NTLM：在此域中添加服务器例外|MACHINE_系统_电流控制集_服务[Netlogon]参数\DC允许NTLM服务器|
|网络安全: 限制 NTLM: 审核此域中的 NTLM 身份验证|MACHINE_系统_当前控制集_服务 [净日志]参数\审核NTLMInDomain|

> [!NOTE]
> 要了解有关各种操作系统版本支持的注册表设置的详细信息，请参阅[组策略设置参考电子表格](https://www.microsoft.com/download/confirmation.aspx?id=25250)。

*要将 FIM 配置为监视注册表基线，*

1. 在"**添加 Windows 注册表用于更改跟踪"** 窗口中，在 **"Windows 注册表密钥"** 文本框中输入注册表项。

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![在注册表上启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>跟踪对 Windows 文件的更改

1. 在"**添加 Windows 文件以进行更改跟踪"** 窗口中，在 **"输入路径**文本"框中，输入包含要跟踪的文件的文件夹。在下图中的示例中 **，Contoso Web 应用**驻留在 D：***ContosWebApp**文件夹结构中的驱动器。  
1. 通过提供设置类的名称、启用递归以及使用通配符 （*） 后缀指定顶部文件夹，创建自定义 Windows 文件条目。

    ![在文件上启用 FIM](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>检索更改数据

文件完整性监视数据驻留在 Azure 日志分析/配置更改表集中。  

 1. 设置时间范围以按资源检索更改摘要。
在下面的示例中，我们将检索注册表和文件类别中最近 14 天内的所有更改：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. 要查看注册表更改的详细信息，

    1. 从**where**子句中删除**文件**， 
    1. 删除汇总行并将其替换为排序子句：

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

报表可以导出到 CSV 进行存档和/或引导到 Power BI 报表。  

![职能指令手册数据](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)