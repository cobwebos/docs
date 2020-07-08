---
title: 将 Azure Blob 存储用于模型转换
description: 介绍为模型转换设置和使用 blob 存储的常见步骤。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681644"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>将 Azure Blob 存储用于模型转换

[模型转换](model-conversion.md)服务需要访问 Azure blob 存储，以便可以检索输入数据和存储输出数据。 本文介绍如何执行最常见的步骤。

## <a name="prepare-azure-storage-accounts"></a>准备 Azure 存储帐户

- 创建存储帐户（StorageV2）
- 在存储帐户中创建一个输入 blob 容器（例如，名为 "arrinput"）
- 在存储帐户中创建一个输出 blob 容器（例如，名为 "arroutput"）

> [!TIP]
> 有关如何设置存储帐户的分步说明，请参阅[快速入门：转换用于呈现的模型](../../quickstarts/convert-model.md)

可以通过以下工具之一来创建存储帐户和 blob 容器：

- [Azure 门户](https://portal.azure.com)
- [az 命令行](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
- Sdk （c #、Python ...）

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>确保 Azure 远程呈现可以访问你的存储帐户

Azure 远程呈现需要从存储帐户检索模型数据，并将数据写回。

可以通过以下两种方式授予对存储帐户的 Azure 远程呈现访问权限：

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>将 Azure 存储帐户与 Azure 远程呈现帐户连接

按照[创建帐户](../create-an-account.md#link-storage-accounts)部分中提供的步骤进行操作。

### <a name="retrieve-sas-for-the-storage-containers"></a>检索存储容器的 SAS

存储访问签名（SAS）用于授予对输入和输出访问权限的读取访问权限。 建议每次转换模型时生成新的 Uri。 由于 Uri 将在一段时间后过期，将它们保持较长的持续时间可能会导致应用程序意外中断。

有关 SAS 的详细信息，请参阅[sas 文档](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

可以使用以下项之一生成 SAS URI：

- az PowerShell module
  - 请参阅[PowerShell 脚本示例](../../samples/powershell-example-scripts.md)
- [az 命令行](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
  - 右键单击容器 "获取共享访问签名" （读取、列出输入容器的访问权限、对输出容器的写访问权限）
- Sdk （c #、Python ...）

Conversion.ps1 [Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)中显示了在资产转换中使用共享访问签名的示例。

## <a name="upload-an-input-model"></a>上载输入模型

若要开始转换模型，需要使用以下选项之一上载它：

- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)-一种方便的 UI，可用于上传/下载/管理 Azure blob 存储上的文件
- [Azure 命令行](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 请参阅[PowerShell 脚本示例](../../samples/powershell-example-scripts.md)
- [使用存储 SDK （Python、c # ...）](https://docs.microsoft.com/azure/storage/)
- [使用 Azure 存储 REST Api](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

有关如何上传用于转换的数据的示例，请参阅[Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1。

## <a name="get-a-sas-uri-for-the-converted-model"></a>获取转换后的模型的 SAS URI

此步骤类似于[检索存储容器的 SAS](#retrieve-sas-for-the-storage-containers)。 但是，这次需要检索模型文件的 SAS URI，该 URI 已写入到输出容器。

例如，若要通过[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)检索 SAS URI，请右键单击模型文件，然后选择 "获取共享访问签名"。

如果尚未将存储帐户连接到 Azure 远程呈现帐户，则需要使用共享访问签名（SAS）来加载模型。 你可以在[创建帐户](../create-an-account.md#link-storage-accounts)中了解如何连接你的帐户。

## <a name="next-steps"></a>后续步骤

- [配置模型转换](configure-model-conversion.md)
- [模型转换 REST API](conversion-rest-api.md)
