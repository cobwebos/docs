---
title: "导入和导出 Azure IoT 中心设备标识 | Microsoft Docs"
description: "如何使用 Azure IoT 服务 SDK 针对标识注册表执行批量操作，以导入和导出设备标识。 借助导入操作，可批量创建、更新和删除设备标识。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 6d878b00094f573adc440d2384c426506fea0a40
ms.lasthandoff: 04/06/2017


---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>批量管理 IoT 中心设备标识

每个 IoT 中心都有标识注册表，可用于在服务中创建每个设备资源，如包含未送达的云到设备消息的队列。 设备标识注册表还可控制对面向设备的终结点的访问。 本文介绍如何在标识注册表中批量导入和导出设备标识。

*作业*的上下文中发生导入和导出操作，可允许你对 IoT 中心执行批量服务操作。

**RegistryManager** 类包括使用**作业**框架的 **ExportDevicesAsync** 和 **ImportDevicesAsync** 方法。 这些方法可让你导出、导入和同步整个 IoT 中心标识注册表。

## <a name="what-are-jobs"></a>什么是作业？

当操作出现以下情况时，标识注册表操作使用“作业”系统：

* 相较标准运行时操作，其执行时间可能很长。
* 向用户返回大量数据。

针对这些情况（而不是针对在操作结果处等待或阻塞的单个 API 调用），操作将以异步方式创建该 IoT 中心的**作业**。 然后，操作立即返回 **JobProperties** 对象。

以下 C# 代码段演示如何创建导出作业：

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> 若要在 C# 代码中使用 **RegistryManager** 类，请将 **Microsoft.Azure.Devices** NuGet 包添加到项目。 **RegistryManager** 类位于 **Microsoft.Azure.Devices** 命名空间。


可使用 **RegistryManager** 类，查询使用返回的 **JobProperties** 元数据的**作业**的状态。

以下 C# 代码段演示如何每隔五秒轮询一次以查看作业是否已完成执行：

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>导出设备

使用 **ExportDevicesAsync** 方法，将整个 IoT 中心标识注册表导出到使用[共享访问签名](../storage/storage-security-guide.md#data-plane-security)的 [Azure 存储](../storage/index.md) Blob 容器。

此方法可让你在所控制的 Blob 容器中创建可靠的设备信息备份。

**ExportDevicesAsync** 方法需要两个参数：

* 一个 *字符串*，其中包含 Blob 容器的 URI。 此 URI 必须包含可授予容器写入权限的 SAS 令牌。 作业在此容器中创建用于存储序列化导出设备数据的块 Blob。 SAS 令牌必须包含这些权限：

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* 一个*布尔值*，该值指示你是否想要从导出数据中排除身份验证密钥。 如果为 **false**，则身份验证密钥包含在导出输出中。 否则，密钥导出为 **null**。

下面的 C# 代码段演示了如何启动在导出数据中包含设备身份验证密钥的导出作业，然后对完成情况进行轮询：

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

该作业将其输出以名为 **devices.txt** 的块 Blob 的形式存储在提供的 Blob 容器中。 输出数据包含 JSON 序列化设备数据，每行代表一个设备。

下面的示例演示了输出数据：

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

如果你需要在代码中访问此数据，可以使用 **ExportImportDevice** 类轻松反序列化此数据。 以下 C# 代码段演示如何读取前面导出到块 Blob 的设备信息：

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [!NOTE]
> 你还可以使用 **RegistryManager** 类的 **GetDevicesAsync** 方法获取设备的列表。 但是，此方法有一个硬性限制，那就是返回的设备对象数最多只能有 1000 个。 **GetDevicesAsync** 方法的预期用例用于帮助调试的开发方案，不建议用于生产工作负荷。

## <a name="import-devices"></a>导入设备

通过 **RegistryManager** 类中的 **ImportDevicesAsync** 方法，可以在 IoT 中心标识注册表中执行批量导入和同步操作。 如同 **ExportDevicesAsync** 方法，**ImportDevicesAsync** 方法也使用**作业**框架。

请小心使用 **ImportDevicesAsync** 方法，因为除了在标识注册表中预配新设备以外，它还可以更新和删除现有设备。

> [!WARNING]
> 导入操作不可撤消。 在对标识注册表进行批量更改之前，始终使用 **ExportDevicesAsync** 方法将现有数据备份到另一个 Blob 容器中。

**ImportDevicesAsync** 方法采用两个参数：

* 一个字符串，其中包含作为作业的输入使用的 [Azure 存储](../storage/index.md) Blob 容器的 URI。 此 URI 必须包含可授予容器读取权限的 SAS 令牌。 此容器必须包含名为 **devices.txt** 的 Blob，其中包含要导入标识注册表的序列化设备数据。 导入数据包含的设备信息必须采用 **ExportImportDevice** 作业在创建 **devices.txt** Blob 时使用的同一种 JSON 格式。 SAS 令牌必须包含这些权限：

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* 一个字符串，其中包含作为作业中的输出使用的 [Azure 存储](https://azure.microsoft.com/documentation/services/storage/) Blob 容器的 URI。 该作业在此容器中创建块 Blob，以存储来自已完成的导入**作业**的任何错误信息。 SAS 令牌必须包含这些权限：

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> 这两个参数可以指向同一 Blob 容器。 参数不同只会让你更容易掌控数据，因为输出容器需要其他权限。

以下 C# 代码段演示如何启动导入作业：

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## <a name="import-behavior"></a>导入行为

可以使用 **ImportDevicesAsync** 方法在标识注册表中执行以下批量操作：

* 批量注册新设备
* 批量删除现有设备
* 批量更改状态（启用或禁用设备）
* 批量分配新的设备身份验证密钥
* 批量自动重新生成设备身份验证密钥

可以在单一 **ImportDevicesAsync** 调用中执行前面的操作的任意组合。 例如，可以同时注册新设备并删除或更新现有设备。 当与 **ExportDevicesAsync** 方法一起使用时，可以将所有设备完全从一个 IoT 中心迁移到另一个中心。

在每个设备的导入序列化数据中使用可选 **importMode** 属性可控制每个设备的导入过程。 **ImportMode** 属性具有以下选项：

| importMode | 说明 |
| --- | --- |
| **createOrUpdate** |如果具有指定 **id** 的设备不存在，则新注册该设备。 <br/>如果该设备已存在，则使用提供的输入数据覆盖现有信息，与 **ETag** 值无关。 |
| **create** |如果具有指定 **id** 的设备不存在，则新注册该设备。 <br/>如果设备已存在，则将错误写入日志文件。 |
| **update** |如果具有指定 **id** 的设备已存在，则使用提供的输入数据覆盖现有信息，与 **ETag** 值无关。 <br/>如果设备不存在，则在日志文件中写入错误。 |
| **updateIfMatchETag** |如果具有指定 **id** 的设备已存在，则只有当 **ETag** 匹配时，才使用提供的输入数据覆盖现有信息。 <br/>如果设备不存在，则在日志文件中写入错误。 <br/>如果 **ETag** 不匹配，则将错误写入日志文件。 |
| **createOrUpdateIfMatchETag** |如果具有指定 **id** 的设备不存在，则新注册该设备。 <br/>如果设备已存在，则只有当 **ETag** 匹配时，才使用提供的输入数据覆盖现有信息。 <br/>如果 **ETag** 不匹配，则将错误写入日志文件。 |
| **delete** |如果具有指定 **id** 的设备已存在，则删除该设备，与 **ETag** 值无关。 <br/>如果设备不存在，则在日志文件中写入错误。 |
| **deleteIfMatchETag** |如果具有指定 **id** 的设备已存在，则只有当 **ETag** 匹配时才删除该设备。 如果设备不存在，则在日志文件中写入错误。 <br/>如果 ETag 不匹配，则将错误写入日志文件。 |

> [!NOTE]
> 如果序列化数据未显式定义设备的 **importMode** 标志，则该标志在导入操作过程中默认为 **createOrUpdate**。

## <a name="import-devices-example--bulk-device-provisioning"></a>导入设备示例 – 批量预配设备

下面的 C# 代码示例说明了如何生成多个执行以下操作的设备标识：

* 包括身份验证密钥。
* 将该设备信息写入块 blob。
* 将设备导入标识注册表中。

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>导入设备示例 – 批量删除

以下代码示例演示如何删除使用前面代码示例添加的设备：

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## <a name="get-the-container-sas-uri"></a>获取容器 SAS URI

下面的代码示例演示如何使用 Blob 容器的读取、写入和删除权限生成 [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md)：

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## <a name="next-steps"></a>后续步骤

在本文中，你已学习如何针对 IoT 中心内的标识注册表执行批量操作。 若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [IoT 中心度量值][lnk-metrics]
* [操作监视][lnk-monitor]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

