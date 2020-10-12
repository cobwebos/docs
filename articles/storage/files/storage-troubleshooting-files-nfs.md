---
title: 排查 Azure NFS 文件共享问题-Azure 文件
description: 排查 Azure NFS 文件共享问题。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708614"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>排查 Azure NFS 文件共享问题

本文列出了一些与 Azure NFS 文件共享相关的常见问题。 其中提供了这些问题的潜在原因和解决方法。

## <a name="unable-to-create-an-nfs-share"></a>无法创建 NFS 共享

### <a name="cause-1-subscription-is-not-enabled"></a>原因1：订阅未启用

你的订阅可能尚未注册 Azure 文件 NFS 预览版。 要启用该功能，需要从 Cloud Shell 或本地终端运行一些附加的 commandlet。

> [!NOTE]
> 你可能需要等待30分钟才能完成注册。


#### <a name="solution"></a>解决方案

使用以下脚本注册功能和资源提供程序，并 `<yourSubscriptionIDHere>` 在运行脚本前替换：

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>原因2：不支持的存储帐户设置

NFS 只能在具有以下配置的存储帐户上使用：

- 层-高级
- 帐户类型-FileStorage
- 冗余-LRS
- 区域-美国东部、美国东部2、英国南部、东南亚

#### <a name="solution"></a>解决方案

按照本文中的说明进行操作： [如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)。

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>原因3：存储帐户是在注册完成之前创建的

为了使存储帐户使用此功能，必须在订阅完成 NFS 注册后创建该帐户。 注册完成最多可能需要30分钟。

#### <a name="solution"></a>解决方案

完成注册后，请按照本文中的说明进行操作： [如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)。

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>无法连接或装载 Azure NFS 文件共享

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因1：请求来自不受信任的网络/不受信任的 IP 中的客户端

不同于 SMB，NFS 没有基于用户的身份验证。 共享的身份验证基于网络安全规则配置。 因此，为了确保仅为你的 NFS 共享建立安全连接，你必须使用服务终结点或专用终结点。 若要从本地访问共享以及专用终结点，你必须设置 VPN 或 ExpressRoute。 将忽略添加到存储帐户的允许列表中的 Ip。 必须使用以下方法之一来设置对 NFS 共享的访问权限：


- [服务终结点](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 由公共终结点访问
    - 仅在同一区域中可用。
    - VNet 对等互连不会授予对共享的访问权限。
    - 每个虚拟网络或子网必须分别添加到允许列表中。
    - 对于本地访问，服务终结点可用于 ExpressRoute、点到站点和站点到站点 Vpn，但我们建议使用专用终结点，因为它更安全。

下图描述了使用公共终结点的连接。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="公共终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 访问比服务终结点更安全。
    - 可在存储帐户的 Azure 区域内部和外部访问 NFS 共享， (跨区域本地) 
    - 虚拟网络对等互连在专用终结点中托管的虚拟网络允许 NFS 共享访问对等互连虚拟网络中的客户端。
    - 专用终结点可用于 ExpressRoute、点到站点和站点到站点 Vpn。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="公共终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因2：需要安全传输

目前尚不支持 NFS 共享。 Azure 为使用 MACSec 的 Azure 数据中心之间传输的所有数据提供加密层。 只能从受信任的虚拟网络和 VPN 隧道访问 NFS 共享。 NFS 共享上没有其他传输层加密可用。

#### <a name="solution"></a>解决方案

禁用存储帐户的配置边栏选项卡中所需的安全传输。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="公共终结点连接的关系图。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因3：未安装 nfs-公共包
在运行 mount 命令之前，请通过运行以下命令来安装包。

若要检查 NFS 包是否已安装，请运行： `rpm qa | grep nfs-utils`

#### <a name="solution"></a>解决方案

如果未安装此包，请在分发中安装包。

##### <a name="ubuntu-or-debian"></a>Ubuntu 或 Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8 +、CentOS 8 +

使用 dnf 包管理器： `sudo dnf install nfs-common` 。

旧版本的 Red Hat Enterprise Linux 和 CentOS 使用 yum 包管理器： `sudo yum install nfs-common` 。

##### <a name="opensuse"></a>openSUSE

使用 zypper 包管理器： `sudo zypper install-nfscommon` 。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因4：防火墙阻止端口2049

NFS 协议通过端口2049与服务器通信，请确保此端口已打开到 NFS) 服务器 (的存储帐户。

#### <a name="solution"></a>解决方案

运行以下命令，验证是否已在客户端上打开端口2049： `telnet <storageaccountnamehere>.file.core.windows.net 2049` 。 如果端口未打开，请将其打开。

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。