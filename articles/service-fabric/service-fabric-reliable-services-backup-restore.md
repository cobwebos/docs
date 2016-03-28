<properties
   pageTitle="Reliable Service 备份和还原 | Microsoft Azure"
   description="Service Fabric Reliable Service 备份和还原的概念文档"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.date="01/25/2016"
   wacn.date=""/>

# 备份和还原 Reliable Services

Azure Service Fabric 是一个高可用性平台，用于复制多个节点中的状态以维护此高可用性。因此，即使群集中的一个节点出现故障，服务也将继续可用。尽管此平台提供的此内置冗余对某些情况来说可能已足够用了，但在特定情况下，仍需要服务备份数据（到外部存储）。

例如，在以下情况下，服务可能要备份数据：

* 永久失去运行指定分区的整个 Service Fabric 群集或所有节点。例如，当状态不是异地复制的，整个群集位于一个数据中心中，并且整个数据中心出现故障时可能发生此种情况。

* 状态被意外删除或受损而引起管理错误。例如，当具有足够权限的管理员错误地删除了服务时可能发生此情况。

* 服务中的 bug 导致数据损坏。例如，当某个服务代码升级程序开始将错误数据写入到可靠集合中时可能发生此情况。在此情况下，代码和数据可能都必须还原到先前的状态。

* 离线数据处理。对于商业智能来说使用离线处理的数据很方便，此处理是独立于生成数据的服务进行的。

备份/还原功能允许在 Reliable Services API 上构建的服务来创建和还原备份。由平台提供的备份 API 允许在不阻止读取或写入操作的情况下备份分区的状态。还原 API 允许从选定的备份还原某个分区的状态。


## 备份操作

服务创建者可完全控制何时进行备份，以及将备份存储在何处。

要启动备份，服务需要调用 **IReliableStateManager.BackupAsync**。仅可从主副本创建备份，并且需要授予这些备份写入状态。

如下所示，在名为 **backupCallback** 的函数 << BackupInfo  bool >> 中进行的 **BackupAsync** 的最简单的重载。

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** 提供有关备份的信息，包括运行时保存备份的文件夹的位置 (**BackupInfo.Directory**)。此回调函数期望将 **BackupInfo.Directory** 移到外部存储或其他位置。此函数也返回一个布尔值，此值表示是否已成功将备份文件夹移动到其目标位置。

以下代码演示如何使用 backupCallback 将备份上传到 Azure 存储空间：

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

在上面的示例中，**ExternalBackupStore** 是用于与 Azure Blob 存储进行交互的示例类，**UploadBackupFolderAsync** 是压缩此文件夹并将其放置在 Azure Blob 存储中的方法。

请注意：

- 在任何指定时间，每个副本只能有一个 **BackupAsync** 在运行。一次调用多个 **BackupAsync** 将引发 **FabricBackupInProgressException** 异常，以此来将运行中的备份限制为一个。

- 如果备份正在进行时副本发生故障转移，那么备份可能没有完成。因此，故障转移完成后，服务应负责根据需要通过调用 **BackupAsync** 来重新启动备份。

## 还原操作

一般而言，你可能需要执行还原操作的情况可以为以下类型之一：


- 服务分区丢失数据。例如，分区的三个副本中的两个（包括主副本）的磁盘数据已损坏或被擦除。新的主副本可能需要从备份中还原数据。

- 整个服务已丢失。例如，管理员删除了整个服务，因此需要还原此服务和数据。

- 服务复制了损坏的应用程序数据（例如，由于应用程序的 bug）。在此情况下，必须升级或恢复服务来消除损坏的原因，并还原未损坏的数据。

虽然有许多还原方法，但是我们仅针对上述情形提供一些使用 **RestoreAsync** 进行还原的示例。

## 分区数据丢失

在此情况下，运行时将自动检测数据丢失并调用 **OnDataLossAsync** API。

服务创建者需要执行下列操作来恢复：

- 重写 **CreateReliableStateManager** 以返回一个新的 **ReliableStateManager**，并提供发生数据丢失事件时调用的回调函数。

- 在包含服务的备份的外部位置中查找最新备份。

- 如果最新备份的状态晚于新的主副本的状态，则返回 false。这可以确保此新的主副本不会被较旧的数据覆盖。

- 下载最新的备份（如果备份已压缩，则将其解压缩到备份文件夹中）。

- 使用备份文件夹的路径调用 **IReliableStateManager.RestoreAsync**。

- 如果还原成功，则返回 true。

以下是实现 **OnDataLossAsync** 方法以及重写 **IReliableStateManager** 的一个示例。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] 默认情况下，RestorePolicy 设置为安全。这表示如果检测到备份文件夹中包含的状态早于或等于此副本中包含的状态，则 **RestoreAsync** API 将因 ArgumentException 而失败。**RestorePolicy.Force** 可以用来跳过此安全检查。

## 删除或丢失服务

如果删除了一个服务，则在还原数据之前必须首先重新创建此服务。请务必创建具有相同配置的服务，例如，相同的分区方案，以便可以无缝地还原数据。一旦服务启动后，需要在此服务的每个分区上调用用于还原数据的 API（上面的 **OnDataLossAsync**）。实现此操作的一种方法是在每个分区上使用 **FabricClient.ServiceManager.InvokeDataLossAsync**。

从这个角度来看，实现操作与上述情况相同。每个分区需要从外部存储中还原最新的相关备份。值得注意的一点是，分区 ID 现在可能已更改，因为运行时是动态创建分区 ID。因此，此服务需要还原相应的分区信息和服务名称来标识每个分区要还原的正确的最新备份。


## 复制损坏的应用程序数据

如果新部署的应用程序升级有一个 bug，则可能会导致数据损坏。例如，应用程序升级可能使用无效的区号更新可靠字典中的每个电话号码记录。在此情况下，将复制无效的电话号码，因为 Service Fabric 并不知道要存储的数据的性质。

在检测到这样一个导致数据损坏的严重 bug 之后首先要做的是在应用程序级别冻结服务，并且如果可以，请升级到没有此 bug 的应用程序代码版本。但是，即使修复了服务代码，数据仍可能是损坏的，因此可能需要还原数据。在此情况下，还原最新备份可能不足以解决问题，因为此最新备份可能已损坏。因此，你需要查找在数据被损坏之前创建的最新备份。

如果你不确定哪些备份已损坏，那么你可以部署一个新的 Service Fabric 群集，然后还原受影响分区的备份，正如上面的“删除或丢失服务”情况一样。针对每个分区，从最新备份到最旧备份开始还原。当你找到一个未被损坏的备份时，移动或删除此分区的比此备份更新的所有备份。对每个分区重复此过程。此时，对生产群集中的分区调用 **OnDataLossAsync** 时，在外部存储中找到的最新备份将是通过上述过程选取的备份。

现在，可使用“删除或丢失服务”部分中的步骤来将此服务备份的状态还原到错误代码损坏此状态之前的状态。

请注意：

- 还原时，要还原的备份状态可能早于数据丢失前分区的状态。因此，你只能将还原作为最后的办法来恢复尽可能多的数据。

- 表示备份文件夹路径和备份文件夹内的文件路径的字符串可以大于 255 个字符，具体取决于 FabricDataRoot 路径和应用程序类型名称的长度。这会导致某些 .NET 方法，如 **Directory.Move** 引发 **PathTooLongException** 异常。一种解决方法是直接调用 kernel32 API，如 **CopyFile**。


## 表象之下：有关备份和还原的更多详细信息

### 备份
可靠性状态管理器具有在不阻止任何读取或写入操作的情况下创建一致的备份的功能。要实现此功能，它利用了检查点和日志持久性机制。可靠性状态管理器在特定时间点采用模糊（轻型）检查点来缓解来自事务日志的压力，并缩短恢复时间。调用 **IReliableStateManager.BackupAsync** 时，可靠性状态管理器指示所有可靠对象将其最新的检查点文件复制到本地备份文件夹。然后，可靠性状态管理器将复制所有日志记录（从“开始指针”开始到最新的日志记录）到备份文件夹中。由于所有日志记录直至最新日志记录都包含在备份中，并且可靠性状态管理器保留了预写日志的功能，因此 可靠性状态管理器保证所有提交的事务（**CommitAsync** 已成功返回）都包含在备份中。

在调用 **BackupAsync** 之后提交的任何事务可能在备份中，也可能不在。一旦平台填充此本地备份文件夹（即由运行时完成本地备份），就将调用此服务的备份回调。此回调负责将此备份文件夹移至 Azure 存储空间等外部位置。

### 还原

可靠性状态管理器具有使用 **IReliableStateManager.RestoreAsync** API 从备份还原的功能。只能在 **OnDataLossAsync** 方法内部调用 **RestoreAsync** 方法。由 **OnDataLossAsync** 返回的布尔值表示服务是否从外部源还原其状态。如果 **OnDataLossAsync** 返回 true，则 Service Fabric 将使用此主副本重建所有其他副本。Service Fabric 可确保将第一个接收 **OnDataLossAsync** 的副本转换为主角色，但不会授予其读取状态或写入状态。这意味着对于 StatefulService 实现程序，不会调用 **RunAsync**，直到 **OnDataLossAsync** 成功完成。然后，将在新的主副本上调用 **OnDataLossAsync**。将一次调用一回 API，如此循环，直到服务成功完成此 API（返回 true 或 false），并完成相关的重新配置。


**RestoreAsync** 首先删除调用它的主副本中的所有现有状态。之后，可靠性状态管理器创建备份文件夹中存在的所有可靠对象。接下来，指示可靠对象从备份文件夹中的它们的检查点还原。最后，可靠性状态管理器从备份文件夹中的日志记录中恢复其自己的状态，并执行恢复。作为恢复过程的一部分，将对可靠对象重播从“起始点”开始的操作，该起始点已提交备份文件夹中的日志记录。此步骤可以确保恢复的状态是一致的。

<!---HONumber=Mooncake_0321_2016-->