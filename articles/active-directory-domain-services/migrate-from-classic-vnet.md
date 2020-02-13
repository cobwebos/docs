---
title: 从经典虚拟网络迁移 Azure AD 域服务 |Microsoft Docs
description: 了解如何将现有 Azure AD 域服务托管域实例从经典虚拟网络模型迁移到基于资源管理器的虚拟网络。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: bd20bb008c52b7d99416aed7a0599a6e78d2acf2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161641"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器

对于当前使用经典虚拟网络模型到资源管理器虚拟网络模型的客户，Azure Active Directory 域服务（AD DS）支持一次移动。 使用资源管理器部署模型 Azure AD DS 托管域提供其他功能，例如细化密码策略、审核日志和帐户锁定保护。

本文概述了迁移的优点和注意事项，以及成功迁移现有 Azure AD DS 实例所需的步骤。

## <a name="overview-of-the-migration-process"></a>迁移过程概述

迁移过程采用在经典虚拟网络中运行的现有 Azure AD DS 实例，并将其移动到现有的资源管理器虚拟网络中。 使用 PowerShell 执行迁移，并有两个主要阶段执行*准备*和*迁移*。

![Azure AD DS 的迁移过程概述](media/migrate-from-classic-vnet/migration-overview.png)

在*准备*阶段，Azure AD DS 会对域进行备份，以获取同步到托管域的用户、组和密码的最新快照。 然后，将禁用同步，同时会删除承载 Azure AD DS 托管域的云服务。 在准备阶段，Azure AD DS 托管域无法对用户进行身份验证。

![迁移 Azure AD DS 的准备阶段](media/migrate-from-classic-vnet/migration-preparation.png)

在*迁移*阶段，将复制经典 Azure AD DS 托管域中的域控制器的底层虚拟磁盘，以使用资源管理器部署模型创建 vm。 然后重新创建 Azure AD DS 托管域，其中包括 LDAPS 和 DNS 配置。 重新启动与 Azure AD 的同步，并还原 LDAP 证书。 无需将任何计算机重新加入到 Azure AD DS 托管域，它们会继续加入托管域并无需更改即可运行。

![迁移 Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>迁移权益

使用此迁移过程移动 Azure AD DS 托管域时，无需将计算机重新加入托管域或删除 Azure AD DS 实例，并从头开始创建一个。 迁移过程结束时，Vm 将继续加入到 Azure AD DS 托管域。

迁移后，Azure AD DS 提供了许多功能，这些功能仅适用于使用资源管理器虚拟网络的域，例如：

* 细化密码策略支持。
* AD 帐户锁定保护。
* Azure AD DS 托管域上的警报的电子邮件通知。
* 使用 Azure Monitor 审核日志。
* Azure 文件集成
* HD Insights 集成

使用资源管理器虚拟网络 Azure AD DS 托管域可帮助你随时了解最新的新功能。 以后将不再支持使用经典虚拟网络的 Azure AD DS。

## <a name="example-scenarios-for-migration"></a>迁移的示例方案

迁移 Azure AD DS 托管域的常见方案包括以下示例。

> [!NOTE]
> 在确认迁移成功之前，请不要转换经典虚拟网络。 如果在迁移和验证阶段出现任何问题，转换虚拟网络将删除回滚或还原 Azure AD DS 托管域的选项。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>将 Azure AD DS 迁移到现有资源管理器虚拟网络（推荐）

常见的情况是，你已将其他现有经典资源移到资源管理器部署模型和虚拟网络。 然后，将使用对等互连从资源管理器虚拟网络到经典虚拟网络，该虚拟网络 Azure AD DS 继续运行。 此方法允许资源管理器的应用程序和服务在经典虚拟网络中使用 Azure AD DS 托管域的身份验证和管理功能。 迁移后，将使用资源管理器部署模型和虚拟网络运行所有资源。

![将 Azure AD DS 迁移到现有资源管理器虚拟网络](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

此示例迁移方案中涉及的高级步骤包括：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中所述的步骤迁移 Azure AD DS 托管域。
1. 测试并确认迁移是否成功，然后删除经典虚拟网络。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>迁移多个资源，包括 Azure AD DS

在此示例方案中，你将 Azure AD DS 和其他关联资源从经典部署模型迁移到资源管理器部署模型。 如果某些资源与 Azure AD DS 托管域一起继续在经典虚拟网络中运行，则在迁移到资源管理器部署模型时，这些资源都可以受益。

![将多个资源迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

此示例迁移方案中涉及的高级步骤包括：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中所述的步骤迁移 Azure AD DS 托管域。
1. 在经典虚拟网络和资源管理器网络之间设置虚拟网络对等互连。
1. 测试并确认迁移是否成功。
1. [移动 vm 等其他经典资源][migrate-iaas]。

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>迁移 Azure AD DS 但在经典虚拟网络上保留其他资源

在此示例方案中，一个会话中的停机时间最短。 仅将 Azure AD DS 迁移到资源管理器虚拟网络，并将现有资源保留在经典部署模型和虚拟网络上。 在以下维护期内，你可以根据需要从经典部署模型和虚拟网络迁移其他资源。

![仅将 Azure AD DS 迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

此示例迁移方案中涉及的高级步骤包括：

1. 删除经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中所述的步骤迁移 Azure AD DS 托管域。
1. 在经典虚拟网络与新资源管理器虚拟网络之间设置虚拟网络对等互连。
1. 稍后，根据需要从经典虚拟网络[迁移其他资源][migrate-iaas]。

## <a name="before-you-begin"></a>开始之前

准备并迁移 Azure AD DS 托管域时，有一些有关身份验证和管理服务可用性的注意事项。 在迁移过程中，Azure AD DS 托管域在一段时间内不可用。 依赖于在迁移过程中 Azure AD DS 体验停机的应用程序和服务。

> [!IMPORTANT]
> 在开始迁移过程之前，请阅读所有此迁移文章和指南。 迁移过程会影响 Azure AD DS 域控制器在一段时间内的可用性。 在迁移过程中，用户、服务和应用程序无法对托管域进行身份验证。

### <a name="ip-addresses"></a>IP 地址

迁移后，Azure AD DS 托管域的域控制器 IP 地址将更改。 此更改包括安全 LDAP 终结点的公共 IP 地址。 新的 IP 地址位于资源管理器虚拟网络中的新子网的地址范围内。

如果是回滚，则在回滚之后 IP 地址可能会更改。

Azure AD DS 通常使用地址范围内的前两个可用 IP 地址，但这并不保证。 你当前无法指定要在迁移后使用的 IP 地址。

### <a name="downtime"></a>故障时间

迁移过程涉及域控制器在一段时间内处于脱机状态。 当 Azure AD DS 迁移到资源管理器部署模型和虚拟网络时，无法访问域控制器。 平均情况下，停机时间大约为1到3小时。 此时间段是从域控制器脱机到第一个域控制器恢复联机的那一刻。 此平均值不包括复制第二个域控制器所需的时间，也不包括将其他资源迁移到资源管理器部署模型所需的时间。

### <a name="account-lockout"></a>帐户锁定

在经典虚拟网络上运行 Azure AD DS 托管域不存在 AD 帐户锁定策略。 如果 Vm 向 internet 公开，攻击者可能会利用密码喷涂方法来强行使用帐户。 没有帐户锁定策略来停止这些尝试。 对于使用资源管理器部署模型和虚拟网络的 Azure AD DS 托管域，AD 帐户锁定策略可防范这些密码喷涂攻击。

默认情况下，在2分钟内尝试执行5次错误密码会锁定帐户30分钟。

锁定的帐户不能用于登录，这可能会影响管理由帐户管理的 Azure AD DS 托管域或应用程序的能力。 迁移 Azure AD DS 托管域后，帐户可能会由于登录失败的重复尝试而感觉到永久性锁定。 迁移后的两种常见方案包括：

* 使用过期密码的服务帐户。
    * 服务帐户会反复尝试使用过期密码登录，这会锁定帐户。 若要解决此问题，请找到凭据过期的应用程序或 VM，并更新密码。
* 恶意实体正在使用强行登录帐户。
    * 当 Vm 向 internet 公开时，攻击者通常会尝试进行签名时常见的用户名和密码组合。 这些重复的失败登录尝试可能会锁定帐户。 建议不要使用具有通用名称（如*管理员*或*管理员*）的管理员帐户，例如，将管理帐户从锁定状态降到最低。
    * 最大程度地减少向 internet 公开的 Vm 数量。 可以使用[Azure 堡垒][azure-bastion]通过 Azure 门户安全地连接到 vm。

如果怀疑迁移后某些帐户可能被锁定，最终的迁移步骤概述如何启用审核或更改细化密码策略设置。

### <a name="roll-back-and-restore"></a>回滚和还原

如果迁移未成功，则需要回滚或还原 Azure AD DS 托管域。 Rollback 是一项自助服务选项，可立即将托管域的状态返回到迁移尝试之前。 Azure 支持工程师还可以从备份中还原托管域，这是最后一种手段。 有关详细信息，请参阅[如何从失败的迁移回滚或还原](#roll-back-and-restore-from-migration)。

### <a name="restrictions-on-available-virtual-networks"></a>对可用虚拟网络的限制

Azure AD DS 托管域可以迁移到的虚拟网络有一些限制。 资源管理器虚拟网络的目标必须满足以下要求：

* 资源管理器虚拟网络必须与当前部署 Azure AD DS 的经典虚拟网络位于同一 Azure 订阅中。
* 资源管理器虚拟网络必须与当前部署 Azure AD DS 的经典虚拟网络位于同一区域。
* 资源管理器虚拟网络的子网应至少具有3-5 个可用的 IP 地址。
* 资源管理器虚拟网络的子网应为 Azure AD DS 的专用子网，并且不应托管任何其他工作负荷。

有关虚拟网络要求的详细信息，请参阅[虚拟网络设计注意事项和配置选项][network-considerations]。

## <a name="migration-steps"></a>迁移步骤

迁移到资源管理器部署模型和虚拟网络的步骤分为5个主要步骤：

| 步骤    | 执行  | 估计时间  | 故障时间  | 要回滚/还原？ |
|---------|--------------------|-----------------|-----------|-------------------|
| [步骤 1-更新并找到新的虚拟网络](#update-and-verify-virtual-network-settings) | Azure 门户 | 15 分钟 | 无需停机 | 空值 |
| [步骤 2-为迁移准备 Azure AD DS 托管域](#prepare-the-managed-domain-for-migration) | PowerShell | 平均15到30分钟 | 此命令完成后，将启动 Azure AD DS 的停机时间。 | 回滚并可用还原。 |
| [步骤 3-将 Azure AD DS 托管域移动到现有虚拟网络](#migrate-the-managed-domain) | PowerShell | 平均时间为1到3小时 | 当完成此命令后，一个域控制器可用，中断时间结束。 | 出现故障时，回滚（自助服务）和还原都可用。 |
| [步骤 4-测试并等待副本域控制器](#test-and-verify-connectivity-after-the-migration)| PowerShell 和 Azure 门户 | 1小时或更多，具体取决于测试数量 | 这两个域控制器都可用并且应正常工作。 | 不适用。 成功迁移第一个 VM 后，就不会有回滚或还原选项。 |
| [步骤 5-可选配置步骤](#optional-post-migration-configuration-steps) | Azure 门户和 Vm | 空值 | 无需停机 | 空值 |

> [!IMPORTANT]
> 若要避免额外的停机时间，请在开始迁移过程之前阅读此迁移文章和指南。 迁移过程会在一段时间内影响 Azure AD DS 域控制器的可用性。 在迁移过程中，用户、服务和应用程序无法对托管域进行身份验证。

## <a name="update-and-verify-virtual-network-settings"></a>更新并验证虚拟网络设置

在开始迁移过程之前，请完成以下初始检查和更新。 这些步骤可在迁移之前的任何时间发生，并且不会影响 Azure AD DS 托管域的操作。

1. 将本地 Azure PowerShell 环境更新到最新版本。 若要完成迁移步骤，至少需要*2.3.2*版本。

    有关如何检查和更新 PowerShell 版本的信息，请参阅[Azure PowerShell 概述][azure-powershell]。

1. 创建或选择现有资源管理器虚拟网络。

    请确保 "网络设置" 不会阻止 Azure AD DS 所需的端口。 必须在经典虚拟网络和资源管理器虚拟网络上打开端口。 这些设置包括路由表（尽管不建议使用路由表）和网络安全组。

    若要查看所需端口，请参阅[网络安全组和所需的端口][network-ports]。 若要最大程度地减少网络通信问题，建议在迁移成功完成后，等待并将网络安全组或路由表应用到资源管理器虚拟网络。

    记下目标资源组、目标虚拟网络和目标虚拟网络子网。 在迁移过程中将使用这些资源名称。

1. 检查 Azure 门户中 Azure AD DS 托管域的运行状况。 如果你对托管域具有任何警报，请在开始迁移过程之前解决它们。
1. （可选）如果你计划将其他资源移到资源管理器部署模型和虚拟网络，请确认可以迁移这些资源。 有关详细信息，请参阅[平台支持的从经典部署模型到资源管理器的 IaaS 资源迁移][migrate-iaas]。

    > [!NOTE]
    > 请勿将经典虚拟网络转换为资源管理器虚拟网络。 如果这样做，则没有回滚或还原 Azure AD DS 托管域的选项。

## <a name="prepare-the-managed-domain-for-migration"></a>准备托管域以进行迁移

Azure PowerShell 用于为迁移准备 Azure AD DS 托管域。 这些步骤包括执行备份，暂停同步，以及删除 Azure AD DS 承载的云服务。 此步骤完成时，Azure AD DS 在一段时间内处于脱机状态。 如果准备步骤失败，可以[回滚到以前的状态](#roll-back)。

若要为迁移准备 Azure AD DS 托管域，请完成以下步骤：

1. 从[PowerShell 库][powershell-script]安装 `Migrate-Aaads` 脚本。 此 PowerShell 迁移脚本由 Azure AD 工程团队进行数字签名。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. 使用[Get Credential][get-credential] cmdlet 创建一个变量来保存迁移脚本的凭据。

    指定的用户帐户需要 Azure AD 租户中的*全局管理员*特权，才能在 Azure 订阅中启用 Azure AD DS 和*参与者*权限，以创建所需的 Azure AD DS 资源。

    出现提示时，请输入相应的用户帐户和密码：

    ```powershell
    $creds = Get-Credential
    ```

1. 现在，使用 *-Prepare*参数运行 `Migrate-Aadds` cmdlet。 为自己 Azure AD DS 托管域提供 *-ManagedDomainFqdn* ，例如*contoso.com*：

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>迁移托管域

准备并备份 Azure AD DS 托管域后，可以迁移域。 此步骤使用资源管理器部署模型重新创建 Azure AD 域服务域控制器 Vm。 此步骤可能需要1到3小时才能完成。

使用 *-Commit*参数运行 `Migrate-Aadds` cmdlet。 为在上一节中准备好 Azure AD DS 托管域提供 *-ManagedDomainFqdn* ，例如*contoso.com*：

指定包含要迁移 Azure AD DS 的虚拟网络的目标资源组，如*myResourceGroup*。 提供目标虚拟网络（如*myVnet*）和子网（如*DomainServices*）。

运行此命令后，将无法回滚：

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

在脚本验证托管域是否准备好进行迁移后，输入*Y*开始迁移过程。

> [!IMPORTANT]
> 请不要在迁移过程中将经典虚拟网络转换为资源管理器的虚拟网络。 如果转换虚拟网络，则无法回退或还原 Azure AD DS 托管域，因为原始虚拟网络不再存在。

在迁移过程中每两分钟一次，进度指示器将报告当前状态，如以下示例输出所示：

![Azure AD DS 迁移的进度指示器](media/migrate-from-classic-vnet/powershell-migration-status.png)

即使关闭 PowerShell 脚本，迁移过程也会继续运行。 在 Azure 门户中，托管域的状态报告为 "正在*迁移*"。

成功完成迁移后，可以在 Azure 门户或 Azure PowerShell 中查看第一个域控制器的 IP 地址。 还显示了第二个可用的域控制器上的时间估算。

在此阶段，你可以选择移动经典部署模型和虚拟网络中的其他现有资源。 或者，你可以在 Azure AD DS 迁移完成后将资源保留在经典部署模型中，并将虚拟网络对等互连。

## <a name="test-and-verify-connectivity-after-the-migration"></a>迁移后测试并验证连接

第二个域控制器可能需要一些时间才能成功部署，并可在 Azure AD DS 托管域中使用。

利用资源管理器部署模型，Azure AD DS 托管域的网络资源显示在 Azure 门户或 Azure PowerShell 中。 若要详细了解这些网络资源的作用，请参阅[AZURE AD DS 使用的网络资源][network-resources]。

如果至少有一个域控制器可用，请完成以下配置步骤以使用 Vm 进行网络连接：

* **更新 DNS 服务器设置**若要让资源管理器虚拟网络上的其他资源解析和使用 Azure AD DS 托管域，请使用新域控制器的 IP 地址更新 DNS 设置。 Azure 门户可以自动为你配置这些设置。 若要了解有关如何配置资源管理器虚拟网络的详细信息，请参阅[更新 Azure 虚拟网络的 DNS 设置][update-dns]。
* **重新启动已加入域的 vm** -作为 Azure AD DS 域控制器的 DNS 服务器 IP 地址发生变化，重新启动任何已加入域的 vm，以便它们使用新的 DNS 服务器设置。 如果应用程序或 Vm 已手动配置 DNS 设置，请使用 Azure 门户中显示的域控制器的新 DNS 服务器 IP 地址手动更新它们。

现在，请测试虚拟网络连接和名称解析。 在连接到资源管理器虚拟网络或对等互连的 VM 上，尝试以下网络通信测试：

1. 检查是否可以对其中一个域控制器的 IP 地址进行 ping 操作，例如 `ping 10.1.0.4`
    * 域控制器的 IP 地址显示在 Azure 门户中 Azure AD DS 托管域的 "**属性**" 页上。
1. 验证托管域的名称解析，如 `nslookup contoso.com`
    * 指定自己 Azure AD DS 托管域的 DNS 名称，以验证 DNS 设置是否正确以及是否可解决。

迁移 cmdlet 完成后，第二个域控制器应在1-2 小时内可用。 若要查看第二个域控制器是否可用，请查看 Azure 门户中 Azure AD DS 托管域的**属性**页。 如果显示了两个 IP 地址，则第二个域控制器已准备就绪。

## <a name="optional-post-migration-configuration-steps"></a>可选迁移后配置步骤

成功完成迁移过程后，一些可选的配置步骤包括启用审核日志或电子邮件通知，或者更新细化密码策略。

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>使用 Azure Monitor 订阅审核日志

Azure AD DS 公开了审核日志，以帮助排查和查看域控制器上的事件。 有关详细信息，请参阅[启用和使用审核日志][security-audits]。

您可以使用模板来监视日志中公开的重要信息。 例如，"审核日志" 工作簿模板可以监视 Azure AD DS 托管域上的可能帐户锁定。

### <a name="configure-azure-ad-domain-services-email-notifications"></a>配置 Azure AD 域服务电子邮件通知

若要在 Azure AD DS 托管域上检测到问题时收到通知，请更新 Azure 门户中的电子邮件通知设置。 有关详细信息，请参阅[配置通知设置][notifications]。

### <a name="update-fine-grained-password-policy"></a>更新细化密码策略

如果需要，可以将严格的密码策略更新为低于默认配置。 您可以使用审核日志来确定限制性较低的设置是否合理，然后根据需要配置策略。 使用以下高级步骤来查看和更新迁移后重复锁定的帐户的策略设置：

1. [配置密码策略][password-policy]以减少对 Azure AD DS 托管域的限制，并观察审核日志中的事件。
1. 如果任何服务帐户使用审核日志中标识的过期密码，请使用正确的密码更新这些帐户。
1. 如果向 internet 公开了 VM，请查看一般帐户名称（如*管理员*、*用户*或*来宾*），登录尝试次数很高。 如果可能，请更新这些虚拟机以使用不太常用的命名帐户。
1. 使用 VM 上的网络跟踪查找攻击源，并阻止这些 IP 地址尝试登录。
1. 如果出现最少的锁定问题，请根据需要将细化密码策略更新为严格限制。

### <a name="creating-a-network-security-group"></a>创建网络安全组

Azure AD DS 需要使用网络安全组来保护托管域所需的端口，并阻止其他所有传入流量。 此网络安全组充当一层额外的保护措施，用于锁定对托管域的访问，但不会自动创建。 若要创建网络安全组并打开所需的端口，请查看以下步骤：

1. 在 Azure 门户中，选择 Azure AD DS 资源。 在 "概述" 页上，如果没有与 Azure AD 域服务相关联，则会显示一个按钮来创建网络安全组。
1. 如果使用安全 LDAP，请将规则添加到网络安全组，以允许*TCP*端口*636*的传入流量。 有关详细信息，请参阅[配置安全 LDAP][secure-ldap]。

## <a name="roll-back-and-restore-from-migration"></a>回退并从迁移还原

在迁移过程中，你可以选择回滚或还原 Azure AD DS 托管域。

### <a name="roll-back"></a>回滚

如果在步骤2中运行 PowerShell cmdlet 准备迁移时出现错误，或者在步骤3中的迁移本身中出现错误，则 Azure AD DS 托管域可回滚到原始配置。 此回滚需要原始经典虚拟网络。 请注意，在回滚后，IP 地址可能仍会更改。

使用 *-Abort*参数运行 `Migrate-Aadds` cmdlet。 为您自己的 Azure AD DS 托管域提供 *-ManagedDomainFqdn* ，该域是在上一节中准备的，如*contoso.com*和经典虚拟网络名称，例如*myClassicVnet*：

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>还原

作为最后的手段，可以从上一次可用备份中还原 Azure AD 的域服务。 在迁移的步骤1中执行备份，以确保最新的备份可用。 此备份存储30天。

若要从备份中还原 Azure AD DS 托管域，请[使用 Azure 门户打开支持案例票证][azure-support]。 提供要还原的目录 ID、域名和原因。 支持和还原过程可能需要几天才能完成。

## <a name="troubleshooting"></a>故障排除

如果迁移到资源管理器部署模型后遇到问题，请查看以下常见故障排除区域：

* [排查域加入问题][troubleshoot-domain-join]
* [帐户锁定问题的疑难解答][troubleshoot-account-lockout]
* [排查帐户登录问题][troubleshoot-sign-in]
* [排查安全 LDAP 连接问题][tshoot-ldaps]

## <a name="next-steps"></a>后续步骤

将 Azure AD DS 托管域迁移到资源管理器部署模型，[创建 WINDOWS VM 并将其加入域][join-windows]，然后[安装管理工具][tutorial-create-management-vm]。

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
