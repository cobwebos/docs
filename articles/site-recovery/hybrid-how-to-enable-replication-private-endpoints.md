---
title: 使用 Azure Site Recovery 中的专用终结点为本地计算机启用复制
description: 本文介绍如何使用 Site Recovery 为本地计算机配置复制。
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: c91c92a18570f569b6364b646e6fdf7bf534802f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096435"
---
# <a name="replicate-on-premises-machines-with-private-endpoints"></a>将本地计算机复制到专用终结点

Azure Site Recovery 允许使用[Azure 专用链接](../private-link/private-endpoint-overview.md)专用终结点将本地计算机复制到 Azure 中的虚拟网络。 以下区域支持专用终结点对恢复保管库的访问：

- Azure 商业：美国中南部、美国西部2、美国东部
- Azure 政府版： US Gov 弗吉尼亚州、US Gov 亚利桑那州、US Gov 德克萨斯州、US DoD 东部、US DoD 中部

本文介绍如何执行以下步骤：

- 创建 Azure 备份恢复服务保管库来保护计算机。
- 为保管库启用托管标识，并授予访问客户存储帐户所需的权限，以将流量从本地复制到 Azure 目标位置。 设置对保管库的私有链接访问时，需要对存储的托管标识访问权限。
- 对专用终结点进行 DNS 更改
- 为虚拟网络中的保管库创建和批准专用终结点
- 为存储帐户创建专用终结点。 你可以根据需要继续允许公共或防火墙处理访问存储。 Azure Site Recovery 不强制创建用于访问存储的专用终结点。
  
以下参考体系结构介绍了如何更改复制工作流以使用专用终结点进行混合灾难恢复。 无法在本地网络中创建专用终结点。 若要使用专用链接，你需要创建 Azure 虚拟网络（本文中称为 "回避网络"），在本地与绕过网络之间建立专用连接，然后在旁路网络中创建专用终结点。 选择专用连接是自行决定。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="具有专用终结点的 Site Recovery 的参考体系结构。":::

## <a name="prerequisites-and-caveats"></a>先决条件和注意事项

- 为**9.35**版本及更高版本的 Site Recovery 基础结构启用了对专用链接的支持。
- 只能为未向保管库注册任何项的新恢复服务保管库创建专用终结点。 因此，**在将任何项目添加到保管库之前，必须先创建**专用终结点。 查看[专用终结点](https://azure.microsoft.com/pricing/details/private-link/)的定价结构。
- 为保管库创建专用终结点时，保管库将被锁定，并且**不能从具有专用终结点的网络以外的网络进行访问**。
- Azure Active Directory 当前不支持私有终结点。 因此，要在区域中工作 Azure Active Directory 所需的 Ip 和完全限定的域名必须允许来自受保护的 Azure 虚拟网络的出站访问。 你还可以使用网络安全组标记 "Azure Active Directory" 和 Azure 防火墙标记，以允许访问 Azure Active Directory （如果适用）。
- 在创建专用终结点的回避网络中**需要5个 IP 地址**。 为保管库创建专用终结点时，Site Recovery 会创建五个专用链接来访问其微服务。
- 在旁路网络中，**需要一个额外的 IP 地址**，以便与缓存存储帐户建立专用终结点连接。 在本地与你的存储帐户终结点之间的连接方法（如 internet 或[ExpressRoute](../expressroute/index.yml)）是你的说明。 建立专用链接是可选的。 只能在常规用途 v2 类型上创建用于存储的专用终结点。 查看[GPv2 上数据传输](https://azure.microsoft.com/pricing/details/storage/page-blobs/)的定价结构。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>创建和使用 Site Recovery 的专用终结点

 本部分介绍在虚拟网络中创建和使用 Azure Site Recovery 专用终结点的步骤。

> [!NOTE]
> 强烈建议您按照所提供的相同顺序执行这些步骤。 如果无法这样做，则可能导致保管库无法使用专用终结点，要求你使用新的保管库重新启动此过程。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库。

恢复服务保管库是包含计算机复制信息并用于触发 Site Recovery 操作的实体。 有关在发生灾难时要进行故障转移的 Azure 区域中创建恢复服务保管库的步骤，请参阅[创建恢复服务保管库](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

## <a name="enable-the-managed-identity-for-the-vault"></a>启用保管库的托管标识。

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)允许保管库获取对客户的存储帐户的访问权限。 Site Recovery 需要根据方案要求访问目标存储帐户和缓存/日志存储帐户。 如果使用的是保管库的专用链接服务，则托管标识访问很重要。

1. 请参阅恢复服务保管库。 选择 "_设置_" 下的**标识**。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="显示 Azure 门户 和 恢复服务 页。":::

1. 将**状态**更改为 _"打开_"，然后选择 "**保存**"。

1. 将生成一个**对象 ID** ，指示保管库现在已注册到 Azure Active Directory。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>创建恢复服务保管库的专用终结点

对于本地源网络中的计算机，你需要在旁路网络中为保管库使用一个专用终结点。 使用门户中的专用链接中心或通过[Azure PowerShell](../private-link/create-private-endpoint-powershell.md)创建专用终结点。

1. 在 Azure 门户搜索栏中，搜索并选择 "专用链接"。 此操作会将你带到专用链接中心。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="显示搜索专用链接中心的 Azure 门户。":::

1. 在左侧导航栏上，选择 "**专用终结点**"。 在 "专用终结点" 页上，选择 " ** \+ 添加**" 开始为保管库创建专用终结点。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="显示如何在专用链接中心创建专用终结点。":::

1. 一旦进入 "创建专用终结点" 体验，就需要指定创建专用终结点连接的详细信息。

   1. **基本信息**：填写专用终结点的基本详细信息。 该区域应与绕过网络相同。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="显示 基本 选项卡、项目详细信息、订阅，以及用于在 Azure 门户中创建专用终结点的其他相关字段。":::

   1. **资源**：此选项卡要求您提到要为其创建连接的平台即服务资源。 从所选订阅的**资源类型**中选择 " _microsoft.recoveryservices/保管库_"。 然后，为 "**资源**" 选择恢复服务保管库的名称，将_Azure Site Recovery_设置为 "**目标子资源**"。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="显示用于链接到 Azure 门户中的专用终结点的 资源 选项卡、资源类型、资源和目标子资源字段。":::

   1. **配置**：在 "配置" 中，指定要在其中创建专用终结点的 "回避网络" 和 "子网"。 通过选择 **"是"** 启用与专用 DNS 区域的集成。
      选择已创建的 DNS 区域，或新建一个。 选择 **"是"** 会自动将该区域链接到 "绕过网络"，并添加 dns 解析新 ip 和为专用终结点创建的完全限定域名所需的 dns 记录。

      确保为连接到同一保管库的每个新的专用终结点选择创建新的 DNS 区域。 如果选择现有的专用 DNS 区域，则会覆盖以前的 CNAME 记录。 继续之前，请参阅[私有终结点指南](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

      如果你的环境有一个中心辐射型模型，则只需要一个专用终结点，并且只有一个专用 DNS 区域用于整个设置，因为你的所有虚拟网络都已在它们之间启用了对等互连。 有关详细信息，请参阅[私有终结点 DNS 集成](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手动创建专用 DNS 区域，请按照创建专用 DNS 区域中的步骤操作，[并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="显示 配置 选项卡，其中包含网络和 DNS 集成字段，用于配置 Azure 门户中的专用终结点。":::

   1. **标记**：可以选择添加专用终结点的标记。

   1. **查看 \+ 创建**：验证完成后，选择 "**创建**" 以创建专用终结点。

创建专用终结点后，会将五个完全限定的域名添加到专用终结点。 这些链接使本地网络中的计算机能够通过回避网络访问保管库上下文中所有必需的 Site Recovery 微服务。 同一专用终结点可用于保护绕过网络和所有对等互连网络中的任何 Azure 计算机。

五个域名的格式如下：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>批准 Site Recovery 的专用终结点

如果创建专用终结点的用户也是恢复服务保管库的所有者，则在数分钟内会自动批准上面创建的专用终结点。 否则，保管库的所有者必须先批准专用终结点，然后才能使用该终结点。 若要批准或拒绝请求的专用终结点连接，请在 "恢复保管库" 页上的 "设置" 下，中转到**专用终结点连接**。

在继续操作之前，您可以转到专用终结点资源来查看连接状态。

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="显示保管库的 专用终结点连接 页和 Azure 门户中的连接列表。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>可有可无为缓存存储帐户创建专用终结点

可以使用 Azure 存储的专用终结点。 为存储访问创建专用终结点对于 Azure Site Recovery 复制是_可选_的。 创建专用终结点用于存储时，需要在绕过虚拟网络中缓存/日志存储帐户的专用终结点。

> [!NOTE]
> 只能在**常规用途 v2**存储帐户上创建存储的专用终结点。 有关定价信息，请参阅[标准页 blob 价格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

按照创建[专用存储的指导](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint)创建使用专用终结点的存储帐户。 请确保选择 **"是"** 以与专用 DNS 区域集成。 选择已创建的 DNS 区域，或新建一个。

## <a name="grant-required-permissions-to-the-vault"></a>授予保管库所需的权限

根据设置，可能需要在目标 Azure 区域中有一个或多个存储帐户。 接下来，为 Site Recovery 所需的所有缓存/日志存储帐户授予托管标识权限。 在这种情况下，必须提前创建所需的存储帐户。

在启用虚拟机的复制之前，保管库的托管标识必须具有以下角色权限（具体取决于存储帐户的类型）：

- 基于资源管理器的存储帐户（标准类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 基于资源管理器的存储帐户（高级类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

以下步骤介绍如何将角色分配添加到存储帐户，一次添加一个：

1. 转到存储帐户，并导航到页面左侧的 "**访问控制（IAM）** "。

1. 在**访问控制（IAM）** 上，在 "添加角色分配" 框中选择 "**添加**"。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="显示存储帐户的 访问控制（IAM） 页和 Azure 门户中的 添加角色分配 按钮。":::

1. 在 "添加角色分配" 侧页上，从 "**角色**" 下拉列表中选择上面的列表中的角色。 输入保管库的**名称**，然后选择 "**保存**"。

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="显示存储帐户的 访问控制（IAM） 页，以及用于选择角色的选项以及在 Azure 门户中将该角色授予的主体。":::

除了这些权限之外，还需要允许 MS 可信服务进行访问。 请访问 "防火墙和虚拟网络"，并选中 "允许受**信任的 Microsoft**服务访问此存储帐户" 复选框。

## <a name="protect-your-virtual-machines"></a>保护虚拟机

完成上述所有配置后，请继续设置本地基础结构。

- [为 VMware 和物理计算机部署配置服务器](./vmware-azure-deploy-configuration-server.md)
- 或[设置用于复制的 hyper-v 环境](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

完成设置后，为源计算机启用复制。 确保仅在已在旁路网络中创建了保管库的专用终结点后，才会设置基础结构。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>创建专用 DNS 区域并手动添加 DNS 记录

如果未选择在为保管库创建专用终结点时与专用 DNS 区域集成的选项，请按照本部分中的步骤进行操作。

创建一个专用 DNS 区域，以允许 Site Recovery 提供程序（适用于 Hyper-v 计算机）或进程服务器（对于 VMware/物理计算机）将专用链接完全限定的域名解析为专用 Ip。

1. 创建专用 DNS 区域

   1. 在 "**所有服务**" 搜索栏中搜索 "专用 DNS 区域"，然后从下拉范围中选择 "专用 DNS 区域"。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="显示在 Azure 门户中的 新建资源 页面上搜索 专用 dns 区域。":::

   1. 在 "专用 DNS 区域" 页上，选择 " ** \+ 添加**" 按钮开始创建新区域。

   1. 在 "创建专用 DNS 区域" 页上，填写所需的详细信息。 输入专用 DNS 区域的名称 `privatelink.siterecovery.windowsazure.com` 。 你可以选择任何资源组和任何订阅来创建它。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="显示 创建专用 DNS 区域 页的 基本信息 选项卡和 Azure 门户中相关项目的详细信息。":::

   1. 转到 "**查看 \+ 创建**" 选项卡，查看并创建 DNS 区域。

1. 将专用 DNS 区域链接到你的虚拟网络

   上面创建的专用 DNS 区域现在必须已链接到 "绕过"。

   1. 转到你在上一步中创建的专用 DNS 区域，然后导航到页面左侧的 "**虚拟网络" 链接**。 完成后，选择 " ** \+ 添加**" 按钮。

   1. 填写必需的详细信息。 必须使用跳过网络的相应详细信息填充 "**订阅**" 和 "**虚拟网络**" 字段。 其他字段必须保留不动。

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="显示用于在 Azure 门户中添加具有链接名称、订阅和相关虚拟网络的虚拟网络链接的页面。":::

1. 添加 DNS 记录

   创建所需的专用 DNS 区域和专用终结点后，需要将 DNS 记录添加到 DNS 区域。

   > [!NOTE]
   > 如果你使用的是自定义专用 DNS 区域，请确保按照下面所述进行类似的条目。

   此步骤要求你将专用终结点中每个完全限定的域名的条目输入到专用 DNS 区域。

   1. 转到专用 DNS 区域，然后导航到页面左侧的 "**概述**" 部分。 完成后，选择 " ** \+ 记录集**"，开始添加记录。

   1. 在打开的 "添加记录集" 页中，为每个完全限定的域名和专用 IP 添加_一个_条目作为类型记录。 在**概述**中，可以从 "专用终结点" 页获取完全限定的域名和 ip 的列表。 如以下示例中所示，专用终结点的第一个完全限定的域名将添加到专用 DNS 区域中的记录集。

      这些完全限定的域名匹配模式：`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="显示用于将完全限定域名的类型记录添加到 Azure 门户中的专用终结点的页面。":::

## <a name="next-steps"></a>后续步骤

现在，你已为虚拟机复制启用了专用终结点，请参阅以下其他页面了解更多相关信息：

- [部署本地配置服务器](./vmware-azure-deploy-configuration-server.md)
- [对 Azure 设置本地 Hyper-V VM 的灾难恢复](./hyper-v-azure-tutorial.md)
