---
title: 为客户端应用程序配置传输层安全性 (TLS)
titleSuffix: Azure Storage
description: 配置客户端应用程序以使用最低版本的传输层安全性 (TLS) 与 Azure 存储进行通信。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89001801"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>为客户端应用程序配置传输层安全性 (TLS)

为了安全起见，Azure 存储帐户可能要求客户端使用最低版本的传输层安全性 (TLS) 来发送请求。 如果客户端使用的 TLS 版本低于所需的最低版本，则对 Azure 存储的调用将失败。 例如，如果存储帐户需要 TLS 1.2，则使用 TLS 1.1 的客户端发送的请求将失败。

本文介绍如何将客户端应用程序配置为使用特定版本的 TLS。 有关如何为 Azure 存储帐户配置最小所需 TLS 版本的信息，请参阅 [为存储帐户配置 (tls) 的传输层安全性最低版本](transport-layer-security-configure-minimum-version.md)。

## <a name="configure-the-client-tls-version"></a>配置客户端 TLS 版本

为了使客户端能够使用特定版本的 TLS 发送请求，操作系统必须支持该版本。

以下示例演示如何通过 PowerShell 或 .NET 将客户端的 TLS 版本设置为 1.2。 客户端使用的 .NET Framework 必须支持 TLS 1.2。 有关详细信息，请参阅 [TLS 1.2 支持](/dotnet/framework/network-programming/tls#support-for-tls-12)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下示例演示如何在 PowerShell 客户端中启用 TLS 1.2：

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

以下示例演示如何使用 Azure 存储客户端库版本 12 在 .NET 客户端中启用 TLS 1.2：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

以下示例演示如何使用 Azure 存储客户端库版本 11 在 .NET 客户端中启用 TLS 1.2：

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>验证客户端使用的 TLS 版本

若要验证客户端是否使用指定版本的 TLS 发送请求，可以使用 [Fiddler](https://www.telerik.com/fiddler) 或类似工具。 打开 Fiddler 以开始捕获客户端网络流量，然后执行上一节中的示例之一。 查看 Fiddler 跟踪，确认已使用正确版本的 TLS 发送请求，如下图所示。

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="显示 Fiddler 跟踪的屏幕截图，此跟踪指示根据请求使用的 TLS 版本":::

## <a name="next-steps"></a>后续步骤

- [为存储帐户配置 (TLS) 的传输层安全性的最低要求版本](transport-layer-security-configure-minimum-version.md)
- [适用于 Blob 存储的安全建议](../blobs/security-recommendations.md)
