---
title: 教程：使用 Azure Stack Edge GPU 将数据传输到存储帐户 | Microsoft Docs
description: 了解如何在 Azure Stack Edge GPU 设备上添加本地和 Edge 存储帐户并与其连接。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 66c09b7fa0eb8fbf709441da5996fb2237b3e284
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256390"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-gpu"></a>教程：使用 Azure Stack Edge GPU 通过存储帐户传输数据 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教程介绍如何在 Azure Stack Edge 设备上添加存储帐户并与其连接。 添加存储帐户后，Azure Stack Edge 可将数据传输到 Azure。

此过程可能需要大约 30 分钟才能完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 添加存储帐户
> * 连接到存储帐户

 
## <a name="prerequisites"></a>先决条件

在向 Azure Stack Edge 添加存储帐户之前，请确保：

- 已按照[安装 Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md) 中所述安装物理设备。

- 已按照[激活 Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md) 中所述激活物理设备。


## <a name="add-an-edge-storage-account"></a>添加 Edge 存储帐户

若要创建 Edge 存储帐户，请执行以下过程：

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>连接到 Edge 存储帐户

现在可以通过 http 或 https 连接到 Edge 存储 REST API。

- https 是安全且推荐的方式。
- 通过受信任的网络连接时，可以使用 http。

## <a name="connect-via-http"></a>通过 http 进行连接

通过 http 连接到 Edge 存储 REST API 需要执行以下步骤：

- 将 Azure 一致服务 VIP 和 blob 服务终结点添加到远程主机
- 验证连接 

后续部分将介绍其中的每个步骤。

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>将设备 IP 地址和 blob 服务终结点添加到远程客户端

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>验证连接

若要验证连接，通常需要上一步中收集的以下信息（可能会有所不同）：

- 存储帐户名称。
- 存储帐户访问密钥。
- blob 服务终结点。

你已经具有存储帐户名称和 blob 服务终结点。 可以使用 Azure PowerShell 客户端通过 Azure 资源管理器连接到设备，以获取存储帐户访问密钥。

按照[通过 Azure 资源管理器连接到设备](azure-stack-edge-j-series-connect-resource-manager.md)中的步骤进行操作。 通过 Azure 资源管理器登录到本地设备 API 后，获取设备上的存储帐户列表。 运行以下 cmdlet：

`Get-AzureRMStorageAccount`

从设备上的存储帐户列表中，确定需要获取其访问密钥的存储帐户。 注意存储帐户名称和资源组。

下面显示了示例输出：

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

若要获取访问密钥，请运行以下 cmdlet：

`Get-AzureRmStorageAccountAccessKey`

下面显示了示例输出：

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

复制并保存此密钥。 你将使用此密钥通过 Azure 存储资源管理器来验证连接。

若要验证是否已成功建立连接，请使用存储资源管理器来附加到外部存储帐户。 如果没有存储资源管理器，请[下载存储资源管理器](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)。

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>通过 https 进行连接

通过 https 连接到 Azure Blob 存储 REST API 需要执行以下步骤：

- 获取 blob 终结点证书
- 在客户端或远程主机上导入证书
- 将设备 IP 和 Blob 服务终结点添加到客户端或远程主机
- 配置并验证连接

后续部分将介绍其中的每个步骤。

### <a name="get-certificate"></a>获取证书

通过 HTTPS 访问 Blob 存储需要设备的 SSL 证书。 此外，还要将此证书作为 .pfx 文件（附加了私钥）上传到 Azure Stack Edge 设备。 有关如何创建（仅用于测试和开发目的）这些证书并将其上传到 Azure Stack Edge 设备的详细信息，请转到：

- [创建 blob 终结点证书](azure-stack-edge-j-series-manage-certificates.md#create-certificates-optional)。
- [上传 blob 终结点证书](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)。
- [在访问设备的客户端上导入证书](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)。

### <a name="import-certificate"></a>导入证书

如果使用 Azure 存储资源管理器连接到设备上的存储帐户，则还需要以 PEM 格式将证书导入到存储资源管理器中。 在 Windows 环境中，Base-64 编码的 .cer 与 PEM 格式相同。

通过执行以下步骤在 Azure 存储资源管理器上导入证书：

1. 确保 Azure 存储资源管理器以 Azure Stack API 为目标。 转到“编辑”>“以 Azure Stack API 为目标”。 出现提示时，重启存储资源管理器以使更改生效。

2. 若要导入 SSL 证书，请转到“编辑”>“SSL 证书”>“导入证书”。

  
   ![将证书导入存储资源管理器](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. 导航并提供签名链和 blob 证书。 签名链和 blob 证书均应采用 PEM 格式，该格式与 Windows 系统上的 Base64 编码格式相同。 系统会通知你已成功导入证书。


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>添加设备 IP 地址和 Blob 服务终结点

在 http  上进行连接时，请按照相同的步骤[添加设备 IP 地址和 blob 服务终结点](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client)。

### <a name="configure-and-verify-connection"></a>配置并验证连接

按照通过 http 进行连接时使用的步骤[配置并验证连接](#verify-connection)。 唯一的差别在于，应将“使用 http 选项”保留未选中状态。 

## <a name="next-steps"></a>后续步骤

本教程介绍了以下 Azure Stack Edge 主题：

> [!div class="checklist"]
> * 添加存储帐户
> * 连接到存储帐户

若要了解如何使用 Azure Stack Edge 转换数据，请继续学习下一教程：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge 转换数据](./azure-stack-edge-j-series-deploy-configure-compute.md)


