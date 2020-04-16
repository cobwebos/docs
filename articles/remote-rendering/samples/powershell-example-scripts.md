---
title: PowerShell 脚本示例
description: 演示如何通过 PowerShell 脚本使用前端的示例
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891486"
---
# <a name="example-powershell-scripts"></a>PowerShell 脚本示例

Azure 远程渲染提供以下两个 REST API：

- [转换 REST API](../how-tos/conversion/conversion-rest-api.md)
- [会话 REST API](../how-tos/session-rest-api.md)

[ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)的 Scripts 文件夹中包含了用来与服务的 REST API 进行交互的示例脚本。  本文介绍这些脚本的用法。

## <a name="prerequisites"></a>先决条件

若要执行示例脚本，需要正确安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)。

1. 安装 Azure PowerShell：
    1. 以管理员权限打开 PowerShell
    1. 运行：`Install-Module -Name Az -AllowClobber`

1. 如果出现了有关运行脚本的错误，请确保正确设置[执行策略](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)：
    1. 以管理员权限打开 PowerShell
    1. 运行：`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [准备 Azure 存储帐户](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. 登录到包含 Azure 远程渲染帐户的订阅：
    1. 打开 PowerShell
    1. 运行 `Connect-AzAccount`，并按照屏幕上的指示进行操作。

> [!NOTE]
> 如果你的组织有多个订阅，你可能需要指定 SubscriptionId 和 Tenant 参数。 在 [Connect-AzAccount 文档](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)中可以找到详细信息。

1. 从 [Azure 远程渲染 GithHub 存储库](https://github.com/Azure/azure-remote-rendering)下载 Scripts 文件夹。 

## <a name="configuration-file"></a>配置文件

在 `.ps1` 文件的旁边，有一个需要填写的 `arrconfig.json`：

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> 请务必使用双反斜杠“\\\\”来正确转义 LocalAssetDirectoryPath 路径中的反斜杠，并在 inputFolderPath 和 inputAssetPath 等所有其他路径中使用正斜杠“/”。

### <a name="accountsettings"></a>accountSettings

对于 `arrAccountId` 和 `arrAccountKey`，请参阅[创建 Azure 远程渲染帐户](../how-tos/create-an-account.md)。
对于 `region`，请参阅[可用区域列表](../reference/regions.md)。

### <a name="renderingsessionsettings"></a>renderingSessionSettings

若要运行 RenderingSession.ps1，必须填写此结构。 

- **vmSize：** 选择虚拟机的大小。 选择“标准”或“高级”。   请关闭不再需要的渲染会话。
- **maxLeaseTime：** 要租用 VM 的持续时间。 租约过期后，该 VM 将会关闭。 以后可以延长租用时间（请参阅下文）。

### <a name="assetconversionsettings"></a>assetConversionSettings

若要运行 Conversion.ps1，必须填写此结构。 

有关详细信息，请参阅[准备 Azure 存储帐户](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)。

## <a name="script-renderingsessionps1"></a>脚本：RenderingSession.ps1

此脚本用于创建、查询和停止渲染会话。

> [!IMPORTANT]
> 请确保已填写 arrconfig.json 中的 accountSettings 和 renderingSessionSettings 部分。  

### <a name="create-a-rendering-session"></a>创建渲染会话

如果有了完全填写的 arrconfig.json，那么常规的用法如下：

```PowerShell
.\RenderingSession.ps1
```

此脚本将调用[会话管理 REST API](../how-tos/session-rest-api.md) 来运转采用指定设置的渲染 VM。 成功时，它会检索 sessionId。  然后，它将轮询会话属性，直到会话准备就绪或出错。

若要使用备用配置文件： 

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

可以重写配置文件中的各项设置： 

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

如果只是启动会话而不轮询，可以使用： 

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

必须将脚本检索的 sessionId 传递给其他大部分会话命令。 

### <a name="retrieve-session-properties"></a>检索会话属性

若要获取会话的属性，请运行：

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

使用 `-Poll` 来等待会话准备就绪或出错。 

### <a name="list-active-sessions"></a>列出活动会话

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>停止会话

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>更改会话属性

目前我们仅支持更改会话的 maxLeaseTime。

> [!NOTE]
> 租用时间始终从最初创建会话 VM 时开始算起。 因此，若要将会话租用时间延长一小时，请将 maxLeaseTime 增大一小时。 

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>脚本：Conversion.ps1

此脚本用于将输入模型转换成特定于 Azure 远程渲染的运行时格式。

> [!IMPORTANT]
> 请确保已填写 arrconfig.json 中的 accountSettings 和 assetConversionSettings 部分。  

该脚本演示两个用于在服务中使用存储帐户的选项：

- 与 Azure 远程渲染帐户相链接的存储帐户
- 通过共享访问签名 (SAS) 提供对存储的访问

### <a name="linked-storage-account"></a>链接的存储帐户

完全填写 arrconfig.json 并链接存储帐户后，可使用以下命令。 [创建帐户](../how-tos/create-an-account.md#link-storage-accounts)中介绍了如何链接存储帐户。

使用链接的存储帐户是使用转换服务的首选方式，因为这样就无需生成共享访问签名。

```PowerShell
.\Conversion.ps1
```

1. 将 `assetConversionSettings.modelLocation` 中包含的所有文件上传到输入 Blob 容器中的给定 `inputFolderPath` 下
1. 调用[模型转换 REST API](../how-tos/conversion/conversion-rest-api.md) 启动[模型转换](../how-tos/conversion/model-conversion.md)
1. 轮询转换状态，直到转换成功或失败
1. 已转换的文件位置的输出详细信息（存储帐户、输出容器、文件在容器中的路径）

### <a name="access-to-storage-via-shared-access-signatures"></a>通过共享访问签名访问存储

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

这会：

1. 将 `assetConversionSettings.localAssetDirectoryPath` 中的本地文件上传到输入 Blob 容器
1. 为输入容器生成 SAS URI
1. 为输出容器生成 SAS URI
1. 调用[模型转换 REST API](../how-tos/conversion/conversion-rest-api.md) 启动[模型转换](../how-tos/conversion/model-conversion.md)
1. 轮询转换状态，直到转换成功或失败
1. 已转换的文件位置的输出详细信息（存储帐户、输出容器、文件在容器中的路径）
1. 将 SAS URI 输出到输出 Blob 容器中已转换的模型

### <a name="additional-command-line-options"></a>其他命令行选项

若要使用备用配置文件： 

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

如果只是启动模型转换而不轮询，可以使用： 

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

可以使用以下命令行开关重写配置文件中的各项设置： 

* **Id：** 与 GetConversionStatus 配合使用的 ConversionId
* **ArrAccountId：** accountSettings 的 arrAccountId
* **ArrAccountKey：** accountSettings 的 arrAccountKey 重写
* **Region：** accountSettings 的 region 重写
* **ResourceGroup：** assetConversionSettings 的 resourceGroup 重写
* **StorageAccountName：** assetConversionSettings 的 storageAccountName 重写
* **BlobInputContainerName：** assetConversionSettings 的 blobInputContainer 重写
* **LocalAssetDirectoryPath：** assetConversionSettings 的 localAssetDirectoryPath 重写
* **InputAssetPath：** assetConversionSettings 的 inputAssetPath 重写
* **BlobOutputContainerName：** assetConversionSettings 的 blobOutputContainerName 重写
* **OutputFolderPath：** assetConversionSettings 的 outputFolderPath 重写
* **OutputAssetFileName：** assetConversionSettings 的 outputAssetFileName 重写

例如，可按如下所示组合多个给定的选项：

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>运行单个转换阶段

若要运行单个过程步骤，可以使用：

仅上传给定 LocalAssetDirectoryPath 中的数据

```PowerShell
.\Conversion.ps1 -Upload
```

仅对已上传到 Blob 存储的模型启动转换过程（不运行上传，且不轮询转换状态）。该脚本将返回 conversionId。 

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

可使用以下命令检索此转换过程的转换状态：

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

使用 `-Poll` 等待转换完成或出错。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Unity 渲染模型](../quickstarts/render-model.md)
- [快速入门：转换要渲染的模型](../quickstarts/convert-model.md)
- [模型转换](../how-tos/conversion/model-conversion.md)
