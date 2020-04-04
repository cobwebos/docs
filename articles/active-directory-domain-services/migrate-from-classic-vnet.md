---
title: 从经典虚拟网络迁移 Azure AD 域服务 |微软文档
description: 了解如何将现有的 Azure AD 域服务托管域实例从经典虚拟网络模型迁移到基于资源管理器的虚拟网络。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655021"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器

Azure 活动目录域服务 （AD DS） 支持当前使用经典虚拟网络模型的客户一次性移动到资源管理器虚拟网络模型。 使用资源管理器部署模型的 Azure AD DS 托管域提供了其他功能，如细粒度密码策略、审核日志和帐户锁定保护。

本文概述了迁移的好处和注意事项，以及成功迁移现有 Azure AD DS 实例所需的步骤。

> [!NOTE]
> 2017 年，Azure AD 域服务可用于在 Azure 资源管理器网络中托管。 自那时以来，我们一直能够使用 Azure 资源管理器的现代功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，因此 Azure AD DS 经典虚拟网络部署将于 2023 年 3 月 1 日停用。
>
> 有关详细信息，请参阅[官方弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>迁移过程概述

迁移过程采用在经典虚拟网络中运行的现有 Azure AD DS 实例，并将其移动到现有的资源管理器虚拟网络。 迁移使用 PowerShell 执行，并且有两个主要执行阶段 -*准备*和*迁移*。

![Azure AD DS 的迁移过程概述](media/migrate-from-classic-vnet/migration-overview.png)

在*准备*阶段，Azure AD DS 会备份域，以获得与托管域同步的用户、组和密码的最新快照。 然后禁用同步，并删除承载 Azure AD DS 托管域的云服务。 在准备阶段，Azure AD DS 托管域无法对用户进行身份验证。

![迁移 Azure AD DS 的准备阶段](media/migrate-from-classic-vnet/migration-preparation.png)

在*迁移*阶段，将复制经典 Azure AD DS 托管域中域控制器的基础虚拟磁盘，以便使用资源管理器部署模型创建 VM。 然后重新创建 Azure AD DS 托管域，其中包括 LDAPS 和 DNS 配置。 重新启动与 Azure AD 的同步，并还原 LDAP 证书。 无需将任何计算机重新加入 Azure AD DS 托管域 - 它们继续加入托管域，并在不发生更改的情况下运行。

![Azure AD DS 的迁移](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>迁移好处

使用此迁移过程移动 Azure AD DS 托管域时，无需将计算机重新加入托管域或删除 Azure AD DS 实例并从头开始创建。 在迁移过程结束时，VM 将继续加入 Azure AD DS 托管域。

迁移后，Azure AD DS 提供了许多功能，这些功能仅适用于使用资源管理器虚拟网络的域，例如：

* 细粒度密码策略支持。
* AD 帐户锁定保护。
* 通过电子邮件发送 Azure AD DS 托管域上的警报通知。
* 使用 Azure 监视器审核日志。
* Azure 文件集成
* 高清洞察集成

使用资源管理器虚拟网络的 Azure AD DS 托管域可帮助您了解最新的新功能。 将来将弃用使用经典虚拟网络对 Azure AD DS 的支持。

## <a name="example-scenarios-for-migration"></a>迁移示例方案

迁移 Azure AD DS 托管域的一些常见方案包括以下示例。

> [!NOTE]
> 在确认迁移成功之前，不要转换经典虚拟网络。 转换虚拟网络将删除在迁移和验证阶段出现任何问题时回滚或还原 Azure AD DS 托管域的选项。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>将 Azure AD DS 迁移到现有资源管理器虚拟网络（建议）

常见方案是，您已经将其他现有经典资源移动到资源管理器部署模型和虚拟网络。 然后，从资源管理器虚拟网络到继续运行 Azure AD DS 的经典虚拟网络使用对等互连。 此方法允许资源管理器应用程序和服务在经典虚拟网络中使用 Azure AD DS 托管域的身份验证和管理功能。 迁移后，所有资源都使用资源管理器部署模型和虚拟网络运行。

![将 Azure AD DS 迁移到现有资源管理器虚拟网络](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

此示例迁移方案涉及的高级步骤包括以下部分：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移 Azure AD DS 托管域。
1. 测试并确认迁移成功，然后删除经典虚拟网络。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>迁移多个资源，包括 Azure AD DS

在此示例中，您将 Azure AD DS 和其他关联资源从经典部署模型迁移到资源管理器部署模型。 如果某些资源继续与 Azure AD DS 托管域一起在经典虚拟网络中运行，则它们都可以从迁移到资源管理器部署模型中获益。

![将多个资源迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

此示例迁移方案涉及的高级步骤包括以下部分：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移 Azure AD DS 托管域。
1. 在经典虚拟网络和资源管理器网络之间设置虚拟网络对等互连。
1. 测试并确认迁移成功。
1. [移动其他经典资源，如 VM。][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>迁移 Azure AD DS，但保留经典虚拟网络上的其他资源

使用此示例方案，您可以在一个会话中具有最少的停机时间。 您只将 Azure AD DS 迁移到资源管理器虚拟网络，并将现有资源保留在经典部署模型和虚拟网络上。 在随后的维护期间，您可以根据需要从经典部署模型和虚拟网络迁移其他资源。

![仅将 Azure AD DS 迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

此示例迁移方案涉及的高级步骤包括以下部分：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移 Azure AD DS 托管域。
1. 在经典虚拟网络和新的资源管理器虚拟网络之间设置虚拟网络对等。
1. 稍后，根据需要从经典虚拟网络[迁移其他资源][migrate-iaas]。

## <a name="before-you-begin"></a>在开始之前

在准备然后迁移 Azure AD DS 托管域时，有关身份验证和管理服务可用性的一些注意事项。 Azure AD DS 托管域在迁移期间一段时间内不可用。 依赖 Azure AD DS 的应用程序和服务在迁移期间遇到停机时间。

> [!IMPORTANT]
> 在开始迁移过程之前，请阅读所有这些迁移文章和指导。 迁移过程会影响 Azure AD DS 域控制器在一段时间内的可用性。 在迁移过程中，用户、服务和应用程序无法针对托管域进行身份验证。

### <a name="ip-addresses"></a>IP 地址

迁移后，Azure AD DS 托管域的域控制器 IP 地址会更改。 此更改包括安全 LDAP 终结点的公共 IP 地址。 新的 IP 地址位于资源管理器虚拟网络中新子网的地址范围内。

在回滚的情况下，IP 地址可能会在回滚后更改。

Azure AD DS 通常使用地址范围内的前两个可用 IP 地址，但不保证这样做。 当前无法指定迁移后要使用的 IP 地址。

### <a name="downtime"></a>故障时间

迁移过程涉及域控制器在一段时间内处于脱机状态。 当 Azure AD DS 迁移到资源管理器部署模型和虚拟网络时，无法访问域控制器。 平均而言，停机时间约为 1 到 3 小时。 此时间段从域控制器脱机到第一个域控制器重新联机的那一刻。 此平均值不包括第二个域控制器复制所需的时间，也不包括将其他资源迁移到资源管理器部署模型所需的时间。

### <a name="account-lockout"></a>帐户锁定

在经典虚拟网络上运行的 Azure AD DS 托管域没有 AD 帐户锁定策略。 如果 VM 被暴露在互联网上，攻击者可能会使用密码喷射方法将病毒强制强制进入帐户。 没有帐户锁定策略来阻止这些尝试。 对于使用资源管理器部署模型和虚拟网络的 Azure AD DS 托管域，AD 帐户锁定策略可防止这些密码喷射攻击。

默认情况下，在 2 分钟内尝试 5 次错误密码，将帐户锁定 30 分钟。

锁定的帐户不能用于登录，这可能会干扰管理 Azure AD DS 托管域或由帐户管理的应用程序的能力。 迁移 Azure AD DS 托管域后，由于多次尝试登录失败，帐户可能会体验到永久锁定的感觉。 迁移后的两种常见方案包括：

* 使用过期密码的服务帐户。
    * 服务帐户反复尝试使用过期密码登录，密码将锁定帐户。 要解决此问题，请找到凭据过期的应用程序或 VM 并更新密码。
* 恶意实体使用暴力尝试登录到帐户。
    * 当 VM 暴露到 Internet 时，攻击者通常会尝试使用通用用户名和密码组合进行签名。 这些重复的失败登录尝试可以锁定帐户。 不建议使用具有通用名称（例如*管理员*或*管理员*）的管理员帐户，以尽量减少将管理帐户锁定。
    * 尽量减少暴露到互联网的 VM 数量。 可以使用[Azure 堡垒][azure-bastion]使用 Azure 门户安全地连接到 VM。

如果您怀疑某些帐户可能在迁移后锁定，则最后迁移步骤将概述如何启用审核或更改细粒度的密码策略设置。

### <a name="roll-back-and-restore"></a>回滚和恢复

如果迁移不成功，则有回滚或还原 Azure AD DS 托管域的过程。 回滚是一种自助服务选项，用于在迁移尝试之前立即将托管域的状态返回到。 Azure 支持工程师还可以作为最后手段从备份还原托管域。 有关详细信息，请参阅[如何从失败的迁移回滚或还原](#roll-back-and-restore-from-migration)。

### <a name="restrictions-on-available-virtual-networks"></a>对可用虚拟网络的限制

Azure AD DS 托管域可以迁移到虚拟网络时存在一些限制。 目标资源管理器虚拟网络必须满足以下要求：

* 资源管理器虚拟网络必须与 Azure AD DS 当前部署的经典虚拟网络位于同一 Azure 订阅中。
* 资源管理器虚拟网络必须与 Azure AD DS 当前部署的经典虚拟网络位于同一区域。
* 资源管理器虚拟网络的子网应至少具有 3-5 个可用 IP 地址。
* 资源管理器虚拟网络的子网应该是 Azure AD DS 的专用子网，并且不应承载任何其他工作负荷。

有关虚拟网络要求的详细信息，请参阅[虚拟网络设计注意事项和配置选项][network-considerations]。

## <a name="migration-steps"></a>迁移步骤

迁移到资源管理器部署模型和虚拟网络分为 5 个主要步骤：

| 步骤    | 通过执行  | 估计时间  | 故障时间  | 回滚/还原？ |
|---------|--------------------|-----------------|-----------|-------------------|
| [第 1 步 - 更新并查找新的虚拟网络](#update-and-verify-virtual-network-settings) | Azure 门户 | 15 分钟 | 无需停机 | 空值 |
| [步骤 2 - 准备 Azure AD DS 托管域进行迁移](#prepare-the-managed-domain-for-migration) | PowerShell | 平均15~30分钟 | Azure AD DS 的停机时间在此命令完成后开始。 | 回滚并还原可用。 |
| [步骤 3 - 将 Azure AD DS 托管域移动到现有虚拟网络](#migrate-the-managed-domain) | PowerShell | 平均 1 ~ 3 小时 | 完成此命令后，一个域控制器可用，停机时间结束。 | 发生故障时，回滚（自助服务）和还原都可用。 |
| [步骤 4 - 测试并等待副本域控制器](#test-and-verify-connectivity-after-the-migration)| 电源外壳和 Azure 门户 | 1 小时或更长时间，具体取决于测试次数 | 两个域控制器都可用，应正常工作。 | 不适用。 成功迁移第一个 VM 后，没有回滚或还原的选项。 |
| [步骤 5 - 可选配置步骤](#optional-post-migration-configuration-steps) | Azure 门户和 VM | 空值 | 无需停机 | 空值 |

> [!IMPORTANT]
> 为了避免额外的停机时间，在开始迁移过程之前，请阅读所有这些迁移文章和指导。 迁移过程会影响 Azure AD DS 域控制器在一段时间内的可用性。 在迁移过程中，用户、服务和应用程序无法针对托管域进行身份验证。

## <a name="update-and-verify-virtual-network-settings"></a>更新和验证虚拟网络设置

在开始迁移过程之前，完成以下初始检查和更新。 这些步骤可以在迁移之前的任何时间发生，并且不会影响 Azure AD DS 托管域的操作。

1. 将本地 Azure PowerShell 环境更新为最新版本。 要完成迁移步骤，至少需要版本*2.3.2*。

    有关如何检查和更新 PowerShell 版本的信息，请参阅 Azure [PowerShell 概述][azure-powershell]。

1. 创建或选择现有的资源管理器虚拟网络。

    确保网络设置不会阻止 Azure AD DS 所需的必要端口。 必须在经典虚拟网络和资源管理器虚拟网络上打开端口。 这些设置包括路由表（尽管不建议使用路由表）和网络安全组。

    要查看所需的端口，请参阅[网络安全组和所需端口][network-ports]。 为了最小化网络通信问题，建议在迁移成功完成后等待并将网络安全组或路由表应用于资源管理器虚拟网络。

    记下此目标资源组、目标虚拟网络和目标虚拟网络子网。 这些资源名称在迁移过程中使用。

1. 检查 Azure 门户中的 Azure AD DS 托管域运行状况。 如果您有托管域的任何警报，则在开始迁移过程之前解决这些问题。
1. 或者，如果您计划将其他资源移动到资源管理器部署模型和虚拟网络，请确认可以迁移这些资源。 有关详细信息，请参阅[平台支持的 IaaS 资源从经典迁移到资源管理器][migrate-iaas]。

    > [!NOTE]
    > 不要将经典虚拟网络转换为资源管理器虚拟网络。 如果这样做，则没有回滚或还原 Azure AD DS 托管域的选项。

## <a name="prepare-the-managed-domain-for-migration"></a>准备托管域进行迁移

Azure PowerShell 用于准备 Azure AD DS 托管域进行迁移。 这些步骤包括执行备份、暂停同步和删除承载 Azure AD DS 的云服务。 完成此步骤后，Azure AD DS 将脱机一段时间。 如果准备步骤失败，您可以[回滚到以前的状态](#roll-back)。

要准备 Azure AD DS 托管域进行迁移，请完成以下步骤：

1. 从`Migrate-Aaads`[PowerShell 库][powershell-script]安装脚本。 此 PowerShell 迁移脚本是由 Azure AD 工程团队进行数字签名的。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. 使用[获取凭据][get-credential]cmdlet 创建一个变量以保存迁移脚本的凭据。

    指定的*用户帐户需要在*Azure AD 租户中启用 Azure AD DS，然后在 Azure 订阅中*启用参与者*权限以创建所需的 Azure AD DS 资源。

    出现提示时，输入相应的用户帐户和密码：

    ```powershell
    $creds = Get-Credential
    ```

1. 现在使用`Migrate-Aadds`*-Prepare*参数运行 cmdlet。 为自己的 Azure AD DS 托管域提供 *-托管域Fqdn，* 例如*aaddscontoso.com*：

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>迁移托管域

通过准备和备份 Azure AD DS 托管域，可以迁移域。 此步骤使用资源管理器部署模型重新创建 Azure AD 域服务域控制器 VM。 此步骤可能需要 1 到 3 小时才能完成。

使用`Migrate-Aadds`*-Commit*参数运行 cmdlet。 为上一节中准备自己的 Azure AD DS 托管域提供 *-托管域Fqdn，* 例如*aaddscontoso.com*：

指定包含要将 Azure AD DS 迁移到的目标资源组，例如*我的资源组*。 提供目标虚拟网络，如*myVnet*和子网，如*域服务*。

运行此命令后，您无法回滚：

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

在脚本验证托管域已准备好进行迁移后，输入*Y*以启动迁移过程。

> [!IMPORTANT]
> 在迁移过程中，不要将经典虚拟网络转换为资源管理器虚拟网络。 如果转换虚拟网络，则无法回滚或还原 Azure AD DS 托管域，因为原始虚拟网络将不复存在。

在迁移过程中，每两分钟一次，进度指示器报告当前状态，如以下示例输出所示：

![Azure AD DS 迁移的进度指示器](media/migrate-from-classic-vnet/powershell-migration-status.png)

即使您关闭了 PowerShell 脚本，迁移过程也会继续运行。 在 Azure 门户中，托管域的状态报告为*迁移*。

迁移成功完成后，您可以在 Azure 门户或通过 Azure PowerShell 查看第一个域控制器的 IP 地址。 还显示了第二个可用域控制器的时间估计值。

在此阶段，您可以选择从经典部署模型和虚拟网络移动其他现有资源。 或者，您可以将资源保留在经典部署模型上，并在 Azure AD DS 迁移完成后将虚拟网络彼此对等。

## <a name="test-and-verify-connectivity-after-the-migration"></a>迁移后测试和验证连接

第二个域控制器可能需要一些时间才能成功部署，并且可用于 Azure AD DS 托管域。

使用资源管理器部署模型，Azure AD DS 托管域的网络资源将显示在 Azure 门户或 Azure PowerShell 中。 要了解有关这些网络资源正在做什么和做什么的更多信息，请参阅[Azure AD DS 使用的网络资源][network-resources]。

当至少有一个域控制器可用时，完成以下配置步骤，以便与 VM 进行网络连接：

* **更新 DNS 服务器设置**要让资源管理器虚拟网络上的其他资源解析并使用 Azure AD DS 托管域，请使用新域控制器的 IP 地址更新 DNS 设置。 Azure 门户可以为您自动配置这些设置。 要了解有关如何配置资源管理器虚拟网络的详细信息，请参阅更新 Azure[虚拟网络的 DNS 设置][update-dns]。
* **重新启动加入域的 VM** - 当 Azure AD DS 域控制器的 DNS 服务器 IP 地址更改时，重新启动任何加入域的 VM，以便他们使用新的 DNS 服务器设置。 如果应用程序或 VM 已手动配置 DNS 设置，请使用 Azure 门户中显示的域控制器的新 DNS 服务器 IP 地址手动更新它们。

现在测试虚拟网络连接和名称解析。 在连接到资源管理器虚拟网络或对等互连的 VM 上，请尝试以下网络通信测试：

1. 检查是否可以 ping 其中一个域控制器的 IP 地址，例如`ping 10.1.0.4`
    * 域控制器的 IP 地址显示在 Azure 门户中的 Azure AD DS 托管域**的属性页上。**
1. 验证托管域的名称解析，例如`nslookup aaddscontoso.com`
    * 指定您自己的 Azure AD DS 托管域的 DNS 名称，以验证 DNS 设置是否正确并解析。

第二个域控制器应在迁移 cmdlet 完成后 1-2 小时可用。 要检查第二个域控制器是否可用，请查看 Azure 门户中的 Azure AD DS 托管域**的属性**页。 如果显示两个 IP 地址，则第二个域控制器已准备就绪。

## <a name="optional-post-migration-configuration-steps"></a>可选的迁移后配置步骤

迁移过程成功完成后，某些可选配置步骤包括启用审核日志或电子邮件通知，或更新细粒度密码策略。

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>使用 Azure 监视器订阅审核日志

Azure AD DS 公开审核日志，以帮助对域控制器上的事件进行故障排除和查看。 有关详细信息，请参阅[启用和使用审核日志][security-audits]。

您可以使用模板监视日志中公开的重要信息。 例如，审核日志工作簿模板可以监视 Azure AD DS 托管域上可能的帐户锁定。

### <a name="configure-azure-ad-domain-services-email-notifications"></a>配置 Azure AD 域服务电子邮件通知

要在 Azure AD DS 托管域上检测到问题时收到通知，请更新 Azure 门户中的电子邮件通知设置。 有关详细信息，请参阅[配置通知设置][notifications]。

### <a name="update-fine-grained-password-policy"></a>更新细粒度密码策略

如果需要，可以更新细粒度密码策略，以小于默认配置的限制。 您可以使用审核日志来确定限制较少的设置是否有意义，然后根据需要配置策略。 使用以下高级步骤查看和更新迁移后反复锁定的帐户的策略设置：

1. [配置密码策略][password-policy]，以减少对 Azure AD DS 托管域的限制，并观察审核日志中的事件。
1. 如果任何服务帐户使用审核日志中标识的过期密码，请使用正确的密码更新这些帐户。
1. 如果 VM 被公开到 Internet，请查看通用帐户名称，如*管理员*、*用户*或具有高登录尝试的*来宾*。 在可能的情况下，更新这些 VM 以使用不太通用的帐户。
1. 使用 VM 上的网络跟踪查找攻击源，并阻止这些 IP 地址尝试登录。
1. 当存在最少的锁定问题时，根据需要更新细粒度密码策略以进行严格操作。

### <a name="creating-a-network-security-group"></a>创建网络安全组

Azure AD DS 需要一个网络安全组来保护托管域所需的端口并阻止所有其他传入流量。 此网络安全组充当一个额外的保护层，用于锁定对托管域的访问，并且不会自动创建。 要创建网络安全组并打开所需的端口，请查看以下步骤：

1. 在 Azure 门户中，选择 Azure AD DS 资源。 在概览页上，如果没有与 Azure AD 域服务关联，则显示一个按钮以创建网络安全组。
1. 如果使用安全 LDAP，请向网络安全组添加规则以允许*TCP*端口*636*的传入流量。 有关详细信息，请参阅[配置安全 LDAP][secure-ldap]。

## <a name="roll-back-and-restore-from-migration"></a>回滚并从迁移中还原

在迁移过程中的某个点，您可以选择回滚或还原 Azure AD DS 托管域。

### <a name="roll-back"></a>回滚

如果在运行 PowerShell cmdlet 准备在步骤 2 中或步骤 3 中迁移本身时出现错误，则 Azure AD DS 托管域可以回滚到原始配置。 此回滚需要原始经典虚拟网络。 请注意，回滚后 IP 地址仍可能更改。

使用`Migrate-Aadds`*-Abort*参数运行 cmdlet。 为上一节中准备的 Azure AD DS 托管域 （如*aaddscontoso.com*） 和经典虚拟网络名称（如*myClassicVnet*） 提供 *-托管域域Fqdn：*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>还原

作为最后的手段，可以从上次可用的备份中还原 Azure AD 域服务。 迁移的步骤 1 中执行备份，以确保最新的备份可用。 此备份存储 30 天。

要从备份还原 Azure AD DS 托管域，[请使用 Azure 门户打开支持案例票证][azure-support]。 提供目录 ID、域名和还原原因。 支持和恢复过程可能需要多天时间才能完成。

## <a name="troubleshooting"></a>疑难解答

如果在迁移到资源管理器部署模型后出现问题，请查看以下一些常见故障排除区域：

* [解决域加入问题][troubleshoot-domain-join]
* [解决帐户锁定问题][troubleshoot-account-lockout]
* [解决帐户登录问题][troubleshoot-sign-in]
* [排除安全的 LDAP 连接问题][tshoot-ldaps]

## <a name="next-steps"></a>后续步骤

将 Azure AD DS 托管域迁移到资源管理器部署模型后，[创建和域加入 Windows VM，][join-windows]然后[安装管理工具][tutorial-create-management-vm]。

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
