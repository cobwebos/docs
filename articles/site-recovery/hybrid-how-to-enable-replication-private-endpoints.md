---
title: 使用专用终结点为本地计算机启用复制
description: 本文介绍如何使用 Site Recovery 中的专用终结点配置本地计算机的复制。
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 13c19f07ac21f986a5523407e46c59c050ebf96d
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142071"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>使用专用终结点复制本地计算机

Azure Site Recovery 允许使用[Azure 专用链接](../private-link/private-endpoint-overview.md)专用终结点将本地计算机复制到 Azure 中的虚拟网络。 以下区域支持专用终结点对恢复保管库的访问：

- Azure 商业：美国中南部、美国西部2、美国东部
- Azure 政府版： US Gov 弗吉尼亚州、US Gov 亚利桑那州、US Gov 德克萨斯州、US DoD 东部、US DoD 中部

本文介绍如何完成以下步骤：

- 创建 Azure 备份恢复服务保管库来保护计算机。
- 启用保管库的托管标识。 授予访问存储帐户所需的权限，以便能够将流量从本地复制到 Azure 目标位置。 需要对存储的托管标识访问权限才能访问保管库的专用链接。

- 对专用终结点所需的 DNS 进行更改。
- 为虚拟网络中的保管库创建和批准专用终结点。
- 为存储帐户创建专用终结点。 你可以根据需要继续允许公共或防火墙处理访问存储。 Azure Site Recovery 不需要创建专用终结点来访问存储。
  
下图显示了具有专用终结点的混合灾难恢复的复制工作流。 你无法在本地网络中创建专用终结点。 若要使用专用链接，需要在本文) 创建一个名为*绕过网络*的 Azure 虚拟网络 (，在本地与绕过网络之间建立专用连接，然后在绕过网络中创建专用终结点。 您可以选择任何形式的专用连接。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="显示 Azure Site Recovery 和专用终结点的体系结构的关系图。":::

## <a name="prerequisites-and-caveats"></a>先决条件和注意事项

- Site Recovery 9.35 及更高版本支持专用链接。
- 只能为未注册任何项的新恢复服务保管库创建专用终结点。 因此，在将任何项添加到保管库之前，必须创建私有终结点。 有关定价信息，请参阅[Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link/)。
- 为保管库创建专用终结点时，保管库已锁定。 只能从具有专用终结点的网络进行访问。
- Azure Active Directory 当前不支持专用终结点。 因此，你需要允许来自受保护的 Azure 虚拟网络的出站访问，以及 Azure Active Directory 在某个区域中工作所需的 Ip 和完全限定的域名。
  如果适用，你还可以使用网络安全组标记 "Azure Active Directory" 和 Azure 防火墙标记以允许访问 Azure Active Directory。
- 在创建专用终结点的回避网络中需要5个 IP 地址。 为保管库创建专用终结点时，Site Recovery 会创建五个专用链接来访问其微服务。
- 在旁路网络中，需要一个额外的 IP 地址，以便与缓存存储帐户建立专用终结点连接。 你可以在本地与你的存储帐户终结点之间使用任何连接方法。 例如，可以使用 internet 或 Azure [ExpressRoute](../expressroute/index.yml)。 建立专用链接是可选的。 只能在常规用途 v2 帐户上创建用于存储的专用终结点。 有关常规用途 v2 帐户的数据传输定价的信息，请参阅[Azure 页 blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>为 site recovery 创建和使用专用终结点

 以下部分介绍在虚拟网络中创建和使用 site recovery 的专用终结点时需要执行的步骤。

> [!NOTE]
> 建议按所示顺序执行这些步骤。 否则，可能无法使用保管库中的专用终结点，可能需要使用新保管库重新启动该过程。

### <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库包含计算机的复制信息。 它用于触发 Site Recovery 操作。 有关如何在发生灾难时要故障转移到的 Azure 区域中创建恢复服务保管库的信息，请参阅[创建恢复服务保管库](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

### <a name="enable-the-managed-identity-for-the-vault"></a>启用保管库的托管标识

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)允许保管库访问你的存储帐户。 Site Recovery 可能需要访问目标存储和缓存/日志存储帐户，具体取决于你的要求。 当你使用保管库的私有链接服务时，需要托管标识访问权限。

1. 请参阅恢复服务保管库。 选择 "**设置**" 下的**标识**：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="显示 标识设置 页的屏幕截图。":::

1. 将**状态**更改为 **"打开**"，然后选择 "**保存**"。

   生成对象 ID。 保管库现在已注册到 Azure Active Directory。

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>创建恢复服务保管库的专用终结点

若要保护本地源网络中的计算机，你需要在旁路网络中为保管库使用一个专用终结点。 使用 Azure 门户中的专用链接中心或使用[Azure PowerShell](../private-link/create-private-endpoint-powershell.md)创建专用终结点。

1. 在 Azure 门户搜索框中，搜索 "专用链接"。 选择 "**专用链接**" 以中转到专用链接中心：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="显示搜索专用链接中心 Azure 门户的屏幕截图。":::

1. 在左窗格中，选择 "**专用终结点**"。 在 "**专用终结点**" 页上，选择 "**添加**" 开始为保管库创建专用终结点：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="显示如何在专用链接中心创建专用终结点的屏幕截图。":::

1. 在 "**创建专用终结点**" 页上，指定用于创建专用终结点连接的详细信息。

   1. **基础知识**。 提供专用终结点的基本详细信息。 使用用于绕过网络的区域：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="显示用于创建专用终结点的 基本 选项卡的屏幕截图。":::

   1. **资源**。 在此选项卡上，需要指定要为其创建连接的平台即服务资源。 在所选订阅的 "**资源类型**" 下，选择 " **microsoft.recoveryservices/保管库**"。 选择 "**资源**" 下的恢复服务保管库的名称。 选择 " **Azure Site Recovery** " 作为 "**目标子资源**"。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="显示用于链接到专用终结点的 资源 选项卡的屏幕截图。":::

   1. **配置**。 在此选项卡上，指定要在其中创建专用终结点的 "回避网络" 和 "子网"。 

      通过选择 **"是"** 启用与专用 DNS 区域的集成。
      选择现有 DNS 区域或创建一个新区域。 选择 **"是"** 会自动将区域链接到 "绕过网络"。 此操作还会添加 dns 解析所需的 DNS 记录，这些记录是为专用终结点创建的新 Ip 和完全限定的域名。

      确保为连接到同一保管库的每个新的专用终结点选择创建新的 DNS 区域。 如果选择现有的专用 DNS 区域，则会覆盖以前的 CNAME 记录。 请参阅[私有终结点指南](../private-link/private-endpoint-overview.md#private-endpoint-properties)，然后再继续。

      如果你的环境具有中心和辐射模型，则整个设置只需要一个专用终结点和一个专用 DNS 区域。 这是因为所有虚拟网络都已在它们之间启用了对等互连。 有关详细信息，请参阅[私有终结点 DNS 集成](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手动创建专用 DNS 区域，请按照创建专用 DNS 区域中的步骤操作，[并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="显示专用终结点配置的 配置 选项卡的屏幕截图。":::

   1. **标记**。 （可选）可以为专用终结点添加标记。

   1. **查看 \+ 创建**。 验证完成后，选择 "**创建**" 以创建专用终结点。

创建专用终结点时，会将5个完全限定的域名 (Fqdn 添加到专用终结点) 。 通过使用这些链接，本地网络中的计算机可以通过绕过网络访问微服务，这是保管库上下文中所有必需的 Site Recovery。 你可以使用相同的专用终结点来保护绕过网络和所有对等互连网络中的任何 Azure 计算机。

这五个域名的格式为：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>为 site recovery 批准专用终结点

如果创建专用终结点，并且还是恢复服务保管库的所有者，则你之前创建的专用终结点将在几分钟内自动批准。 否则，保管库的所有者必须批准专用终结点，然后才能使用该终结点。 若要批准或拒绝请求的专用终结点连接，请在 "恢复保管库" 页上的 "**设置**" 下中转到**专用终结点连接**。

在继续之前，你可以转到专用终结点资源来检查连接状态：

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="显示保管库和连接列表的 专用终结点连接 页的屏幕截图。":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a> (可选) 为缓存存储帐户创建专用终结点

可以使用 Azure 存储的专用终结点。 为存储访问创建专用终结点对于 Azure Site Recovery 复制是可选的。 如果为存储创建专用终结点，则在旁路虚拟网络中需要缓存/日志存储帐户的专用终结点。

> [!NOTE]
> 只能在常规用途 v2 存储帐户上创建用于存储的专用终结点。 有关定价信息，请参阅[Azure 页 blob 定价](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

按照[创建专用存储的指导](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint)创建具有专用终结点的存储帐户。 请确保选择 "**与专用 DNS 区域集成**" 下的 **"是"** 。 选择现有 DNS 区域或创建一个新区域。

### <a name="grant-required-permissions-to-the-vault"></a>授予保管库所需的权限

根据设置，可能需要在目标 Azure 区域中有一个或多个存储帐户。 接下来，为 Site Recovery 所需的所有缓存/日志存储帐户授予托管标识权限。 在这种情况下，必须提前创建所需的存储帐户。

在启用虚拟机的复制之前，保管库的托管标识必须具有以下角色权限，具体取决于存储帐户的类型。

- 基于资源管理器的存储帐户 (标准类型) ：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 基于资源管理器的存储帐户 (高级类型) ：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

以下步骤介绍如何将角色分配添加到存储帐户：

1. 转到存储帐户。 在左窗格中选择 "**访问控制 (IAM) ** 。

1. 在 "**添加角色分配**" 部分，选择 "**添加**"：

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="显示存储帐户的访问控制 (IAM) 页的屏幕截图。":::

1. 在 "**添加角色分配**" 页上的 "**角色**" 列表中，从本部分开头的列表中选择角色。 输入保管库的名称，然后选择 "**保存**"。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="显示 添加角色分配 页的屏幕截图。":::

添加这些权限后，需要允许访问 Microsoft 受信任的服务。 请访问 "**防火墙和虚拟网络**"，并选择 "**允许受信任的 Microsoft 服务访问此存储帐户**"**例外**。

### <a name="protect-your-virtual-machines"></a>保护虚拟机

完成上述任务后，请继续安装本地基础结构。 继续完成以下任务之一： 

- [为 VMware 和物理计算机部署配置服务器](./vmware-azure-deploy-configuration-server.md)
- [设置用于复制的 Hyper-v 环境](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

设置完成后，为源计算机启用复制。 在绕过网络中创建保管库的专用终结点之前，请不要设置基础结构。

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>创建专用 DNS 区域并手动添加 DNS 记录

如果在为保管库创建专用终结点时未选择与专用 DNS 区域集成的选项，请按照本部分中的步骤进行操作。

创建一个专用 DNS 区域，以允许 Site Recovery 提供程序 (用于 Hyper-v 计算机) 或 VMware/物理计算机的进程服务器 () 将专用 Fqdn 解析为专用 Ip。

1. 创建专用 DNS 区域。

   1. 在 "**所有服务**" 搜索框中搜索 "专用 DNS 区域"，并在结果中选择 "**专用 DNS 区域**"：

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="屏幕截图，显示在 Azure 门户的 新建资源 页面上搜索专用 dns 区域。":::

   1. 在 "**专用 DNS 区域**" 页上，选择 "**添加**" 按钮开始创建新区域。

   1. 在 "**创建专用 DNS 区域**" 页上，输入所需的详细信息。 输入**privatelink.siterecovery.windowsazure.com**作为专用 DNS 区域的名称。 你可以选择任何资源组和任何订阅。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="显示 创建专用 DNS 区域 页的 基本信息 选项卡的屏幕截图。":::

   1. 转到 "**查看 \+ 创建**" 选项卡，查看并创建 DNS 区域。

1. 将专用 DNS 区域链接到虚拟网络。

   你现在需要将创建的专用 DNS 区域链接到 "绕过"。

   1. 中转到你在上一步中创建的专用 DNS 区域，然后在左窗格中转到 "**虚拟网络" 链接**。 选择“添加”。

   1. 输入所需的详细信息。 在 "**订阅**" 和 "**虚拟网络**" 列表中，选择对应于绕过网络的详细信息。 保留其他字段中的默认值。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="显示 添加虚拟网络 链接页的屏幕截图。":::

1. 添加 DNS 记录。

   创建所需的专用 DNS 区域和专用终结点后，需要将 DNS 记录添加到 DNS 区域。

   > [!NOTE]
   > 如果使用的是自定义专用 DNS 区域，请务必按以下步骤所述进行类似的条目。

   在此步骤中，需要将专用终结点中每个 FQDN 的条目输入到专用 DNS 区域。

   1. 中转到专用 DNS 区域，然后在左侧窗格中转到 "**概述**" 部分。 选择 "**记录集**"，开始添加记录。

   1. 在 "**添加记录集**" 页上，为每个完全限定的域名和专用 IP 分别添加**一个**条目作为 "类型" 记录。 你可以在 "**概述**" 页上获取完全限定的域名和 ip**的列表**。 如以下屏幕截图中所示，专用终结点的第一个完全限定的域名将添加到专用 DNS 区域中的记录集。

      这些完全限定的域名与此模式匹配：`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="显示 添加记录集 页的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

现在，你已为虚拟机复制启用了专用终结点，请参阅其他相关文章以了解更多相关信息：

- [部署本地配置服务器](./vmware-azure-deploy-configuration-server.md)
- [对 Azure 设置本地 Hyper-V VM 的灾难恢复](./hyper-v-azure-tutorial.md)
