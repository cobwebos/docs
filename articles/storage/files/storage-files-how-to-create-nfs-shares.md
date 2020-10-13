---
title: 创建 NFS 共享-Azure 文件
description: 了解如何创建可使用网络文件系统协议装载的 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2a848cb77336fc89172d55a6204d66b9e5be5976
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705225"
---
# <a name="how-to-create-an-nfs-share"></a>如何创建 NFS 共享

Azure 文件共享是位于云中的完全托管文件共享。 它们可以使用服务器消息块协议或网络文件系统 (NFS) 协议进行访问。 本文介绍如何创建使用 NFS 协议的文件共享。 有关这两种协议的详细信息，请参阅 [Azure 文件共享协议](storage-files-compare-protocols.md)。

## <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>先决条件

- 创建 [FileStorage 帐户](storage-how-to-create-premium-fileshare.md)。

    > [!IMPORTANT]
    > 只能从受信任的网络访问 NFS 共享。 与 NFS 共享的连接必须来自以下源之一：

    - [创建专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint) (建议) 或[限制对公共终结点的访问](storage-files-networking-endpoints.md#restrict-public-endpoint-access)。
    - [在 Linux 上配置点到站点 (P2S) 用于 Azure 文件的 VPN](storage-files-configure-p2s-vpn-linux.md)。
    - [配置站点到站点 VPN 以用于 Azure 文件](storage-files-configure-s2s-vpn.md)。
    - 配置 [ExpressRoute](../../expressroute/expressroute-introduction.md)。
- 如果你打算使用 Azure CLI，请[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="register-the-nfs-41-protocol"></a>注册 NFS 4.1 协议

如果使用的是 Azure PowerShell 模块或 Azure CLI，请使用以下命令注册功能：

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares
                    --namespace Microsoft.Storage
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>验证功能是否已注册

注册批准最多可能需要一小时。 若要验证注册是否完成，请使用以下命令：

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>创建 NFS 共享

# <a name="portal"></a>[门户](#tab/azure-portal)

现在，你已创建 FileStorage 帐户并配置了网络，接下来可以创建一个 NFS 文件共享。 此过程类似于创建 SMB 共享，在创建共享时选择 **NFS** 而不是 **smb** 。

1. 导航到存储帐户，然后选择“文件共享”。
1. 选择“+ 文件共享”创建新的文件共享。
1. 命名文件共享，选择预配的容量。
1. 对于 **协议** ，请选择 " **NFS (预览") **。
1. 对于 **Root Squash** 进行选择。

    - 根 squash (默认) -远程超级用户 (根) 的访问权限映射到 UID (65534) 和 GID (65534) 。
    - 根 squash-远程超级用户 (根) 以 root 身份接收访问权限。
    - 所有 squash-所有用户访问映射到 UID (65534) 和 GID (65534) 。
    
1. 选择“创建”  。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="文件共享创建边栏选项卡的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 确保已安装 .NET Framework。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
 
1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. 安装最新版本的 PowershellGet 模块。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. 关闭 PowerShell 控制台，然后重新将其打开。

1. 安装 **Az** 预览 module **2.5.2**。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. 若要使用 Azure PowerShell 模块创建高级文件共享，请使用 [AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet。

> [!NOTE]
> 预配的共享大小按共享配额指定，文件共享按预配大小计费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建高级文件共享，请使用 [az storage share create](/cli/azure/storage/share-rm) 命令。

> [!NOTE]
> 预配的共享大小按共享配额指定，文件共享按预配大小计费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

```azurecli-interactive
az storage share-rm create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol NFS \
    --root-access RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>后续步骤

现在，你已创建了一个 NFS 共享，若要使用它，则必须将其装载到 Linux 客户端。 有关详细信息，请参阅 [如何装载 NFS 共享](storage-files-how-to-mount-nfs-shares.md)。

如果遇到任何问题，请参阅 [排查 AZURE NFS 文件共享](storage-troubleshooting-files-nfs.md)问题。