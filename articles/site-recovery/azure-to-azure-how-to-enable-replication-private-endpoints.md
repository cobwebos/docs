---
title: 为 Azure Site Recovery 中的专用终结点启用复制
description: 本文介绍如何使用 Site Recovery 为具有专用终结点的 Vm 配置从一个 Azure 区域复制到另一个 Azure 区域。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 37784c4a294ccf296818f2afb1a8a345cb9d813e
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658252"
---
# <a name="replicate-machines-with-private-endpoints"></a>复制具有专用终结点的计算机

Azure Site Recovery 允许使用 [Azure 专用链接](../private-link/private-endpoint-overview.md) 专用终结点将计算机从隔离的虚拟网络内部进行复制。 所有 Azure 商业 & 政府地区都支持对恢复保管库的专用终结点访问。

本文介绍如何执行以下步骤：

- 创建 Azure 备份恢复服务保管库来保护计算机。
- 为保管库启用托管标识，并授予访问客户存储帐户所需的权限，以将流量从源复制到目标位置。 设置对保管库的私有链接访问时，需要对存储的托管标识访问权限。
- 对专用终结点进行 DNS 更改
- 为虚拟网络中的保管库创建和批准专用终结点
- 为存储帐户创建专用终结点。 你可以根据需要继续允许公共或防火墙处理访问存储。 Azure Site Recovery 不强制创建用于访问存储的专用终结点。
  
下面是有关如何使用专用终结点更改复制工作流的参考体系结构。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="具有专用终结点的 Site Recovery 的参考体系结构。":::

## <a name="prerequisites-and-caveats"></a>先决条件和注意事项

- 只能为未向保管库注册任何项的新恢复服务保管库创建专用终结点。 因此， **在将任何项目添加到保管库之前，必须先创建**专用终结点。 查看 [专用终结点](https://azure.microsoft.com/pricing/details/private-link/)的定价结构。
- 为保管库创建专用终结点时，保管库将被锁定，并且 **不能从具有专用终结点的网络以外的网络进行访问**。
- Azure Active Directory 当前不支持私有终结点。 因此，要在某个区域中工作 Azure Active Directory 所需的 Ip 和完全限定的域名必须允许来自安全网络的出站访问权限。 你还可以使用网络安全组标记 "Azure Active Directory" 和 Azure 防火墙标记，以允许访问 Azure Active Directory （如果适用）。
- 源计算机和恢复计算机的子网中**至少需要7个 IP 地址**。 为保管库创建专用终结点时，Site Recovery 会创建五个专用链接来访问其微服务。 此外，在启用复制时，它会为源区域和目标区域配对添加另外两个专用链接。
- 源和恢复子网中**需要一个额外的 IP 地址**。 仅当需要使用连接到缓存存储帐户的专用终结点时，才需要此 IP 地址。
  只能在常规用途 v2 类型上创建用于存储的专用终结点。 查看 [GPv2 上数据传输](https://azure.microsoft.com/pricing/details/storage/page-blobs/)的定价结构。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>创建和使用 Site Recovery 的专用终结点

 本部分介绍在虚拟网络中创建和使用 Azure Site Recovery 专用终结点的步骤。

> [!NOTE]
> 强烈建议您按照所提供的相同顺序执行这些步骤。 如果无法这样做，则可能导致保管库无法使用专用终结点，要求你使用新的保管库重新启动此过程。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是包含计算机复制信息并用于触发 Site Recovery 操作的实体。 有关详细信息，请参阅 [创建恢复服务保管库](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

## <a name="enable-the-managed-identity-for-the-vault"></a>启用保管库的托管标识。

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)允许保管库获取对客户的存储帐户的访问权限。 Site Recovery 需要根据方案要求访问源存储、目标存储和缓存/日志存储帐户。
如果使用的是保管库的专用链接服务，则托管标识访问很重要。

1. 请参阅恢复服务保管库。 选择 "_设置_" 下的**标识**。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="显示 Azure 门户 和 恢复服务 页。":::

1. 将 **状态** 更改为 _"打开_ "，然后选择 " **保存**"。

1. 将生成一个 **对象 ID** ，指示保管库现在已注册到 Azure Active Directory。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>创建恢复服务保管库的专用终结点

若要为 Azure 虚拟机启用故障转移和故障回复，需要保管库的两个专用终结点。 一个专用终结点，用于保护源网络中的计算机，另一个用于重新保护故障转移到恢复网络中的计算机。

请确保在此设置过程中也在目标区域中创建恢复虚拟网络。

使用门户中的专用链接中心或 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)，为你的保管库在源虚拟网络内创建第一个专用终结点。 为恢复网络中的保管库创建第二个专用终结点。 下面是在源网络中创建专用终结点的步骤。 重复相同的指南，创建第二个专用终结点。

1. 在 Azure 门户搜索栏中，搜索并选择 "专用链接"。 此操作会将你带到专用链接中心。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="显示搜索专用链接中心的 Azure 门户。":::

1. 在左侧导航栏上，选择 " **专用终结点**"。 在 "专用终结点" 页上，选择 " ** \+ 添加**" 开始为保管库创建专用终结点。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="显示如何在专用链接中心创建专用终结点。":::

1. 一旦进入 "创建专用终结点" 体验，就需要指定创建专用终结点连接的详细信息。

   1. **基本信息**：填写专用终结点的基本详细信息。 该区域应与源计算机相同。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="显示 基本 选项卡、项目详细信息、订阅，以及用于在 Azure 门户中创建专用终结点的其他相关字段。":::

   1. **资源**：此选项卡要求您提到要为其创建连接的平台即服务资源。 从所选订阅的**资源类型**中选择 " _microsoft.recoveryservices/保管库_"。 然后，为 " **资源** " 选择恢复服务保管库的名称，将 _Azure Site Recovery_ 设置为 " **目标子资源**"。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="显示用于链接到 Azure 门户中的专用终结点的 资源 选项卡、资源类型、资源和目标子资源字段。":::

   1. **配置**：从配置中，指定要在其中创建专用终结点的虚拟网络和子网。 此虚拟网络是虚拟机所在的网络。 通过选择 **"是"** 启用与专用 DNS 区域的集成。 选择已创建的 DNS 区域，或新建一个。 选择 **"是"** 会自动将该区域链接到源虚拟网络，并添加 dns 解析新 ip 和为专用终结点创建的完全限定的域名所需的 dns 记录。

      确保为连接到同一保管库的每个新的专用终结点选择创建新的 DNS 区域。 如果选择现有的专用 DNS 区域，则会覆盖以前的 CNAME 记录。 继续之前，请参阅 [私有终结点指南](../private-link/private-endpoint-overview.md#private-endpoint-properties) 。

      如果你的环境有一个中心辐射型模型，则只需要一个专用终结点，并且只有一个专用 DNS 区域用于整个设置，因为你的所有虚拟网络都已在它们之间启用了对等互连。 有关详细信息，请参阅 [私有终结点 DNS 集成](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手动创建专用 DNS 区域，请按照创建专用 DNS 区域中的步骤操作， [并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="显示 配置 选项卡，其中包含网络和 DNS 集成字段，用于配置 Azure 门户中的专用终结点。":::

   1. **标记**：可以选择添加专用终结点的标记。

   1. **查看 \+ 创建**：验证完成后，选择 " **创建** " 以创建专用终结点。

创建专用终结点后，会将五个完全限定的域名添加到专用终结点。 这些链接使虚拟网络中的计算机可以访问保管库上下文中所有需要的 Site Recovery 微服务。 稍后，在启用复制时，会将两个额外的完全限定域名添加到同一个专用终结点。

五个域名的格式如下：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>批准 Site Recovery 的专用终结点

如果创建专用终结点的用户也是恢复服务保管库的所有者，则在数分钟内会自动批准上面创建的专用终结点。 否则，保管库的所有者必须先批准专用终结点，然后才能使用该终结点。 若要批准或拒绝请求的专用终结点连接，请在 "恢复保管库" 页上的 "设置" 下，中转到 **专用终结点连接** 。

在继续操作之前，您可以转到专用终结点资源来查看连接状态。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="显示保管库的 专用终结点连接 页和 Azure 门户中的连接列表。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a> (可选) 为缓存存储帐户创建专用终结点

可以使用 Azure 存储的专用终结点。 为存储访问创建专用终结点对于 Azure Site Recovery 复制是 _可选_ 的。 为存储创建专用终结点时，需要满足以下要求：

- 源虚拟网络中的缓存/日志存储帐户需要专用终结点。
- 重新保护恢复网络中故障转移的计算机时，需要第二个专用终结点。 此专用终结点用于在目标区域中创建的新存储帐户。

> [!NOTE]
> 只能在 **常规用途 v2** 存储帐户上创建存储的专用终结点。 有关定价信息，请参阅 [标准页 blob 价格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

按照创建 [专用存储的指导](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) 创建使用专用终结点的存储帐户。 请确保选择 **"是"** 以与专用 DNS 区域集成。 选择已创建的 DNS 区域，或新建一个。

## <a name="grant-required-permissions-to-the-vault"></a>授予保管库所需的权限

如果你的虚拟机使用托管磁盘，则需要将托管标识权限仅授予缓存存储帐户。 如果虚拟机使用非托管磁盘，则需要为源、缓存和目标存储帐户授予托管标识权限。 在这种情况下，需要事先创建目标存储帐户。

在启用虚拟机的复制之前，保管库的托管标识必须具有以下角色权限（具体取决于存储帐户的类型）：

- 基于资源管理器的存储帐户 (标准类型) ：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
-  (高级类型) 资源管理器的存储帐户：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

以下步骤介绍如何将角色分配添加到存储帐户，一次添加一个：

1. 转到存储帐户，并导航到页面左侧的 " **访问控制 (" IAM ") ** 。

1. ** (IAM) 上的访问控制**一次后，在 "添加角色分配" 框中选择 "**添加**"。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="显示存储帐户上的访问控制 (IAM) 页，并显示 Azure 门户中的 添加角色分配 按钮。":::

1. 在 "添加角色分配" 侧页上，从 " **角色** " 下拉列表中选择上面的列表中的角色。 输入保管库的 **名称** ，然后选择 " **保存**"。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="显示存储帐户上的访问控制 (IAM) 页，以及用于选择角色的选项，以及要将该角色授予 Azure 门户中的主体。":::

除了这些权限之外，还需要允许 MS 可信服务进行访问。 请访问 "防火墙和虚拟网络"，并选中 "允许受 **信任的 Microsoft**服务访问此存储帐户" 复选框。

## <a name="protect-your-virtual-machines"></a>保护虚拟机

完成上述所有配置后，请继续为虚拟机启用复制。 如果在保管库中创建专用终结点时使用了 DNS 集成，则所有 Site Recovery 操作都不需要执行任何其他步骤。 但是，如果手动创建和配置 DNS 区域，则在启用复制后，需要额外的步骤在源和目标 DNS 区域中添加特定的 DNS 记录。 有关详细信息和步骤，请参阅 [创建专用 dns 区域和手动添加 dns 记录](#create-private-dns-zones-and-add-dns-records-manually)。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>创建专用 DNS 区域并手动添加 DNS 记录

如果未选择在为保管库创建专用终结点时与专用 DNS 区域集成的选项，请按照本部分中的步骤进行操作。

创建一个专用 DNS 区域，以允许移动代理将专用链接完全限定的域名解析为专用 Ip。

1. 创建专用 DNS 区域

   1. 在 " **所有服务** " 搜索栏中搜索 "专用 DNS 区域"，然后从下拉范围中选择 "专用 DNS 区域"。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="显示在 Azure 门户中的 新建资源 页面上搜索 专用 dns 区域。":::

   1. 在 "专用 DNS 区域" 页上，选择 " ** \+ 添加**" 按钮开始创建新区域。

   1. 在 "创建专用 DNS 区域" 页上，填写所需的详细信息。 输入专用 DNS 区域的名称 `privatelink.siterecovery.windowsazure.com` 。 你可以选择任何资源组和任何订阅来创建它。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="显示 创建专用 DNS 区域 页的 基本信息 选项卡和 Azure 门户中相关项目的详细信息。":::

   1. 转到 " **查看 \+ 创建** " 选项卡，查看并创建 DNS 区域。

1. 将专用 DNS 区域链接到你的虚拟网络

   上面创建的专用 DNS 区域现在必须链接到服务器当前所在的虚拟网络。 还需要提前将专用 DNS 区域链接到目标虚拟网络。

   1. 转到你在上一步中创建的专用 DNS 区域，然后导航到页面左侧的 " **虚拟网络" 链接** 。 完成后，选择 " ** \+ 添加**" 按钮。

   1. 填写必需的详细信息。 必须为 " **订阅** " 和 " **虚拟网络** " 字段填入服务器所在的虚拟网络的相应详细信息。 其他字段必须保留不动。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="显示用于在 Azure 门户中添加具有链接名称、订阅和相关虚拟网络的虚拟网络链接的页面。":::

1. 添加 DNS 记录

   创建所需的专用 DNS 区域和专用终结点后，需要将 DNS 记录添加到 DNS 区域。

   > [!NOTE]
   > 如果你使用的是自定义专用 DNS 区域，请确保按照下面所述进行类似的条目。

   此步骤要求你将专用终结点中每个完全限定的域名的条目输入到专用 DNS 区域。

   1. 转到专用 DNS 区域，然后导航到页面左侧的 " **概述** " 部分。 完成后，选择 " ** \+ 记录集**"，开始添加记录。

   1. 在打开的 "添加记录集" 页中，为每个完全限定的域名和专用 IP 添加 _一个_ 条目作为类型记录。 在 **概述**中，可以从 "专用终结点" 页获取完全限定的域名和 ip 的列表。 如以下示例中所示，专用终结点的第一个完全限定的域名将添加到专用 DNS 区域中的记录集。

      这些完全限定的域名匹配模式： `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="显示用于将完全限定域名的类型记录添加到 Azure 门户中的专用终结点的页面。":::

   > [!NOTE]
   > 启用复制后，会在两个区域的专用终结点上创建两个以上的完全限定的域名。 请确保同时为这些新创建的完全限定域名添加 DNS 记录。

## <a name="next-steps"></a>后续步骤

现在，你已为虚拟机复制启用了专用终结点，请参阅以下其他页面了解更多相关信息：

- [将 Azure VM 复制到另一个 Azure 区域](./azure-to-azure-how-to-enable-replication.md)
- [教程：为 Azure Vm 设置灾难恢复](./azure-to-azure-tutorial-enable-replication.md)
