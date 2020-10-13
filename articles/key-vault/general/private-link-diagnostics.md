---
title: 诊断 Azure Key Vault 上的私有链接配置问题
description: 解决与 Key Vault 和深入探讨配置相关的常见专用链接问题
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 156edbeda225b5457d6f5e7d29482e393b510736
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998391"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>诊断 Azure Key Vault 上的私有链接配置问题

## <a name="introduction"></a>简介

本文可帮助用户诊断和修复与 Key Vault 和专用链接功能有关的问题。 本指南将帮助你了解配置方面的情况，例如获取第一次工作的专用链接，或修复由于某些更改而导致专用链接停止工作的情况。

如果你不熟悉此功能，请参阅将 [Key Vault 与 Azure Private Link 集成](private-link-service.md)。

### <a name="problems-covered-by-this-article"></a>本文涵盖的问题

- DNS 查询仍返回密钥保管库的公共 IP 地址，而不是使用 "专用链接" 功能的预期专用 IP 地址。
- 给定客户端发出的使用专用链接的所有请求都失败，出现超时或网络错误，问题不是间歇性的。
- Key vault 有一个专用 IP 地址，但请求仍会收到 `403` `ForbiddenByFirewall` 内部错误代码的响应。
- 你使用的是专用链接，但 key vault 仍接受来自公共 Internet 的请求。
- Key vault 有两个专用终结点。 使用一个请求的操作很正常，但使用其他请求的请求会失败。
- 你有另一个使用专用链接的订阅、密钥保管库或虚拟网络。 你要进行新的类似部署，但无法获取专用链接。

### <a name="problems-not-covered-by-this-article"></a>本文未涵盖的问题

- 出现间歇性连接问题。 在给定的客户端中，你会看到一些请求工作，一些请求工作不起作用。 *间歇性问题通常不是由专用链接配置中的问题引起的;它们是网络或客户端重载的符号。*
- 你使用的是支持 BYOK (创建自己的密钥) 、CMK (客户托管密钥) 或访问存储在密钥保管库中的机密的 Azure 产品。 当你在 key vault 设置中启用防火墙时，该产品无法访问你的密钥保管库。 *查看产品特定文档。请确保它已启用防火墙，并显式声明对密钥保管库的支持。如果需要，请与该特定产品的支持人员联系。*

### <a name="how-to-read-this-article"></a>如何阅读本文

如果你不熟悉专用链接或正在评估复杂的部署，建议你阅读整篇文章。 否则，可以随意选择对你所面临的问题有更多意义的部分。

让我们开始吧！

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. 确认你拥有客户端连接

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>确认客户端在虚拟网络上运行

本指南旨在帮助您修复从应用程序代码到密钥保管库的连接。 示例包括在 Azure 虚拟机中执行的应用程序和脚本、Azure Service Fabric 群集、Azure App Service、Azure Kubernetes 服务 (AKS) 和类似的其他内容。 本指南还适用于在 Azure 门户 web 基用户界面中执行的访问，浏览器直接访问你的密钥保管库。

按照专用链接的定义，应用程序、脚本或门户必须运行在连接到部署了 [专用终结点资源](../../private-link/private-endpoint-overview.md) 的虚拟网络的计算机、群集或环境中。

如果应用程序、脚本或门户是在连接 Internet 的任意网络上运行，则本指南不适用，可能无法使用专用链接。 此限制也适用于在 Azure Cloud Shell 中执行的命令，因为它们在按需提供的远程 Azure 计算机而不是用户浏览器中运行。

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>如果使用托管解决方案，请参阅特定文档

本指南不适用于 Microsoft 托管的解决方案，其中的密钥保管库由独立于客户虚拟网络的 Azure 产品访问。 此类方案的示例包括： Azure 存储或 Azure SQL 配置为静态加密、Azure 事件中心用客户提供的密钥加密数据、Azure 数据工厂访问存储在密钥保管库中的服务凭据、Azure Pipelines 从密钥保管库检索机密和其他类似的方案。 在这些情况下， *你必须检查产品是否支持启用了防火墙的密钥保管库*。 此支持通常通过 Key Vault firewall 的 " [受信任的服务](overview-vnet-service-endpoints.md#trusted-services) " 功能来执行。 不过，由于各种原因，许多产品并未包含在可信服务列表中。 在这种情况下，请访问特定于产品的支持。

少量的 Azure 产品支持 *vnet 注入*的概念。 简单地说，产品会将网络设备添加到客户虚拟网络，这样它就可以像部署到虚拟网络一样发送请求。 [Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)了一个值得注意的示例。 类似于这样的产品可以使用专用链接向密钥保管库发出请求，此故障排除指南可能会有所帮助。

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. 确认连接已批准并已成功

以下步骤验证专用终结点连接是否已批准并成功：

1. 打开 Azure 门户并打开密钥保管库资源。
2. 在左侧菜单中，选择 " **网络**"。
3. 单击 " **专用终结点连接** " 选项卡。这会显示所有专用终结点连接及其各自的状态。 如果没有连接，或者虚拟网络的连接丢失，则必须创建新的专用终结点。 稍后将对此进行介绍。
4. 仍在 **专用终结点连接**中，找到你正在诊断的连接，并确认 "连接状态" 已 **获批准** 并且 "设置状态" 已 **成功**。
    - 如果连接处于 "挂起" 状态，则可以直接批准。
    - 如果连接 "已拒绝"、"失败"、"错误"、"已断开" 或其他状态，则必须创建新的专用终结点资源。

最好删除无效的连接，以使其保持整洁。

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. 确认已正确配置密钥保管库防火墙

>[!IMPORTANT]
> 更改防火墙设置可能会从仍未使用专用链接的合法客户端中删除访问权限。 请确保你已了解防火墙配置中每个更改的含义。

一项重要的概念是，专用链接功能只 *提供* 对已关闭的虚拟网络中的密钥保管库的访问权限，以防止数据渗透。 它不会 *删除* 任何现有的访问权限。 为了有效地阻止来自公共 Internet 的访问，你必须显式启用 key vault 防火墙：

1. 打开 Azure 门户并打开密钥保管库资源。
2. 在左侧菜单中，选择 " **网络**"。
3. 请确保在顶部选择 " **防火墙和虚拟网络** " 选项卡。
4. 请确保选择 " **专用终结点和所选网络** " 选项。 如果找到 " **所有网络** "，则说明外部客户端仍然可以访问密钥保管库的原因。

以下语句还适用于防火墙设置：

- "专用链接" 功能不需要在 key vault 防火墙设置中指定任何 "虚拟网络"。 使用密钥保管库专用 IP 地址的所有请求 (参阅下一节) 必须工作，即使 key vault 防火墙设置中未指定任何虚拟网络也是如此。
- "专用链接" 功能不需要指定密钥保管库防火墙设置中的任何 IP 地址。 同样，使用 key vault 专用 IP 地址的所有请求都必须有效，即使防火墙设置中未指定任何 IP 地址也是如此。

如果使用的是专用链接，则在 key vault 防火墙中指定虚拟网络或 IP 地址的唯一动机是：

- 有一些混合系统，某些客户端使用专用链接，某些客户端使用服务终结点，一些使用公共 IP 地址。
- 正在转换到专用链接。 在这种情况下，确认所有客户端都使用专用链接后，应从 key vault 防火墙设置中删除虚拟网络和 IP 地址。

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. 确保密钥保管库具有专用 IP 地址

### <a name="difference-between-private-and-public-ip-addresses"></a>专用 IP 地址和公共 IP 地址之间的差异

专用 IP 地址始终具有以下格式之一：

- 2.x. x：例如： `10.1.2.3` 、 `10.56.34.12` 。
- 172.16. 到172.32：，示例： `172.20.1.1` ， `172.31.67.89` 。
- 192.168.：示例： `192.168.0.1` ， `192.168.100.7`

某些 IP 地址和范围是保留的：

- 224. x. x：多播
- 255.255.255.255：广播
- 127. x. x：环回
- 169.254.：本地链接
- 168.63.129.16：内部 DNS

所有其他 IP 地址都是公开的。

当你浏览门户或运行显示 IP 地址的命令时，请确保你可以确定该 IP 地址是专用、公用还是保留。 要使专用链接正常工作，密钥保管库主机名必须解析为属于虚拟网络地址空间的专用 IP 地址。

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>在虚拟网络中查找 key vault 专用 IP 地址

需要诊断主机名解析，并且必须知道启用了专用链接的密钥保管库的确切专用 IP 地址。 若要找到该地址，请按照此过程操作：

1. 打开 Azure 门户并打开密钥保管库资源。
2. 在左侧菜单中，选择 " **网络**"。
3. 单击 " **专用终结点连接** " 选项卡。这会显示所有专用终结点连接及其各自的状态。
4. 找到你正在诊断的用户并确认 "连接状态" 已 **获批准** 并且预配状态为 " **成功**"。 如果未看到此内容，请返回到本文档前面的部分。
5. 找到正确的项目后，单击 " **专用终结点** " 列中的链接。 这将打开专用终结点资源。
6. "概述" 页可能会显示一个名为 " **自定义 DNS 设置**" 的部分。 确认只有一个匹配密钥保管库主机名的条目。 该条目显示密钥保管库专用 IP 地址。
7. 还可以单击 " **网络接口** " 中的链接，并确认专用 IP 地址与上一步中显示的相同。 网络接口是表示密钥保管库的虚拟设备。

IP 地址是指 *在同一虚拟网络中运行* 的 vm 和其他设备将用于连接到密钥保管库的 IP 地址。 记下 IP 地址，或将 "浏览器" 选项卡保持打开状态，并在进行进一步调查时不进行触摸。

>[!NOTE]
> 如果密钥保管库有多个专用终结点，则它有多个专用 IP 地址。 仅当有多个虚拟网络访问同一密钥保管库（每个虚拟网络通过其自己的专用终结点 (专用终结点属于单个虚拟网络) 时，此方法才有用。 请确保诊断了正确虚拟网络的问题，并在上面的过程中选择正确的专用终结点连接。 此外， **不要** 为同一虚拟网络中的同一个 Key Vault 创建多个专用终结点。 这不是必需的，并且是一种混乱的原因。

## <a name="5-validate-the-dns-resolution"></a>5. 验证 DNS 解析

DNS 解析是将 key vault 主机名 (示例： `fabrikam.vault.azure.net`) 转换为 IP 地址的过程， (示例： `10.1.2.3`) 。 以下小节显示了每个方案中的 DNS 解析的预期结果。

### <a name="key-vaults-without-private-link"></a>没有专用链接的密钥保管库

本部分旨在学习用途。 如果密钥保管库在 "已批准" 状态下没有专用终结点连接，则解析主机名将产生类似于下面的结果：

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

你可以看到，名称解析为公共 IP 地址，并且没有 `privatelink` 别名。 别名稍后将进行说明，别担心。

无论计算机是连接到虚拟网络，还是使用 Internet 连接的任意计算机，都应使用以上结果。 出现这种情况的原因是 key vault 没有处于已批准状态的专用终结点连接，因此无需密钥保管库支持专用链接。

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>通过任意 Internet 计算机解析专用链接的密钥保管库

如果密钥保管库具有处于已批准状态的一个或多个专用终结点连接，并且你在连接到 Internet 的任意计算机上解析了主机名， (*未* 连接到专用终结点所在的虚拟网络的计算机) 中，你应找到以下内容：

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

与上一方案相比，有一些明显的差异是，有一个具有值的新别名 `{vaultname}.privatelink.vaultcore.azure.net` 。 这意味着密钥保管库数据平面已准备好接受来自专用链接的请求。

这并不意味着从虚拟网络 *外部* 的计算机执行的请求 (如您刚才使用的) 将使用专用链接。 您可以看到，主机名仍可解析为公共 IP 地址。 只有 *连接到虚拟网络的* 计算机才能使用专用链接。 有关详细信息，请参阅。

如果看不到 `privatelink` 别名，则表示密钥保管库的状态为0个专用终结点连接 `Approved` 。 请返回到 [此部分](#2-confirm-that-the-connection-is-approved-and-succeeded) ，然后重试。

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>通过虚拟网络解析专用链接的密钥保管库

如果 key vault 具有一个或多个处于已批准状态的专用终结点连接，并且你从连接到创建了专用终结点的虚拟网络的计算机中解析主机名，则这是预期的响应：

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

Linux：

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

有两个值得注意的差异。 首先，名称解析为专用 IP 地址。 这必须是我们在本文的 [相应部分](#find-the-key-vault-private-ip-address-in-the-virtual-network) 中找到的 IP 地址。 其次，它后面没有其他别名 `privatelink` 。 出现这种情况的原因是，虚拟网络 DNS 服务器 *截获* 别名链，并直接从名称返回专用 IP 地址 `fabrikam.privatelink.vaultcore.azure.net` 。 该条目实际上是 `A` 专用 DNS 区域中的一条记录。 有关详细信息，请参阅。

>[!NOTE]
> 上述结果仅发生在连接到创建了专用终结点的虚拟网络的虚拟机上。 如果虚拟网络中未部署包含专用终结点的 VM，请部署一个虚拟机并将其远程连接到该虚拟机，然后 `nslookup` (Windows) 或 `host` 上述命令 (Linux) 执行此命令。

如果在连接到创建了专用终结点的虚拟网络的虚拟机上运行这些命令，但 **没有** 显示密钥保管库专用 IP 地址，则下一部分可帮助解决此问题。

## <a name="6-validate-the-private-dns-zone"></a>6. 验证专用 DNS 区域

如果 DNS 解析未按上一部分所述的方式运行，则专用 DNS 区域可能有问题，此部分可能会有所帮助。 如果 DNS 解析显示了正确的密钥保管库专用 IP 地址，则您的专用 DNS 区域可能是正确的。 可以跳过此部分。

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>确认所需的专用 DNS 区域资源存在

你的 Azure 订阅必须具有具有此准确名称的 [专用 DNS 区域](../../dns/private-dns-privatednszone.md) 资源：

    privatelink.vaultcore.azure.net

可以通过转到门户中的 "订阅" 页，然后选择左侧菜单中的 "资源" 来检查此资源是否存在。 资源名称必须为 `privatelink.vaultcore.azure.net` ，且资源类型必须为 **专用 DNS 区域**。

通常，当你使用通用过程创建专用终结点时，将自动创建此资源。 但在某些情况下，不会自动创建此资源，您必须手动执行此操作。 此资源也可能已被意外删除。

如果没有此资源，请在订阅中创建新的专用 DNS 区域资源。 请记住，名称必须完全相同 `privatelink.vaultcore.azure.net` ，不包含空格或其他点。 如果指定的名称不正确，则本文中所述的名称解析将不起作用。 有关如何创建此资源的详细信息，请参阅 [使用 Azure 门户创建 Azure 专用 DNS 区域](../../dns/private-dns-getstarted-portal.md)。 如果遵循该页，则可以跳过虚拟网络创建，因为此时应该已经有一个。 你还可以跳过具有虚拟机的验证过程。

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>确认专用 DNS 区域是否已链接到虚拟网络

没有足够的专用 DNS 区域。 还必须将它链接到包含专用终结点的虚拟网络。 如果专用 DNS 区域未链接到正确的虚拟网络，则从该虚拟网络进行的任何 DNS 解析都将忽略专用 DNS 区域。

打开专用 DNS 区域资源，然后单击左侧菜单中的 " **虚拟网络链接** " 选项。 这会显示一个链接列表，每个链接都有一个虚拟网络流入量订阅的名称。 包含专用终结点资源的虚拟网络必须在此处列出。 如果它不存在，请添加它。 有关详细步骤，请参阅 [链接虚拟网络](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)。 可以取消选中 "启用自动注册"-该功能与专用链接无关。

专用 DNS 区域链接到虚拟网络后，来自虚拟网络的 DNS 请求将查找专用 DNS 区域中的名称。 在连接到创建了专用终结点的虚拟网络的虚拟机上执行正确的地址解析时，这是必需的。

>[!NOTE]
> 如果你刚刚保存了该链接，则此操作可能需要一段时间才能生效，即使在门户指示操作完成后也是如此。 你可能还需要重新启动用于测试 DNS 解析的虚拟机。

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>确认专用 DNS 区域包含正确的 A 记录

使用门户打开名称为的专用 DNS 区域 `privatelink.vaultcore.azure.net` 。 "概述" 页将显示所有记录。 默认情况下，会有一条记录的名称 `@` 和类型 `SOA` ，表示开始颁发机构。 别触摸。

要使密钥保管库名称解析正常工作，必须有一个 `A` 具有简单保管库名称且不带后缀或点的记录。 例如，如果主机名为 `fabrikam.vault.azure.net` ，则必须存在 `A` 名称 `fabrikam` 中不含任何后缀或点的记录。

而且， `A` (IP 地址) 记录的值必须是 [密钥保管库专用 IP 地址](#find-the-key-vault-private-ip-address-in-the-virtual-network)。 如果找到 `A` 记录但包含错误的 ip 地址，则必须删除错误的 ip 地址并添加新的 ip 地址。 建议删除整个 `A` 记录并添加一个新记录。

>[!NOTE]
> 无论何时删除或修改 `A` 记录，计算机仍可能会解析为旧 IP 地址，因为 TTL (生存时间) 值可能尚未过期。 建议你始终将 TTL 值指定为不到60秒 (一分钟) 且不超过600秒 (10 分钟) 。 如果指定的值太大，则客户端可能需要很长时间才能从中断中恢复。

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>针对多个虚拟网络的 DNS 解析

如果有多个虚拟网络，并且每个虚拟网络都有其自己的专用终结点资源引用相同的密钥保管库，则密钥保管库主机名需要解析为不同的专用 IP 地址，具体取决于网络。 这意味着还需要多个专用 DNS 区域，每个区域链接到不同的虚拟网络并在记录中使用不同的 IP 地址 `A` 。

在更高级的方案中，虚拟网络可能已启用对等互连。 在这种情况下，只有一个虚拟网络需要专用终结点资源，尽管这两个虚拟网络可能需要链接到专用 DNS 区域资源。 此文档不直接涵盖这种情况。

### <a name="understand-that-you-have-control-over-dns-resolution"></a>了解你可以控制 DNS 解析

如 [前一部分](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)所述，具有专用链接的密钥保管库 `{vaultname}.privatelink.vaultcore.azure.net` 在其 *公共* 注册中具有别名。 虚拟网络使用的 DNS 服务器使用公共注册，但会检查每个别名是否存在 *专用* 注册，如果找到，它将停止在公共注册中定义的以下别名。

此逻辑表示，如果虚拟网络链接到名为的专用 DNS 区域 `privatelink.vaultcore.azure.net` ，并且 key vault 的公共 DNS 注册具有别名 `fabrikam.privatelink.vaultcore.azure.net` (请注意，key vault 主机名后缀与专用 DNS 区域名称精确匹配) ，则 DNS 查询将查找 `A` 名称 `fabrikam` *在专用 DNS 区域中*的记录。 如果 `A` 找到该记录，则会在 DNS 查询中返回其 IP 地址，并且不会在公共 DNS 注册中执行进一步的查找。

正如您所看到的，名称解析在您的控制之下。 此设计的基本原理是：

- 可能有一个复杂的方案涉及到自定义 DNS 服务器，并与本地网络集成。 在这种情况下，需要控制如何将名称转换为 IP 地址。
- 可能需要访问没有专用链接的密钥保管库。 在这种情况下，解析虚拟网络中的主机名时必须返回公共 IP 地址，这是因为没有专用链接的密钥保管库 `privatelink` 在名称注册中没有别名。

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. 验证对 key vault 的请求是否使用专用链接

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>查询 `/healthstatus` 密钥保管库的终结点

Key vault 提供 `/healthstatus` 终结点，可用于诊断。 响应标头包含源 IP 地址（由密钥保管库服务查看）。 您可以使用以下命令调用该终结点 (**记得使用 key vault 主机名**) ：

Windows (PowerShell) ：

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux 或最新版本的 Windows 10，其中包括 `curl` ：

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

如果未获得类似于的输出，或者出现网络错误，则意味着无法通过示例) 中指定 (的主机名来访问密钥保管库 `fabrikam.vault.azure.net` 。 主机名未解析为正确的 IP 地址，或者在传输层出现连接问题。 这可能是由于路由问题、包删除和其他原因造成的。 需要进一步调查。

响应必须包含标头 `x-ms-keyvault-network-info` ：

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

`addr`标头中的字段 `x-ms-keyvault-network-info` 显示请求的源的 IP 地址。 此 IP 地址可以是以下项之一：

- 执行请求的计算机的专用 IP 地址。 这表明请求使用的是私有链接或服务终结点。 这是专用链接的预期结果。
- 其他一些专用 IP 地址， *而不* 是来自执行请求的计算机。 这表明某些自定义路由是有效的。 它仍表示请求使用的是私有链接或服务终结点。
- 一些公共 IP 地址。 这表明请求将通过网关 (NAT) 设备路由到 Internet。 这表明请求未使用专用链接，并且某些问题需要修复。 导致这种情况的常见原因是 1) 专用终结点未处于已批准状态和成功状态;2) 主机名未解析为密钥保管库的专用 IP 地址。 本文包括这两种情况下的故障排除操作。

>[!NOTE]
> 如果请求 `/healthstatus` 工作，但 `x-ms-keyvault-network-info` 缺少标头，则终结点可能不是由密钥保管库提供服务的。 由于上述命令也会验证 HTTPS 证书，缺少的标头可能是篡改的符号。

### <a name="query-the-key-vault-ip-address-directly"></a>直接查询 key vault IP 地址

>[!IMPORTANT]
> 不使用 HTTPS 证书验证访问密钥保管库是危险的，只能用于学习目的。 如果没有进行此客户端验证，则生产代码决不能访问密钥保管库。 即使你只是诊断问题，你可能会受到篡改尝试，如果你经常在对密钥保管库的请求中禁用 HTTPS 证书验证，则将不会显示这些尝试。

如果你安装了 PowerShell 的最新版本，则可以使用 `-SkipCertificateCheck` 跳过 HTTPS 证书检查，然后可以直接针对 [密钥保管库 IP 地址](#find-the-key-vault-private-ip-address-in-the-virtual-network) ：

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

如果使用的是 `curl` ，则可以使用参数执行相同 `-k` 操作：

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

响应必须与上一节中的相同，这意味着它必须包含 `x-ms-keyvault-network-info` 具有相同值的标头。 `/healthstatus`如果你使用的是 key vault 主机名或 IP 地址，则终结点不会有任何问题。

如果看到 `x-ms-keyvault-network-info` 使用 key vault 主机名为请求返回一个值，并且使用 IP 地址返回该请求的另一个值，则每个请求都面向不同的终结点。 请参阅 `addr` `x-ms-keyvault-network-info` 上一节中字段的说明，以确定哪种情况是错误的，需要修复。

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. 导致影响的其他更改和自定义

以下各项不是穷举调查操作。 他们将告诉你查找其他问题的位置，但你必须具备高级网络知识才能修复这些方案中的问题。

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>在虚拟网络中诊断自定义 DNS 服务器

在门户中，打开 "虚拟网络" 资源。 在左侧菜单中，打开 " **DNS 服务器**"。 如果你使用的是 "自定义"，则 DNS 解析可能不如本文档中所述。 必须诊断 DNS 服务器解析密钥保管库主机名的方式。

如果你使用的是默认的 Azure 提供的 DNS 服务器，则此文档适用。

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>在虚拟机上诊断主机重写或自定义 DNS 服务器

许多操作系统允许为每个主机名设置一个显式固定 IP 地址，这通常是通过更改文件来实现的 `hosts` 。 它们还可能允许替代 DNS 服务器。 如果你使用其中一个方案，请继续使用系统特定的诊断选项。

### <a name="promiscuous-proxies-fiddler-etc"></a>混合代理 (Fiddler 等 ) 

除非明确注明，否则本文中的诊断选项仅适用于环境中不存在的混合代理。 尽管这些代理通常以独占方式安装到正在被诊断的计算机中 (Fiddler 是最常见的示例) ，但高级管理员可能会将根证书颁发机构覆盖 (Ca) 并在为网络中的多台计算机提供服务的网关设备上安装混合代理。 这些代理会显著影响安全性和可靠性。 Microsoft 不支持使用此类产品的配置。

### <a name="other-things-that-may-affect-connectivity"></a>可能影响连接的其他因素

这是在高级或复杂方案中可以找到的项的不完整列表：

- 防火墙设置，连接到虚拟网络的 Azure 防火墙，或者在虚拟网络或计算机中部署的自定义防火墙解决方案。
- 网络对等互连，这可能会影响使用的 DNS 服务器以及流量的路由方式。
- 自定义网关 (NAT) 解决方案，这可能会影响流量的路由方式，包括来自 DNS 查询的流量。
