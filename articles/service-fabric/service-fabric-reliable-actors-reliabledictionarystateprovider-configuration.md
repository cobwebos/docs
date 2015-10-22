<properties
   pageTitle="Service Fabric Reliable Actors“ReliableDictionaryActorStateProvider”配置概述"
   description="了解如何配置类型“ReliableDictionaryActorStateProvider”的 Service Fabric 有状态参与者"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="anuragg"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.date="08/26/2015"
   wacn.date=""/>

# 配置 Reliable Actors - ReliableDictionaryActorStateProvider
通过更改 Visual Studio 程序包根目录下的“Config”文件夹中生成的“settings.xml”文件，可以为指定的问题参与者修改 ReliableDictionaryActorStateProvider 的默认配置。

Service-Fabric 运行时查找“settings.xml”文件中的预定义的部分名称，并在创建基础运行时组件时使用配置值。

> [AZURE.NOTE]请**勿**删除/修改 Visual Studio 解决方案中生成的“settings.xml”文件中的以下配置的部分名称。

## 复制器安全配置
复制器安全配置用于保护在复制过程中使用的通信通道的安全。这意味着服务将无法看到对方的复制流量，从而确保高度可用的数据也处于安全状态。默认情况下，空的安全配置部分不启用复制安全。
### 部分名称
&lt;ActorName&gt;ServiceReplicatorSecurityConfig
### 配置名称
请参阅[复制安全](/documentation/articles/service-fabric-replication-security)

## 复制器配置
复制器配置用于配置通过在本地复制和保持状态，负责使参与者状态提供程序的状态高度可靠的复制器。默认配置由 Visual Studio 模板生成，并应已足够。本部分介绍了可用于调整复制器的其他配置。
### 部分名称
&lt;ActorName&gt;ServiceReplicatorConfig
### 配置名称

|Name|计价单位|默认值|备注|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0\.05|收到操作后，在向主要复制器送回确认之前，辅助复制器等待的时间段。为在此间隔内处理的操作发送的任何其他确认都作为响应发送。||
|ReplicatorEndpoint|不适用|不适用 - RequiredParameter|主要/辅助复制器用于与副本集中其他复制器通信的 IP 地址和端口。这应该引用服务清单中的 TCP 资源终结点。若要了解有关在服务清单中定义终结点资源的更多信息，请参阅[服务清单资源](/documentation/articles/service-fabric-service-manifest-resources)。 |
|RetryInterval|秒|5|如果复制器未收到操作的确认信息而进行重新传输之后的时间段。|
|MaxReplicationMessageSize|字节|50MB|可以在单个消息中传输的复制数据的最大大小。|
|MaxPrimaryReplicationQueueSize|操作的数量|1024|主要队列中的操作的最大数目。主要复制器接收来自所有辅助复制器的确认之后，释放操作。此值必须大于 64 和 2 的幂。|
|MaxSecondaryReplicationQueueSize|操作的数量|2048|辅助队列中的操作的最大数目。使其状态在保持期间高度可用之后，释放操作。此值必须大于 64 和 2 的幂。|
|MaxStreamSizeInMB|MB|1024|保留的日志文件空间量。大小（以字节为单位）必须比 MaxRecordSize（以字节为单位）大 16 倍。|
|MaxRecordSizeInKB|KB|1024|复制器可以在日志中写入的最大记录大小。此值必须是 4 的倍数，且大于 16。|
|OptimizeForLocalSSD|布尔值|false|为 true 时会配置日志，以便将状态信息直接写入副本的专用日志文件。当日志文件处于固态磁盘介质上时或是当 VM 磁盘 IO 率高度受限时，这可提供最佳性能。为 false 时，状态信息会首先暂存在共享日志文件中，随后移出到专用日志文件。|
|OptimizeLogForLowerDiskUsage|布尔值|false|为 true 时会配置日志，以便使用 NTFS 稀疏文件创建副本的专用日志文件。这会降低文件的实际磁盘空间使用率。为 false 时，会使用固定分配创建文件，这可提供最佳写入性能。|
|SharedLogId|guid|""|指定要用于标识与此副本一起使用的共享日志文件的唯一 guid。通常服务不应使用此设置，但是如果指定了 SharedLogId，则还必须指定 SharedLogPath。|
|SharedLogPath|完全限定的路径名|""|指定将在其中创建此副本共享日志文件的完全限定路径。通常服务不应使用此设置，但是如果指定了 SharedLogPath，则还必须指定 SharedLogId。|


## 示例配置文件

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## 备注
BatchAcknowledgementInterval 控制复制延迟。“0”值导致可能的最低延迟，但代价是牺牲吞吐量（因为必须发送和处理更多的确认消息，每个包含较少的确认）。BatchAcknowledgementInterval 的值越大，整体复制吞吐量就越高，但代价是导致更高的操作延迟。这直接转换为事务提交的延迟。

MaxStreamSizeInMB 的值确定复制器可以用于将状态信息存储在副本的专用日志文件中的磁盘空间量。因为存储的信息状态用于使另一个副本能够与主副本的状态匹配，所以通常最好使用较大的日志文件，因为这会减少另一个副本匹配主副本状态所花的时间。但是较大的日志文件可能会使用较多磁盘空间，从而减少可以在特定节点上托管的副本数。

OptimizeForLowerDiskUsage 设置允许“过度预配”日志文件空间，以便活动副本可以将更多状态信息存储在其日志文件中，而非活动副本使用较少的磁盘空间。虽然这使节点上承载的副本多于由于缺乏磁盘空间而发生的其他情况，但是通过将 OptimizeForLowerDiskUsage 设置为 false，状态信息可更快地写入日志文件。

OptimizeForLocalSSD 设置用于禁用首先将状态信息写入共享日志文件，然后再移出到专用日志文件。当专用日志文件的本地磁盘存储是固态介质时，通常应设置它（因为最大程度减少磁盘头移动不再是问题）。当 VM 在执行大量本地磁盘 IO，并且本地磁盘 IO 率在很大程度上受 VM 主机限制时，也可以使用它。

MaxRecordSizeInKB 定义可由复制器写入日志文件的记录的最大大小。在几乎所有情况下，默认 1024 KB 记录大小是最佳的，但是如果服务使更大数据项成为状态信息的一部分，则可能需要增加此值。使 MaxRecordSizeInKB 小于 1024 几乎没什么好处，因为记录仅使用较小记录所需的空间。它应该只在极少数情况下才需要更改。

SharedLogId 和 SharedLogPath 设置始终一起使用，使服务可以使用与节点的默认共享日志不同的共享日志。为获得最佳效率，应让尽可能多的服务指定相同共享日志。共享日志文件应置于仅用于共享日志文件的磁盘上，以便减少磁头运动争用。它应该只在极少数情况下才需要更改。
 

<!---HONumber=74-->