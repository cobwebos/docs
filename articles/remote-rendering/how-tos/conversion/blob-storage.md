---
title: 使用 Azure Blob 存储进行模型转换
description: 描述设置和使用 blob 存储进行模型转换的常见步骤。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681644"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>使用 Azure Blob 存储进行模型转换

[模型转换](model-conversion.md)服务需要访问 Azure Blob 存储，以便它可以检索输入数据和存储输出数据。 本文介绍如何执行最常见的步骤。

## <a name="prepare-azure-storage-accounts"></a>准备 Azure 存储帐户

- 创建存储帐户 （存储 V2）
- 在存储帐户中创建输入 blob 容器（例如，名为"arrinput"）
- 在存储帐户中创建输出 Blob 容器（例如，名为"arr输出"）

> [!TIP]
> 有关如何设置存储帐户的分步说明，请查看[快速入门：转换用于渲染的模型](../../quickstarts/convert-model.md)

存储帐户和 blob 容器的创建可以使用以下工具之一完成：

- [Azure 门户](https://portal.azure.com)
- [az 命令行](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 存储浏览器](https://azure.microsoft.com/features/storage-explorer/)
- SDK（C#，Python ...

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>确保 Azure 远程呈现可以访问存储帐户

Azure 远程 Rending 需要从存储帐户检索模型数据，并将数据写回存储帐户。

您可以通过以下两种方式授予 Azure 远程呈现对存储帐户的访问权限：

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>将 Azure 存储帐户与 Azure 远程呈现帐户连接

按照"[创建帐户](../create-an-account.md#link-storage-accounts)"部分中给出的步骤操作。

### <a name="retrieve-sas-for-the-storage-containers"></a>检索存储容器的 SAS

存储的访问签名 （SAS） 用于授予输入的读取访问权限和输出的写入访问权限。 我们建议在每次转换模型时生成新的 URI。 由于 URI 会在一段时间后过期，因此将其保留更长时间可能会意外中断应用程序。

有关 SAS 的详细信息，请参阅[SAS 文档](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

可以使用：

- az PowerShell 模块
  - 请参阅[示例 PowerShell 脚本](../../samples/powershell-example-scripts.md)
- [az 命令行](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure 存储浏览器](https://azure.microsoft.com/features/storage-explorer/)
  - 右键单击容器"获取共享访问签名"（读取、列表访问输入容器，写入输出容器的访问）
- SDK（C#，Python ...

在资产转换中使用共享访问签名的示例显示在[Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1 中。

## <a name="upload-an-input-model"></a>上传输入模型

要开始转换模型，您需要使用以下选项之一上载模型：

- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)- 在 Azure Blob 存储上上载/下载/管理文件的便捷 UI
- [Azure 命令行](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - 请参阅[示例电源外壳脚本](../../samples/powershell-example-scripts.md)
- [使用存储 SDK（Python、C# ...](https://docs.microsoft.com/azure/storage/)
- [使用 Azure 存储 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

有关如何上传数据进行转换的示例，请参阅[Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1。

## <a name="get-a-sas-uri-for-the-converted-model"></a>获取转换后的模型的 SAS URI

此步骤类似于[检索存储容器的 SAS。](#retrieve-sas-for-the-storage-containers) 但是，这一次您需要检索已写入输出容器的模型文件的 SAS URI。

例如，要通过[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)检索 SAS URI，请右键单击模型文件并选择"获取共享访问签名"。

如果尚未将存储帐户连接到 Azure 远程呈现帐户，则需要共享访问签名 （SAS） 来加载模型。 您可以在["创建帐户](../create-an-account.md#link-storage-accounts)"中了解如何连接您的帐户。

## <a name="next-steps"></a>后续步骤

- [配置模型转换](configure-model-conversion.md)
- [模型转换 REST API](conversion-rest-api.md)
