---
title: 使用专用终结点为本地计算机启用复制
description: 本文介绍如何使用 Site Recovery 中的专用终结点配置本地计算机复制。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 3d15f4039da85dfa926e7bc9ab96b2c48965d5f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658800"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>使用专用终结点复制本地计算机

Azure Site Recovery 支持使用 [Azure 专用链接](../private-link/private-endpoint-overview.md)专用终结点将本地计算机复制到 Azure 中的虚拟网络。 所有 Azure 商业 & 政府地区都支持对恢复保管库的专用终结点访问。

本文介绍如何完成以下步骤：

- 创建 Azure 备份恢复服务保管库来保护计算机。
- 为保管库启用托管标识。 授予访问存储帐户所需的权限，以允许将流量从本地复制到 Azure 目标位置。 需要针对存储的托管标识访问权限才可允许专用链接访问保管库。

- 进行专用终结点所需的 DNS 更改。
- 为虚拟网络中的保管库创建和批准专用终结点。
- 为存储帐户创建专用终结点。 你可以根据需要继续允许对存储的公共或防火墙访问。 Azure Site Recovery 不需要创建专用终结点来访问存储。
  
下图显示了具有专用终结点的混合灾难恢复的复制工作流。 无法在本地网络中创建专用终结点。 要使用专用链接，需要创建 Azure 虚拟网络（在本文中称为“旁路网络”），在本地和旁路网络之间建立专用连接，然后在旁路网络中创建专用终结点。 可以选择任何形式的专用连接。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

## <a name="prerequisites-and-caveats"></a>先决条件和注意事项

- Site Recovery 9.35 及更高版本支持专用链接。
- 只能为新的恢复服务保管库创建专用终结点，没有任何项已注册到该保管库。 因此，必须在将任何项添加到保管库之前创建专用终结点。 有关定价信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。
- 为保管库创建专用终结点时，保管库将被锁定。 只能从具有专用终结点的网络进行访问。
- Azure Active Directory 当前不支持专用终结点。 因此，需要允许从安全的 Azure 虚拟网络出站访问使 Azure Active Directory 在某个区域中正常工作所需的 IP 和完全限定的域名。
  如果适用，还可以使用网络安全组标记“Azure Active Directory”和 Azure 防火墙标记来允许访问 Azure Active Directory。
- 在创建专用终结点的旁路网络中，需要五个 IP 地址。 为保管库创建专用终结点时，Site Recovery 会创建五个用于访问其微服务的专用链接。
- 旁路网络中还需要一个额外的 IP 地址，以便与缓存存储帐户建立专用终结点连接。 可以在本地和存储帐户终结点之间使用任何连接方法。 例如，可以使用 Internet 或 Azure [ExpressRoute](../expressroute/index.yml)。 建立专用链接是可选的。 只能在常规用途 v2 帐户上为存储创建专用终结点。 有关常规用途 v2 帐户上数据传输的定价信息，请参阅 [Azure 页 Blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>为站点恢复创建和使用专用终结点

 以下部分介绍为虚拟网络中的站点恢复创建和使用专用终结点所需的步骤。

> [!NOTE]
> 建议按所示顺序执行这些步骤。 否则可能无法在保管库中使用专用终结点，并且可能需要使用新保管库重新开始执行该过程。

### <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库包含计算机的复制信息。 它用于触发 Site Recovery 操作。 有关如何在 Azure 区域（要用于在发生灾难时进行故障转移的区域）中创建恢复服务保管库的信息，请参阅[创建恢复服务保管库](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

### <a name="enable-the-managed-identity-for-the-vault"></a>为保管库启用托管标识

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)允许保管库访问存储帐户。 Site Recovery 可能需要访问目标存储和缓存/日志存储帐户，具体取决于你的要求。 对保管库使用专用链接服务时，需要托管标识访问权限。

1. 转到恢复服务保管库。 在“设置”下选择“标识” ：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

1. 将“状态”更改为“开”，然后选择“保存”  。

   一个对象 ID 随即生成。 现已向 Azure Active Directory 注册保管库。

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>为恢复服务保管库创建专用终结点

要保护本地源网络中的计算机，需要为旁路网络中的保管库提供一个专用终结点。 使用 Azure 门户中的专用链接中心或使用 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)创建专用终结点。

1. 在 Azure 门户的搜索框中，搜索“专用链接”。 选择“专用链接”，转到专用链接中心：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

1. 在左侧窗格中，选择“专用终结点”。 在“专用终结点”页上选择“添加”，开始为保管库创建专用终结点： 

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

1. 在“创建专用终结点”页面上，指定创建专用终结点连接的详细信息。

   1. **基本信息**。 提供专用终结点的基本详细信息。 使用用于旁路网络的区域：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

   1. **资源**。 在此选项卡上，需要指定要为其创建连接的平台即服务资源。 在所选订阅的“资源类型”下，选择“Microsoft.RecoveryServices/vaults” 。 在“资源”下，选择恢复服务保管库的名称。 选择“Azure Site Recovery”作为“目标子资源” 。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

   1. **配置**。 在此选项卡上，指定要在其中创建专用终结点的旁路网络和子网。 

      通过选择“是”启用与专用 DNS 区域的集成。
      选择现有 DNS 区域或创建一个新区域。 选择“是”会自动将区域链接到旁路网络。 此操作还会添加对新 IP 进行 DNS 解析所需的 DNS 记录，以及为专用终结点创建的完全限定的域名。

      确保选择为连接到同一保管库的每个新专用终结点创建新的 DNS 区域。 如果选择现有的专用 DNS 区域，将覆盖以前的 CNAME 记录。 在继续操作之前，请参阅[专用终结点指南](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

      如果你的环境具有中心辐射模型，则整个安装过程只需要一个专用终结点和一个专用 DNS 区域。 这是因为所有虚拟网络之间都已启用了对等互连。 有关详细信息，请参阅[专用终结点 DNS 集成](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手动创建专用 DNS 区域，请按照创建专用 DNS 区域中的步骤操作， [并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

   1. **标记**。 （可选）可以为专用终结点添加标记。

   1. **查看 \+ 创建**。 完成验证后，选择“创建”以创建专用终结点。

创建专用终结点时，有五个完全限定的域名 (FQDN) 会添加到专用终结点。 这些链接使本地网络中的计算机能够通过旁路网络访问保管库上下文中所有必需的 Site Recovery 微服务。 可以使用同一专用终结点来保护旁路网络和所有对等网络中的任何 Azure 计算机。

这五个域名的格式为：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>为站点恢复批准专用终结点

如果你创建了专用终结点，并且还是恢复服务保管库的所有者，则你先前创建的专用终结点将在几分钟内自动获得批准。 否则，保管库的所有者必须先批准专用终结点，然后你才能使用该终结点。 要批准或拒绝请求的专用终结点连接，请转到恢复保管库页中“设置”下的“专用终结点连接” 。

在继续操作之前，可以先转到专用终结点资源，查看连接的状态：

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>（可选）为缓存存储帐户创建专用终结点

可以使用 Azure 存储的专用终结点。 对于 Azure Site Recovery 复制，为存储访问创建专用终结点是可选的。 如果为存储创建专用终结点，则需要为旁路虚拟网络中的缓存/日志存储帐户提供专用终结点。

> [!NOTE]
> 只能在常规用途 v2 存储帐户上为存储创建专用终结点。 有关定价信息，请参阅 [Azure 页 Blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

按照[创建专用存储的指南](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint)操作，创建一个具有专用终结点的存储帐户。 请确保在“与专用 DNS 区域集成”下选择“是” 。 选择现有 DNS 区域或创建一个新区域。

### <a name="grant-required-permissions-to-the-vault"></a>向保管库授予所需的权限

根据设置，可能需要目标 Azure 区域中的一个或多个存储帐户。 接下来，为 Site Recovery 所需的所有缓存/日志存储帐户授予托管标识权限。 在这种情况下，必须提前创建所需的存储帐户。

在启用虚拟机复制之前，保管库的托管标识必须具有以下角色权限，具体取决于存储帐户的类型。

- 基于资源管理器的存储帐户（标准类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 基于资源管理器的存储帐户（高级类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

以下步骤介绍如何向存储帐户添加角色分配：

1. 转到存储帐户。 在左侧窗格中选择“访问控制(IAM)”。

1. 在“添加角色分配”部分，选择“添加” ：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

1. 在“添加角色分配”页上的“角色”列表中，选择本节开头部分列出的角色 。 输入保管库的名称，然后选择“保存”。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

添加这些权限后，需要允许访问 Microsoft 受信任的服务。 转到“防火墙和虚拟网络”，在“例外”中选择“允许受信任的 Microsoft 服务访问此存储帐户”  。

### <a name="protect-your-virtual-machines"></a>保护虚拟机

完成上述任务后，继续设置本地基础结构。 继续完成以下任务之一： 

- [为 VMware 和物理计算机部署配置服务器](./vmware-azure-deploy-configuration-server.md)
- [设置用于复制的 Hyper-V 环境](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

设置完成后，为源计算机启用复制。 在旁路网络中创建保管库的专用终结点之前，请勿设置基础结构。

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>创建专用 DNS 区域并手动添加 DNS 记录

如果在为保管库创建专用终结点时未选择与专用 DNS 区域集成的选项，请按照本节中的步骤操作。

创建一个专用 DNS 区域，允许 Site Recovery 提供程序（对于 Hyper-V 计算机）或进程服务器（对于 VMware/物理计算机）将专用 FQDN 解析为专用 IP。

1. 创建专用 DNS 区域。

   1. 在 " **所有服务** " 搜索框中搜索 "专用 DNS 区域"，并在结果中选择 " **专用 DNS 区域** "：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。" 按钮开始创建新区域。

   1. 在“创建专用 DNS 区域”页上，输入所需的详细信息。 输入 **privatelink.siterecovery.windowsazure.com** 作为专用 DNS 区域的名称。 可以选择任何资源组和任何订阅。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

   1. 继续选择“查看 \+ 创建”选项卡，查看并创建 DNS 区域。

1. 将专用 DNS 区域链接到虚拟网络。

   现在需要将创建的专用 DNS 区域链接到旁路。

   1. 转到在上一步中创建的专用 DNS 区域，然后转到左侧窗格中的“虚拟网络链接”。 选择“添加”。

   1. 输入必需的详细信息。 在“订阅”和“虚拟网络”列表中，选择与旁路网络相对应的详细信息 。 保留其他字段中的默认值。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

1. 添加 DNS 记录。

   创建所需的专用 DNS 区域和专用终结点后，需要将 DNS 记录添加到 DNS 区域。

   > [!NOTE]
   > 如果使用的是自定义专用 DNS 区域，请确保使用类似的条目，如以下步骤所述。

   在此步骤中，需要将专用终结点中每个 FQDN 的条目输入到专用 DNS 区域。

   1. 转到专用 DNS 区域，然后转到左侧窗格中的“概述”部分。 选择“记录集”以开始添加记录。

   1. 在“添加记录集”页上，为每个完全限定的域名和专用 IP 添加一个条目，作为“A”类型的记录 。 可以在“概述”中的“专用终结点”页上获得完全限定的域名和 IP 列表 。 如以下屏幕截图中所示，来自专用终结点的第一个完全限定的域名会添加到专用 DNS 区域中的记录集。

      这些完全限定的域名与此模式匹配： `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的图表。":::

## <a name="next-steps"></a>后续步骤

现在，你已为虚拟机复制启用了专用终结点，请参阅其他文章了解其他相关信息：

- [部署本地配置服务器](./vmware-azure-deploy-configuration-server.md)
- [对 Azure 设置本地 Hyper-V VM 的灾难恢复](./hyper-v-azure-tutorial.md)