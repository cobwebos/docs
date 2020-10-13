---
title: 从经典虚拟网络迁移 Azure AD 域服务 | Microsoft Docs
description: 了解如何将现有的 Azure AD 域服务托管域从经典虚拟网络模型迁移到基于资源管理器的虚拟网络。
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.author: joflore
ms.openlocfilehash: a66268c0cd0c2382b412873ec7f78b87d3491594
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968168"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>将 Azure Active Directory 域服务从经典虚拟网络模型迁移到资源管理器

Azure Active Directory 域服务 (Azure AD DS) 支持当前使用经典虚拟网络模型的客户一次性迁移到资源管理器虚拟网络模型。 使用资源管理器部署模型的 Azure AD DS 托管域提供了更多功能，例如，细化的密码策略、审核日志和帐户锁定保护。

本文概述了迁移注意事项，以及成功迁移现有托管域需要执行的步骤。 请参阅[在 Azure AD DS 中从经典部署模型迁移到资源管理器部署模型的优点][migration-benefits]，了解部分优点。

> [!NOTE]
> 在 2017 年，Azure AD 域服务变得可以承载在 Azure 资源管理器网络中。 自那时起，我们就能够使用 Azure 资源管理器的新式功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，因此 Azure AD DS 经典虚拟网络部署将于 2023 年 3 月 1 日停用。
>
> 有关详细信息，请查看[正式的弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

## <a name="overview-of-the-migration-process"></a>迁移过程概述

迁移过程接受在经典虚拟网络中运行的现有托管域将其移到现有的资源管理器虚拟网络。 迁移是使用 PowerShell 执行的，有两个主要的执行阶段：“准备”阶段和“迁移”阶段。

![Azure AD DS 的迁移过程概述](media/migrate-from-classic-vnet/migration-overview.png)

在“准备”阶段，Azure AD DS 会执行域备份，以将用户、组和密码的最新快照同步到托管域。 然后会禁用同步，并删除用于承载托管域的云服务。 在准备阶段，托管域无法对用户进行身份验证。

![Azure AD DS 迁移的“准备”阶段](media/migrate-from-classic-vnet/migration-preparation.png)

在“迁移”阶段，会将经典托管域的域控制器的底层虚拟磁盘进行复制，以便使用资源管理器部署模型创建 VM。 然后将重新创建托管域，其中包括 LDAPS 和 DNS 配置。 重启到 Azure AD 的同步，并还原 LDAP 证书。 无需将任何计算机重新加入托管域–它们会继续加入托管域并无需更改即可运行。

![迁移 Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>迁移方案示例

迁移托管域的一些常用方案包括以下示例。

> [!NOTE]
> 在确认迁移成功之前，请不要转换经典虚拟网络。 如果在迁移和验证阶段出现任何问题，则转换虚拟网络会删除回退或还原托管域的选项。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>将 Azure AD DS 迁移到现有的资源管理器虚拟网络（推荐）

一种常见情况是，你已将其他现有的经典资源移到资源管理器部署模型和虚拟网络。 然后使用对等互连从资源管理器虚拟网络连接到继续运行 Azure AD DS 的经典虚拟网络。 此方法允许资源管理器应用程序和服务使用经典虚拟网络中的托管域的身份验证和管理功能。 迁移后，所有资源都使用资源管理器部署模型和虚拟网络运行。

![将 Azure AD DS 迁移到现有的资源管理器虚拟网络](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

此示例迁移方案中涉及的大致步骤包括：

1. 删除在经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移托管域。
1. 测试并确认迁移是否成功，然后删除经典虚拟网络。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>迁移多个资源，包括 Azure AD DS

在此示例方案中，你将 Azure AD DS 和其他关联资源从经典部署模型迁移到资源管理器部署模型。 如果某些资源与托管域一起继续在经典虚拟网络中运行，则迁移到资源管理器部署模型后，它们都可以受益。

![将多个资源迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

此示例迁移方案中涉及的大致步骤包括：

1. 删除在经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移托管域。
1. 在经典虚拟网络与资源管理器网络之间设置虚拟网络对等互连。
1. 测试并确认迁移是否成功。
1. [移动 VM 之类的其他经典资源][migrate-iaas]。

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>迁移 Azure AD DS，但将其他资源保留在经典虚拟网络上

使用此示例方案时，单个会话中的停机时间最短。 仅将 Azure AD DS 迁移到资源管理器虚拟网络，将现有资源保留在经典部署模型和虚拟网络上。 在以下维护期内，你可以根据需要从经典部署模型和虚拟网络迁移其他资源。

![仅将 Azure AD DS 迁移到资源管理器部署模型](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

此示例迁移方案中涉及的大致步骤包括：

1. 删除在经典虚拟网络上配置的现有 VPN 网关或虚拟网络对等互连。
1. 使用本文中概述的步骤迁移托管域。
1. 在经典虚拟网络和新的资源管理器虚拟网络之间设置虚拟网络对等互连。
1. 稍后，根据需要从经典虚拟网络[迁移其他资源][migrate-iaas]。

## <a name="before-you-begin"></a>准备阶段

在准备和迁移托管域时，有一些有关身份验证和管理服务的可用性的注意事项。 在迁移过程中，托管域会有一段时间不可用。 在迁移过程中，依赖于 Azure AD DS 的应用程序和服务会遭遇停机。

> [!IMPORTANT]
> 在开始迁移过程之前，请阅读此迁移文章和指南的所有内容。 迁移过程会影响 Azure AD DS 域控制器在一段时间内的可用性。 在迁移过程中，用户、服务和应用程序无法通过托管域进行身份验证。

### <a name="ip-addresses"></a>IP 地址

托管域的域控制器 IP 地址在迁移后会更改。 此更改包括安全 LDAP 终结点的公共 IP 地址。 新的 IP 地址位于资源管理器虚拟网络中的新子网的地址范围内。

如果需要回退，则回退之后的 IP 地址可能会更改。

Azure AD DS 通常使用地址范围内的前两个可用 IP 地址，但不是一定使用。 目前无法指定在迁移后要使用的 IP 地址。

### <a name="downtime"></a>故障时间

在迁移过程中，域控制器会有一段时间处于脱机状态。 将 Azure AD DS 迁移到资源管理器部署模型和虚拟网络时，无法访问域控制器。

平均而言，停机时间大约为 1 到 3 小时。 此时间段是指从域控制器脱机那一刻到第一个域控制器恢复联机那一刻之间的时间。 此平均值不包括复制第二个域控制器所需的时间，也不包括将其他资源迁移到资源管理器部署模型所需的时间。

### <a name="account-lockout"></a>帐户锁定

在经典虚拟网络上运行的托管域没有实施 AD 帐户锁定策略。 如果 VM 向 Internet 公开，攻击者可能会利用密码喷射方法来强行使用帐户。 没有可停止这些尝试的帐户锁定策略。 对于使用资源管理器部署模型和虚拟网络的托管域，AD 帐户锁定策略可防范这些密码喷射攻击。

默认情况下，如果用户在 2 分钟内有 5 次密码尝试错误，则系统会将帐户锁定 30 分钟。

锁定的帐户不能用于登录，这可能会影响对帐户所管理的托管域或应用程序进行管理的能力。 迁移托管域后，帐户可能会遭遇感觉像因登录尝试反复失败而被永久锁定的情况。 迁移后的两种常见场景包括：

* 服务帐户使用过期的密码。
    * 服务帐户反复尝试使用过期的密码登录，这会锁定帐户。 若要解决此问题，请找到凭据已过期的应用程序或 VM，并更新密码。
* 恶意实体尝试通过暴力破解方式登录帐户。
    * 当 VM 向 Internet 公开时，攻击者通常会尝试使用常见的用户名和密码组合来登录。 如果这些登录尝试反复失败，系统可能会锁定相关帐户。 建议不要使用名称常见的管理员帐户（例如，admin 或 administrator 之类的帐户），以便将管理帐户被锁定的概率降到最低。
    * 尽量减少向 Internet 公开的 VM 数量。 可以通过 Azure 门户使用 [Azure Bastion][azure-bastion] 安全地连接到 VM。

如果担心某些帐户在迁移后被锁定，可以参阅最终的迁移步骤，其中概述了如何启用审核或更改细化的密码策略设置。

### <a name="roll-back-and-restore"></a>回退和还原

如果迁移未成功，可以通过相关过程回退或还原托管域。 回退是一个自助服务选项，可以让托管域立即回到尝试迁移之前的状态。 Azure 支持工程师还可以从备份中还原托管域，这是迫不得已才会采用的手段。 有关详细信息，请参阅[如何从失败的迁移回退或还原](#roll-back-and-restore-from-migration)。

### <a name="restrictions-on-available-virtual-networks"></a>对可用虚拟网络的限制

托管域可以迁移到的虚拟网络存在一些限制。 目标资源管理器虚拟网络必须满足以下要求：

* 资源管理器虚拟网络必须与当前部署了 Azure AD DS 的经典虚拟网络位于同一 Azure 订阅中。
* 资源管理器虚拟网络必须与当前部署了 Azure AD DS 的经典虚拟网络位于同一区域中。
* 资源管理器虚拟网络的子网应至少有 3-5 个可用的 IP 地址。
* 资源管理器虚拟网络的子网应该是 Azure AD DS 的专用子网，不应承载任何其他工作负荷。

有关虚拟网络要求的详细信息，请参阅[虚拟网络设计注意事项和配置选项][network-considerations]。

还必须创建网络安全组，以限制托管域的虚拟网络中的流量。 Azure 标准负载均衡器是在迁移过程中创建的，需要使用这些规则。 此网络安全组会保护 Azure AD DS，是托管域正常运行所需的。

有关需要哪些规则的详细信息，请参阅 [AZURE AD DS 网络安全组和所需端口](network-considerations.md#network-security-groups-and-required-ports)。

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>LDAPS 和 TLS/SSL 证书过期

如果为 LDAPS 配置了托管域，请确认当前的 TLS/SSL 证书的有效期超过30天。 在接下来的30天内过期的证书将导致迁移过程失败。 如果需要，请续订证书，并将其应用于托管域，然后开始迁移过程。

## <a name="migration-steps"></a>迁移步骤

迁移到资源管理器部署模型和虚拟网络的过程可拆分为 5 个主要步骤：

| 步骤    | 执行方式  | 估计时间  | 故障时间  | 回退/还原？ |
|---------|--------------------|-----------------|-----------|-------------------|
| [步骤 1 - 更新并找到新的虚拟网络](#update-and-verify-virtual-network-settings) | Azure 门户 | 15 分钟 | 无需停机 | 空值 |
| [步骤 2 - 准备要迁移的托管域](#prepare-the-managed-domain-for-migration) | PowerShell | 平均15到30分钟 | 此命令完成后，Azure AD DS 开始停机。 | 回退和还原功能可用。 |
| [步骤 3 - 将托管域移到现有虚拟网络](#migrate-the-managed-domain) | PowerShell | 平均时间为1到3小时 | 完成此命令后，一个域控制器将可用，停机结束。 | 出现故障时，回退（自助服务）和还原功能都可用。 |
| [步骤 4 - 测试并等待副本域控制器](#test-and-verify-connectivity-after-the-migration)| PowerShell 和 Azure 门户 | 1 小时或更长时间，具体取决于测试数量 | 两个域控制器都可用并应正常运行。 | 不适用。 成功迁移第一个 VM 后，就不会有回退或还原选项。 |
| [步骤 5 - 可选配置步骤](#optional-post-migration-configuration-steps) | Azure 门户和 VM | 空值 | 无需停机 | 空值 |

> [!IMPORTANT]
> 若要避免额外的停机时间，请在开始迁移过程之前，阅读此迁移文章和指南的所有内容。 迁移过程会影响 Azure AD DS 域控制器在一段时间内的可用性。 在迁移过程中，用户、服务和应用程序无法通过托管域进行身份验证。

## <a name="update-and-verify-virtual-network-settings"></a>更新和验证虚拟网络设置

在开始迁移过程之前，请完成以下初始检查和更新。 这些步骤可以在迁移之前的任何时间执行，不影响托管域的运行。

1. 将本地 Azure PowerShell 环境更新到最新版本。 若要完成迁移步骤，至少需要版本 2.3.2。

    若要了解如何检查和更新 PowerShell 版本，请参阅 [Azure PowerShell 概述][azure-powershell]。

1. 创建或选择现有的资源管理器虚拟网络。

    请确保网络设置不会阻止 Azure AD DS 所需的端口。 必须在经典虚拟网络和资源管理器虚拟网络上打开端口。 这些设置包括路由表（虽然不建议使用路由表）和网络安全组。

    Azure AD DS 需要使用网络安全组来保护托管域所需的端口，阻止所有其他的传入流量。 此网络安全组充当一层额外的保护措施，以锁定对托管域的访问。 若要查看必需的端口，请参阅[网络安全组和必需端口][network-ports]。

    如果使用安全 LDAP，请向网络安全组添加规则，以允许 TCP 端口 636 的传入流量。 有关详细信息，请参阅[通过 Internet 锁定安全 LDAP 访问](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)

    请记下此目标资源组、目标虚拟网络和目标虚拟网络子网。 在迁移过程中将使用这些资源名称。

1. 在 Azure 门户中检查托管域的运行状况。 如果存在关于托管域的任何警报，请在开始迁移过程之前解决它们。
1. （可选）如果你计划将其他资源移到资源管理器部署模型和虚拟网络，请确认这些资源可以迁移。 有关详细信息，请参阅[平台支持的从经典部署模型到资源管理器部署模型的 IaaS 资源迁移][migrate-iaas]。

    > [!NOTE]
    > 请勿将经典虚拟网络转换为资源管理器虚拟网络。 如果这样做，则没有回退或还原托管域的选项。

## <a name="prepare-the-managed-domain-for-migration"></a>准备要迁移的托管域

可使用 Azure PowerShell 准备要迁移的托管域。 这些步骤包括执行备份、暂停同步，以及删除承载 Azure AD DS 的云服务。 此步骤完成后，Azure AD DS 会在一段时间内处于脱机状态。 如果准备步骤失败，可以[回退到上一状态](#roll-back)。

若要准备要迁移的托管域，请完成以下步骤：

1. 从 [PowerShell 库][powershell-script]安装 `Migrate-Aaads` 脚本。 此 PowerShell 迁移脚本由 Azure AD 工程团队进行数字签名。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. 通过迁移脚本使用 [Get-Credential][get-credential] cmdlet 创建一个用于保存凭据的变量。

    指定的用户帐户需要在 Azure AD 租户中具有全局管理员权限以启用 Azure AD DS，需要在 Azure 订阅中具有参与者权限以创建所需的 Azure AD DS 资源。

    出现提示时，请输入相应的用户帐户和密码：

    ```powershell
    $creds = Get-Credential
    ```
    
1. 定义 Azure 订阅 ID 的变量。 如果需要，可以使用 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 来列出和查看订阅 ID。 在以下命令中提供自己的订阅 ID：

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. 现在使用 -Prepare 参数运行 `Migrate-Aadds` cmdlet。 提供你自己的托管域的 -ManagedDomainFqdn，例如 aaddscontoso.com：

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>迁移托管域

准备并备份托管域后，可以迁移该域。 此步骤使用资源管理器部署模型重新创建 Azure AD DS 域控制器 VM。 此步骤可能需要 1 到 3 小时才能完成。

使用 -Commit 参数运行 `Migrate-Aadds` cmdlet。 提供你在上一部分中准备的托管域的 -ManagedDomainFqdn，例如 aaddscontoso.com：

指定你要将 Azure AD DS 迁移到的虚拟网络所在的目标资源组，例如 myResourceGroup。 提供目标虚拟网络（例如 myVnet）和子网（例如 DomainServices）。

运行此命令后，将无法回退：

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

在脚本验证托管域已准备好进行迁移后，输入 Y 开始迁移过程。

> [!IMPORTANT]
> 在迁移过程中，请勿将经典虚拟网络转换为资源管理器虚拟网络。 如果转换虚拟网络，则无法回退或还原托管域，因为原始虚拟网络将不再存在。

在迁移过程中，进度指示器每两分钟报告一次当前状态，如以下示例输出所示：

![Azure AD DS 迁移的进度指示器](media/migrate-from-classic-vnet/powershell-migration-status.png)

即使关闭了 PowerShell 脚本，迁移过程也会继续运行。 在 Azure 门户中，托管域的状态报告为“正在迁移”。

成功完成迁移后，可以通过 Azure 门户或 Azure PowerShell 查看第一个域控制器的 IP 地址。 还可以查看第二个域控制器可用的时间（估计）。

在此阶段，你可以选择移动经典部署模型和虚拟网络中的其他现有资源。 也可以在 Azure AD DS 迁移完成后将资源保留在经典部署模型中，然后将虚拟网络对等互连。

## <a name="test-and-verify-connectivity-after-the-migration"></a>迁移后测试并验证连接性

第二个域控制器可能需要一些时间才能部署成功并用于托管域。

使用资源管理器部署模型时，托管域的网络资源将显示在 Azure 门户或 Azure PowerShell 中。 若要详细了解这些网络资源是什么以及有什么作用，请参阅 [Azure AD DS 使用的网络资源][network-resources]。

如果至少有一个域控制器可用，请完成以下配置步骤，以使用 VM 进行网络连接：

* **更新 DNS 服务器设置** 为了让资源管理器虚拟网络上的其他资源能够解析并使用托管域，请使用新的域控制器的 IP 地址更新 DNS 设置。 Azure 门户可以自动为你配置这些设置。

    若要详细了解如何配置资源管理器虚拟网络，请参阅[更新 Azure 虚拟网络的 DNS 设置][update-dns]。
* **重启已加入域的 VM** - 因为 Azure AD DS 域控制器的 DNS 服务器 IP 地址发生更改，请重启任何已加入域的 VM，以便它们使用新的 DNS 服务器设置。 如果应用程序或 VM 具有手动配置的 DNS 设置，请使用 Azure 门户中显示的域控制器的新 DNS 服务器 IP 地址手动更新它们。

现在，请测试虚拟网络连接和名称解析。 在已连接到资源管理器虚拟网络或已对等互连到该虚拟网络的 VM 上，尝试以下网络通信测试：

1. 检查是否可以对其中一个域控制器的 IP 地址进行 ping 操作，例如 `ping 10.1.0.4`
    * 域控制器的 IP 地址显示在 Azure 门户中托管域的“属性”页上。
1. 验证托管域的名称解析，例如 `nslookup aaddscontoso.com`
    * 指定你自己的托管域的 DNS 名称，以验证 DNS 设置是否正确以及是否可以解析。

该迁移 cmdlet 完成后，第二个域控制器应在 1-2 小时内可用。 若要查看第二个域控制器是否可用，请在 Azure 门户中查看该托管域的“属性”页。 如果显示了两个 IP 地址，则表明第二个域控制器已准备就绪。

## <a name="optional-post-migration-configuration-steps"></a>可选的迁移后配置步骤

成功完成迁移过程后，可以执行一些可选的配置步骤，其中包括启用审核日志或电子邮件通知，或者更新细化的密码策略。

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>使用 Azure Monitor 订阅审核日志

Azure AD DS 公开了审核日志，方便用户排查和查看域控制器上的事件。 有关详细信息，请参阅[启用和使用审核日志][security-audits]。

可以使用模板来监视日志中公开的重要信息。 例如，审核日志工作簿模板可以监视托管域上可能存在的帐户锁定。

### <a name="configure-email-notifications"></a>配置电子邮件通知

如果希望在托管域上检测到问题时收到通知，请在 Azure 门户中更新电子邮件通知设置。 有关详细信息，请参阅[配置通知设置][notifications]。

### <a name="update-fine-grained-password-policy"></a>更新细化的密码策略

如果需要，你可以更新细化的密码策略以使其限制性低于默认配置。 可以使用审核日志来确定限制性较低的设置是否合理，然后根据需要配置策略。 使用以下大致步骤查看和更新在迁移后反复锁定的帐户的策略设置：

1. [配置密码策略][password-policy]，以便减少对托管域的限制，并观察审核日志中的事件。
1. 如果任何服务帐户使用了审核日志中识别为过期的密码，请使用正确的密码更新这些帐户。
1. 如果向 Internet 公开了某个 VM，请查看你是否使用了攻击者进行登录尝试时通常会使用的常规帐户名称，例如 administrator、user 或 guest。 请尽可能更新这些 VM，以使用具有不常用名称的帐户。
1. 使用 VM 上的网络跟踪来查找攻击源，阻止这些 IP 地址进行登录尝试。
1. 尽量减少锁定问题后，根据需要更新细化的密码策略，使其尽量严格。

## <a name="roll-back-and-restore-from-migration"></a>从迁移回退和还原

可以选择将托管域回退或还原到迁移过程中的某个时间点。

### <a name="roll-back"></a>回退

如果在步骤 2 中运行 PowerShell cmdlet 以进行迁移准备时出现错误，或者在步骤 3 中迁移本身出现错误，则托管域可以回退到原始配置。 此回退需要原始的经典虚拟网络。 回退之后的 IP 地址仍然可能会更改。

使用 -Abort 参数运行 `Migrate-Aadds` cmdlet。 提供在上一部分中准备的托管域的 -ManagedDomainFqdn（如 aaddscontoso.com），并提供经典虚拟网络名称（如 myClassicVnet）：

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>还原

可以从最后的可用备份中还原 Azure AD 域服务，这是迫不得已才会采用的手段。 在迁移的步骤 1 中执行备份，以确保可以使用最新的备份。 此备份存储 30 天。

若要从备份中还原托管域，请[使用 Azure 门户创建支持案例票证][azure-support]。 提供与还原相关的目录 ID、域名和原因。 支持和还原过程可能需要多日才能完成。

## <a name="troubleshooting"></a>故障排除

如果迁移到资源管理器部署模型后遇到问题，请查看下面一些常见故障排除方面的内容：

* [排查域加入问题][troubleshoot-domain-join]
* [排查帐户锁定问题][troubleshoot-account-lockout]
* [排查帐户登录问题][troubleshoot-sign-in]
* [排查安全 LDAP 连接问题][tshoot-ldaps]

## <a name="next-steps"></a>后续步骤

将托管域迁移到资源管理器部署模型后，请[创建 Windows VM 并将其加入域][join-windows]，然后[安装管理工具][tutorial-create-management-vm]。

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
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
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
