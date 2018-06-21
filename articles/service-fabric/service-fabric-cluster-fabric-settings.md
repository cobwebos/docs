---
title: 更改 Azure Service Fabric 群集设置 | Microsoft Docs
description: 本文介绍可以自定义的结构设置和结构升级策略。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: aljo
ms.openlocfilehash: 118a6d10eeba691fd0886967f90156a0ab8d9fae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642642"
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>自定义 Service Fabric 群集设置和结构升级策略
本文档说明如何为 Service Fabric 群集自定义各种结构设置和结构升级策略。 可以通过 [Azure 门户](https://portal.azure.com)或使用 Azure 资源管理器模板完成自定义。

> [!NOTE]
> 并非所有设备在门户中皆为可用。 如果不能通过门户使用下面列出的设置，请使用 Azure 资源管理器模板对其进行自定义。
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>使用资源管理器模板自定义群集设置
下面的步骤演示如何将新设置 *MaxDiskQuotaInMB* 添加到“诊断”部分。

1. 转到 https://resources.azure.com
2. 通过展开“订阅” -> \<你的订阅> -> “resourceGroups” -> \<你的资源组> -> “提供程序” -> “Microsoft.ServiceFabric” -> “群集” -> \<你的群集名称>导航到你的订阅
3. 选择右上角的“读/写”。
4. 选择“编辑”，更新 `fabricSettings` JSON 元素并添加新元素：

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

以下为可自定义的 Fabric 设置列表（按分区排序）。

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|string，默认值为 L"None"|静态| ApplicationCertificateValidationPolicy：None：不会验证服务器证书；请求成功。 ServiceCertificateThumbprints：请参阅配置 ServiceCertificateThumbprints，获取反向代理可以信任的远程证书的指纹列表（以逗号分隔）。 ServiceCommonNameAndIssuer：请参阅配置 ServiceCommonNameAndIssuer，获取反向代理可以信任的远程证书的使用者名称和证书颁发者指纹。 |
|BodyChunkSize |Uint，默认值为 16384 |动态| 提供用于读取正文的区块大小（以字节为单位）。 |
|CrlCheckingFlag|uint，默认值为 0x40000000 |动态| 应用程序/服务证书链验证的标记；例如 CRL 检查 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY 设置为 0 会禁用 CRL 检查，支持值的完整列表由 CertGetCertificateChain 的 dwFlags 记录：http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |以秒为单位的时间。 默认值为 120 |动态|指定以秒为单位的时间范围。  提供用于 http 应用网关中正在处理的 http 请求的默认请求超时时间。 |
|ForwardClientCertificate|bool，默认值为 FALSE|动态| |
|GatewayAuthCredentialType |string，默认值为“None” |静态| 指示在 http 应用网关终结点处使用的安全凭据的类型，有效值为 "None/X509。 |
|GatewayX509CertificateFindType |string，默认值为“FindByThumbprint” |动态| 指示如何在由 GatewayX509CertificateStoreName 支持的值（FindByThumbprint、FindBySubjectName）指定的存储中搜索证书。 |
|GatewayX509CertificateFindValue | string，默认值为“” |动态| 用于查找 http 应用网关证书的搜索筛选器值。 此证书在 https 终结点上配置，并且如果服务需要，还可用于验证应用的标识。 首先查找 FindValue；如果其不存在，再查找 FindValueSecondary。 |
|GatewayX509CertificateFindValueSecondary | string，默认值为“” |动态|用于查找 http 应用网关证书的搜索筛选器值。 此证书在 https 终结点上配置，并且如果服务需要，还可用于验证应用的标识。 首先查找 FindValue；如果其不存在，再查找 FindValueSecondary。|
|GatewayX509CertificateStoreName |string，默认值为“My” |动态| 包含 http 应用网关证书的 X.509 证书存储的名称。 |
|HttpRequestConnectTimeout|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(5)|动态|指定以秒为单位的时间范围。  提供用于正在从 http 应用网关发送的 http 请求的连接超时时间。  |
|IgnoreCrlOfflineError|bool，默认值为 TRUE|动态|是否要忽略应用程序/服务证书验证的 CRL 脱机错误。 |
|IsEnabled |Bool，默认值为 false |静态| 启用/禁用 HttpApplicationGateway。 默认情况下，禁用 HttpApplicationGateway，需要设置此配置以启用 HttpApplicationGateway。 |
|NumberOfParallelOperations | Uint，默认值为 5000 |静态|要发布到 http 服务器队列的读取数。 此配置控制 HttpGateway 可以满足的并发请求数。 |
|RemoveServiceResponseHeaders|string，默认值为 L"Date; Server"|静态|从服务响应中删除的响应标头列表（以分号/逗号分隔），这些标头将子啊转发到客户端之前删除。 如果此值设置为空字符串，则会按原样传递服务返回的所有标头。 例如  不会覆盖日期和服务器 |
|ResolveServiceBackoffInterval |以秒为单位的时间，默认值为 5 |动态|指定以秒为单位的时间范围。  提供重试失败的解析服务操作之前的默认回退时间间隔。 |
|SecureOnlyMode|bool，默认值为 FALSE|动态| SecureOnlyMode：true：反向代理只会转发到发布安全终结点的服务。 false：反向代理可以将请求转发到安全/不安全的终结点。  |
|ServiceCertificateThumbprints|string，默认值为 L""|动态| |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap，默认值为 None|动态|  |

## <a name="backuprestoreservice"></a>BackupRestoreService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int，默认值为 0|静态|BackupRestoreService 的 MinReplicaSetSize |
|PlacementConstraints|wstring，默认值为 L""|静态| BackupRestore 服务的 PlacementConstraints |
|SecretEncryptionCertThumbprint|wstring，默认值为 L""|动态|机密加密 X509 证书指纹 |
|SecretEncryptionCertX509StoreName|wstring，默认值为 L"My"|  动态|    这表示用来加密和解密 X.509 证书存储的凭据名称的证书，备份还原服务用此加密解密存储凭据 |
|TargetReplicaSetSize|int，默认值为 0|静态| The TargetReplicaSetSize for BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|EnableDefaultServicesUpgrade | Bool，默认值为 false |动态|在应用程序升级期间启用升级默认服务。 升级后，会覆盖默认服务说明。 |
|FabricUpgradeHealthCheckInterval |以秒为单位的时间，默认值为 60 |动态|受监视结构升级期间的运行状况检查频率 |
|FabricUpgradeStatusPollInterval |以秒为单位的时间，默认值为 60 |动态|轮询结构升级状态的频率。 此值确定任何 GetFabricUpgradeProgress 调用的更新速率 |
|ImageBuilderTimeoutBuffer |以秒为单位的时间，默认值为 3 |动态|指定以秒为单位的时间范围。 所允许的映像生成器特定超时错误用于返回到客户端的时间量。 如果此缓冲区太小，客户端会先于服务器超时，并收到一个泛型超时错误。 |
|InfrastructureTaskHealthCheckRetryTimeout | 以秒为单位的时间，默认值为 60 |动态|指定以秒为单位的时间范围。 在对基础结构任务进行后处理时，重试失败的运行状况检查所花费的时间。 观察通过的运行状况检查将重置此计时器。 |
|InfrastructureTaskHealthCheckStableDuration | 以秒为单位的时间，默认值为 0|动态| 指定以秒为单位的时间范围。 在成功完成基础结构任务的后处理之前，用于观察连续通过的运行状况检查的时间量。 观察失败的运行状况检查将重置此计时器。 |
|InfrastructureTaskHealthCheckWaitDuration |以秒为单位的时间，默认值为 0|动态| 指定以秒为单位的时间范围。 对基础结构任务的后处理完成后，开始运行状况检查之前的等待时间量。 |
|InfrastructureTaskProcessingInterval | 以秒为单位的时间，默认值为 10 |动态|指定以秒为单位的时间范围。 基础结构任务处理状态机使用的处理时间间隔。 |
|MaxCommunicationTimeout |以秒为单位的时间，默认值为 600 |动态|指定以秒为单位的时间范围。 ClusterManager 与其他系统服务（即命名服务、故障转移管理器等）之间的内部通信的最大超时时间。 此超时时间应小于全局 MaxOperationTimeout（因为对于每个客户端操作，系统组件之间可能有多个通信）。 |
|MaxDataMigrationTimeout |以秒为单位的时间，默认值为 600 |动态|指定以秒为单位的时间范围。 发生 Fabric 升级后，数据迁移恢复操作的最大超时时间。 |
|MaxOperationRetryDelay |以秒为单位的时间，默认值为 5|动态| 指定以秒为单位的时间范围。 遇到故障时，内部重试的最大延迟时间。 |
|MaxOperationTimeout |以秒为单位的时间，默认值为 MaxValue |动态| 指定以秒为单位的时间范围。 用于内部处理 ClusterManager 上的操作的最大全局超时时间。 |
|MaxTimeoutRetryBuffer | 以秒为单位的时间，默认值为 600 |动态|指定以秒为单位的时间范围。 因超时导致内部重试时，最大操作超时时间是 <Original Time out> + <MaxTimeoutRetryBuffer>。 以 MinOperationTimeout 为增量添加额外超时时间。 |
|MinOperationTimeout | 以秒为单位的时间，默认值为 60 |动态|指定以秒为单位的时间范围。 用于内部处理 ClusterManager 上的操作的最小全局超时时间。 |
|MinReplicaSetSize |Int，默认值为 3 |不允许|ClusterManager 的 MinReplicaSetSize。 |
|PlacementConstraints | string，默认值为“” |不允许|ClusterManager 的 PlacementConstraints。 |
|QuorumLossWaitDuration |以秒为单位的时间，默认值为 MaxValue |不允许| 指定以秒为单位的时间范围。 ClusterManager 的 QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration |以秒为单位的时间，默认值为 (60.0 * 30)|不允许|指定以秒为单位的时间范围。 ClusterManager 的 ReplicaRestartWaitDuration。 |
|ReplicaSetCheckTimeoutRollbackOverride |以秒为单位的时间，默认值为 1200 |动态| 指定以秒为单位的时间范围。 如果 ReplicaSetCheckTimeout 设置为 DWORD 的最大值，则出于回滚目的，将用此配置的值对其进行重写。 永远不会重写用于前滚的值。 |
|SkipRollbackUpdateDefaultService | Bool，默认值为 false |动态|CM 会在应用程序升级回滚过程中跳过恢复更新的默认服务。 |
|StandByReplicaKeepDuration | 以秒为单位的时间，默认值为 (3600.0 * 2)|不允许|指定以秒为单位的时间范围。 ClusterManager 的 StandByReplicaKeepDuration。 |
|TargetReplicaSetSize |Int，默认值为 7 |不允许|ClusterManager 的 TargetReplicaSetSize。 |
|UpgradeHealthCheckInterval |以秒为单位的时间，默认值为 60 |动态|受监视应用程序升级期间的运行状况检查频率 |
|UpgradeStatusPollInterval |以秒为单位的时间，默认值为 60 |动态|轮询应用程序升级状态的频率。 此值确定任何 GetApplicationUpgradeProgress 调用的更新速率 |

## <a name="common"></a>常见
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PerfMonitorInterval |以秒为单位的时间，默认值为 1 |动态|指定以秒为单位的时间范围。 性能监视时间间隔。 设置为 0 或负值，将禁用监视。 |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap，默认值为 None|动态|指定清空节点时遵循的策略碎片整理。 对于某个给定指标，0 表示 SF 应尝试在 UD 和 FD 间对节点进行均匀的碎片整理，1 仅指表示必须对节点进行碎片整理 |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap，默认值为 None|动态|确定应用于碎片整理而不用于负载均衡的指标组。 |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap，默认值为 None|动态|通过在范围 [0.0 - 1.0) 中指定百分比或将空节点数指定为 >= 1.0 的数，确定需考虑进行群集碎片整理的可用节点数 |

## <a name="diagnostics"></a>诊断
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool，默认值为 true | 动态 |代表应用程序访问诊断存储时是否需要模拟。 |
|AppEtwTraceDeletionAgeInDays |Int，默认值为 3 | 动态 |在多少天后删除包含应用程序 ETW 跟踪的旧 ETL 文件。 |
|ApplicationLogsFormatVersion |Int，默认值为 0 | 动态 |用于应用程序日志格式的版本。 支持的值是 0 和 1. 版本 1 比版本 0 包含更多 ETW 事件记录的字段。 |
|ClusterId |String | 动态 |群集的唯一 ID。 于群集创建时生成。 |
|ConsumerInstances |String | 动态 |DCA 使用者实例列表。 |
|DiskFullSafetySpaceInMB |Int，默认值为 1024 | 动态 |要避免被 DCA 使用的剩余磁盘空间（以 MB 为单位）。 |
|EnableCircularTraceSession |Bool，默认值为 false | 静态 |标志指示是否应使用循环跟踪会话。 |
|EnableTelemetry |Bool，默认值为 true | 动态 |这会启用或禁用遥测。 |
|MaxDiskQuotaInMB |Int，默认值为 65536 | 动态 |Windows Fabric 日志文件的磁盘配额（以 MB 为单位）。 |
|ProducerInstances |String | 动态 |DCA 生成者实例列表。 |

## <a name="dnsservice"></a>DnsService
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|IsEnabled|bool，默认值为 FALSE|静态| |
|InstanceCount|int，默认值为 -1|静态|  |

## <a name="fabricclient"></a>FabricClient
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |以秒为单位的时间，默认值为 2 |动态|指定以秒为单位的时间范围。 每次客户端尝试打开网关连接时的连接超时间隔。|
|HealthOperationTimeout |以秒为单位的时间，默认值为 120 |动态|指定以秒为单位的时间范围。 报告消息发送至运行状况管理器的超时时间。 |
|HealthReportRetrySendInterval |以秒为单位的时间，默认值为 30 |动态|指定以秒为单位的时间范围。 报告组件将累积的运行状况报告重新发送至运行状况管理器的时间间隔。 |
|HealthReportSendInterval |以秒为单位的时间，默认值为 30 |动态|指定以秒为单位的时间范围。 报告组件将累积的运行状况报告发送至运行状况管理器的时间间隔。 |
|KeepAliveIntervalInSeconds |Int，默认值为 20 |静态|FabricClient 传输向网关发送保持连接消息的时间间隔。 值为 0，表示禁用 keepAlive。 必须是正值。 |
|MaxFileSenderThreads |Uint，默认值为 10 |静态|并行传输的最大文件数。 |
|NodeAddresses |string，默认值为“” |静态|不同节点上的地址（连接字符串）的集合，可用于与命名服务通信。 最初，客户端随机选择一个地址进行连接。 如果提供了多个连接字符串且因通信或超时错误导致连接失败，客户端按顺序切换为使用下一个地址。 请参阅命名服务地址重试部分，了解有关重试语义的详细信息。 |
|PartitionLocationCacheLimit |Int，默认值为 100000 |静态|为服务解析所缓存的分区数（设置为 0，表示无限制）。 |
|RetryBackoffInterval |以秒为单位的时间，默认值为 3 |动态|指定以秒为单位的时间范围。 重试操作之前的回退时间间隔。 |
|ServiceChangePollInterval |以秒为单位的时间，默认值为 120 |动态|指定以秒为单位的时间范围。 服务的连续轮询之间的间隔从客户端更改为用于注册服务更改通知回调的网关。 |

## <a name="fabrichost"></a>FabricHost
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int，默认值为 10 |动态|这是系统在放弃前重试失败的激活的最大计数。 |
|ActivationMaxRetryInterval |以秒为单位的时间，默认值为 300 |动态|指定以秒为单位的时间范围。 激活的最大重试时间间隔。 每次连续失败后，重试时间间隔的计算结果为 Min（ActivationMaxRetryInterval；连续失败计数 * ActivationRetryBackoffInterval）（即取括号中的最小值）。 |
|ActivationRetryBackoffInterval |以秒为单位的时间，默认值为 5 |动态|指定以秒为单位的时间范围。 每次激活失败的回退时间间隔；每次连续激活失败后，系统将重试激活最多 MaxActivationFailureCount 次。 每次尝试的重试间隔是连续激活失败与激活退让间隔的积。 |
|EnableRestartManagement |Bool，默认值为 false |动态|这会启用服务器重启。 |
|EnableServiceFabricAutomaticUpdates |Bool，默认值为 false |动态|这会通过 Windows 更新启用 Fabric 自动更新。 |
|EnableServiceFabricBaseUpgrade |Bool，默认值为 false |动态|这会启用服务器的基本更新。 |
|StartTimeout |以秒为单位的时间，默认值为 300 |动态|指定以秒为单位的时间范围。 fabricactivationmanager 启动的超时时间。 |
|StopTimeout |以秒为单位的时间，默认值为 300 |动态|指定以秒为单位的时间范围。 托管服务激活、停用和升级的超时时间。 |

## <a name="fabricnode"></a>FabricNode
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |string，默认值为“FindByThumbprint” |动态|指示如何在由 ClientAuthX509StoreName 支持的值（FindByThumbprint、FindBySubjectName）指定的存储中搜索证书。 |
|ClientAuthX509FindValue |string，默认值为“” | 动态|用于查找默认管理员角色 FabricClient 的证书的搜索筛选器值。 |
|ClientAuthX509FindValueSecondary |string，默认值为“” |动态|用于查找默认管理员角色 FabricClient 的证书的搜索筛选器值。 |
|ClientAuthX509StoreName |string，默认值为“My” |动态|X.509 证书存储的名称，包含默认管理员角色 FabricClient 的证书。 |
|ClusterX509FindType |string，默认值为“FindByThumbprint” |动态|指示如何在由 ClusterX509StoreName 支持的值（“FindByThumbprint”和“FindBySubjectName”）指定的存储中搜索群集证书。使用“FindBySubjectName”时，如果有多个匹配项，使用到期时间最远的那一个。 |
|ClusterX509FindValue |string，默认值为“” |动态|用于查找群集证书的搜索筛选器值。 |
|ClusterX509FindValueSecondary |string，默认值为“” |动态|用于查找群集证书的搜索筛选器值。 |
|ClusterX509StoreName |string，默认值为“My” |动态|X.509 证书存储的名称，该存储包含用于保护群集内部通信的群集证书。 |
|EndApplicationPortRange |Int，默认值为 0 |静态|由宿主子系统管理的应用程序端口的结束位置（不含）。 当托管中的 EndpointFilteringEnabled 为 true 时为必需。 |
|ServerAuthX509FindType |string，默认值为“FindByThumbprint” |动态|指示如何在由 ServerAuthX509StoreName 支持的值（FindByThumbprint、FindBySubjectName）指定的存储中搜索服务器证书。 |
|ServerAuthX509FindValue |string，默认值为“” |动态|用于查找服务器证书的搜索筛选器值。 |
|ServerAuthX509FindValueSecondary |string，默认值为“” |动态|用于查找服务器证书的搜索筛选器值。 |
|ServerAuthX509StoreName |string，默认值为“My” |动态|X.509 证书存储的名称，包含用于准入服务的服务器证书。 |
|StartApplicationPortRange |Int，默认值为 0 |静态|由宿主子系统管理的应用程序端口的开始位置。 当托管中的 EndpointFilteringEnabled 为 true 时为必需。 |
|StateTraceInterval |以秒为单位的时间，默认值为 300 |静态|指定以秒为单位的时间范围。 FM/FMM 上每个节点及以上节点的节点状态跟踪的时间间隔。 |
|UserRoleClientX509FindType |string，默认值为“FindByThumbprint” |动态|指示如何在由 UserRoleClientX509StoreName 支持的值（FindByThumbprint、FindBySubjectName）指定的存储中搜索证书。 |
|UserRoleClientX509FindValue |string，默认值为“” |动态|用于查找默认用户角色 FabricClient 的证书的搜索筛选器值。 |
|UserRoleClientX509FindValueSecondary |string，默认值为“” |动态|用于查找默认用户角色 FabricClient 的证书的搜索筛选器值。 |
|UserRoleClientX509StoreName |string，默认值为“My” |动态|X.509 证书存储的名称，包含默认用户角色 FabricClient 的证书。 |

## <a name="failovermanager"></a>FailoverManager
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|BuildReplicaTimeLimit|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(3600)|动态|指定以秒为单位的时间范围。 生成监控状态副本的时间限制。超出该时间范围后会启动警告运行状况报告 |
|ClusterPauseThreshold|int，默认值为 1|动态|如果系统中的节点数低于此值，则会停止放置、负载均衡和故障转移。 |
|CreateInstanceTimeLimit|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(300)|动态|指定以秒为单位的时间范围。 创建无状态实例的时间限制。超出该时间范围后会启动警告运行状况报告 |
|ExpectedClusterSize|int，默认值为 1|动态|最初启动群集时；FM 会等待该数值个数的节点自行报告，然后开始放置其他服务（包括命名等系统服务）。 增加此值会增加群集启动所需的时间；但可防止早期节点重载，而且随着更多的节点进入联机状态，需要执行额外的移动操作。 此值通常应设置为初始群集大小的一小部分。 |
|ExpectedNodeDeactivationDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(60.0 * 30)|动态|指定以秒为单位的时间范围。 这是节点完成停用的预期工期。 |
|ExpectedNodeFabricUpgradeDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(60.0 * 30)|动态|指定以秒为单位的时间范围。 这是 Windows Fabric 升级过程中升级节点的预期持续时间。 |
|ExpectedReplicaUpgradeDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(60.0 * 30)|动态|指定以秒为单位的时间范围。 这是在应用程序升级过程中节点上要升级的所有副本的预期持续时间。 |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool，默认值为 TRUE|动态|如果设置为 true，则允许在升级过程中移动目标副本集大小为 1 的副本。 |
|MinReplicaSetSize|int，默认值为 3|不允许|这是 FM 的最小副本集大小。 如果活动 FM 副本数低于此值，FM 会拒绝对群集进行更改，直到至少恢复最小数目的副本 |
|PlacementConstraints|string，默认值为 L""|不允许|故障转移管理器副本的任何放置约束 |
|PlacementTimeLimit|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(600)|动态|指定以秒为单位的时间范围。 达到目标副本计数的时间限制。超出该时间范围后会启动警告运行状况报告 |
|QuorumLossWaitDuration |以秒为单位的时间，默认值为 MaxValue |动态|指定以秒为单位的时间范围。 这是允许分区处于仲裁丢失状态的最长持续时间。 如果分区在此持续时间后仍然处于仲裁丢失状态，则通过将不可用副本视为已丢失，使分区从仲裁丢失状态中恢复。 请注意，这可能会导致数据丢失。 |
|ReconfigurationTimeLimit|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(300)|动态|指定以秒为单位的时间范围。 重新配置的时间限制。超出该时间范围后会启动警告运行状况报告 |
|ReplicaRestartWaitDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(60.0 * 30)|不允许|指定以秒为单位的时间范围。 这是 FMService 的 ReplicaRestartWaitDuration |
|StandByReplicaKeepDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|不允许|指定以秒为单位的时间范围。 这是 FMService 的 StandByReplicaKeepDuration |
|TargetReplicaSetSize|int，默认值为 7|不允许|这是 Windows Fabric 将维护的 FM 副本目标数量。 数字越大，FM 数据的可靠性越高；但会稍微降低其性能。 |
|UserMaxStandByReplicaCount |Int，默认值为 1 |动态|系统为用户服务保留的默认最大备用副本数。 |
|UserReplicaRestartWaitDuration |以秒为单位的时间，默认值为 60.0 * 30 |动态|指定以秒为单位的时间范围。 当持久化副本不可用时，Windows Fabric 在创建新的替换副本（需要状态的副本）前，会等待该副本恢复正常，等待时间即为此持续时间。 |
|UserStandByReplicaKeepDuration |以秒为单位的时间，默认值为 3600.0 * 24 * 7 |动态|指定以秒为单位的时间范围。 持久化副本从不可用状态恢复时，可能已被替换为另一副本。 此定时器决定 FM 在放弃备用副本之前保留其多长时间。 |

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int，默认值为 604800 |静态| 这是处于终态的操作的大致保留时长。 这也取决于 StoredActionCleanupIntervalInSeconds，因为仅在此间隔时间内执行清理工作。 604800 秒是 7 天。 |
|DataLossCheckPollIntervalInSeconds|int，默认值为 5|静态|这是系统在等待发生数据丢失时执行的检查之间的时间间隔。 每次内部迭代检查数据丢失数的次数是 DataLossCheckWaitDurationInSeconds/this。 |
|DataLossCheckWaitDurationInSeconds|int，默认值为 25|静态|系统等待发生数据丢失的总时间量；以秒为单位。 这在调用 StartPartitionDataLossAsync() api 时在内部使用。 |
|MinReplicaSetSize |Int，默认值为 0 |静态|FaultAnalysisService 的 MinReplicaSetSize。 |
|PlacementConstraints | string，默认值为“”|静态| FaultAnalysisService 的 PlacementConstraints。 |
|QuorumLossWaitDuration | 以秒为单位的时间，默认值为 MaxValue |静态|指定以秒为单位的时间范围。 FaultAnalysisService 的 QuorumLossWaitDuration。 |
|ReplicaDropWaitDurationInSeconds|int，默认值为 600|静态|调用数据丢失 api 时使用此参数。 该参数控制内部调用删除副本后系统等待副本删除的时间。 |
|ReplicaRestartWaitDuration |以秒为单位的时间，默认值为 60 分钟|静态|指定以秒为单位的时间范围。 FaultAnalysisService 的 ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration| 以秒为单位的时间，默认值为 (60*24*7) 分钟 |静态|指定以秒为单位的时间范围。 FaultAnalysisService 的 StandByReplicaKeepDuration。 |
|StoredActionCleanupIntervalInSeconds | Int，默认值为 3600 |静态|这是清理存储的频率。 仅会删除处于终态和至少在 CompletedActionKeepDurationInSeconds 以前完成的操作。 |
|StoredChaosEventCleanupIntervalInSeconds | Int，默认值为 3600 |静态|这是审核存储（以进行清理）的频率，如果事件数量超过 30000，则开始执行清理。 |
|TargetReplicaSetSize |Int，默认值为 0 |静态|NOT_PLATFORM_UNIX_START，FaultAnalysisService 的 TargetReplicaSetSize。 |

## <a name="federation"></a>联合
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|LeaseDuration |以秒为单位的时间，默认值为 30 |动态|节点与其邻居之间的租约持续时间。 |
|LeaseDurationAcrossFaultDomain |以秒为单位的时间，默认值为 30 |动态|所有容错域中的节点与其邻居之间的租约持续时间。 |

## <a name="filestoreservice"></a>FileStoreService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AnonymousAccessEnabled | Bool，默认值为 true |静态|启用/禁用对 FileStoreService 共享的匿名访问。 |
|CommonName1Ntlmx509CommonName|string，默认值为 L""|静态| 使用 NTLM 身份验证时，用于在 CommonName1NtlmPasswordSecret 上生成 HMAC 的 X509 证书的公用名称 |
|CommonName1Ntlmx509StoreLocation|string，默认值为 L"LocalMachine"|静态|使用 NTLM 身份验证时，用于在 CommonName1NtlmPasswordSecret 上生成 HMAC 的 X509 证书的存储位置 |
|CommonName1Ntlmx509StoreName|string，默认值为 L"MY"| 静态|使用 NTLM 身份验证时，用于在 CommonName1NtlmPasswordSecret 上生成 HMAC 的 X509 证书的存储名称 |
|CommonName2Ntlmx509CommonName|string，默认值为 L""|静态|使用 NTLM 身份验证时，用于在 CommonName2NtlmPasswordSecret 上生成 HMAC 的 X509 证书的公用名称 |
|CommonName2Ntlmx509StoreLocation|string，默认值为 L"LocalMachine"| 静态|使用 NTLM 身份验证时，用于在 CommonName2NtlmPasswordSecret 上生成 HMAC 的 X509 证书的存储位置 |
|CommonName2Ntlmx509StoreName|string，默认值为 L"MY"|静态| 使用 NTLM 身份验证时，用于在 CommonName2NtlmPasswordSecret 上生成 HMAC 的 X509 证书的存储名称 |
|CommonNameNtlmPasswordSecret|SecureString，默认值为 Common::SecureString(L"")| 静态|密码，用于在使用 NTLM 身份验证时用作种子以生成相同密码 |
|GenerateV1CommonNameAccount| bool，默认值为 TRUE|静态|指定是否要使用用户名 V1 生成算法生成帐户。 从 Service Fabric 6.1 版开始，始终创建具有 v2 生成的帐户。 从/到不支持 V2 生成的版本升级需要 V1 帐户（6.1 版以前）。|
|MaxCopyOperationThreads | Uint，默认值为 0 |动态| 辅助节点可从主节点复制的最大并行文件数。 ‘0’== 内核数。 |
|MaxFileOperationThreads | Uint，默认值为 100 |静态| 可在主节点中执行 FileOperations（复制/移动）操作的最大并行线程数。 ‘0’== 内核数。 |
|MaxRequestProcessingThreads | Uint，默认值为 200 |静态|可在主节点中处理请求的最大并行线程数。 ‘0’== 内核数。 |
|MaxSecondaryFileCopyFailureThreshold | Uint，默认值为 25|动态|放弃前，辅助节点上的最大文件副本重试次数。 |
|MaxStoreOperations | Uint，默认值为 4096 |静态|主节点上可允许的最大并行存储事务操作数。 ‘0’== 内核数。 |
|NamingOperationTimeout |以秒为单位的时间，默认值为 60 |动态|指定以秒为单位的时间范围。 执行命名操作时的超时时间。 |
|PrimaryAccountNTLMPasswordSecret | SecureString，默认值为空 |静态| 密码，用于在使用 NTLM 身份验证时用作种子以生成相同密码。 |
|PrimaryAccountNTLMX509StoreLocation | string，默认值为“LocalMachine”|静态| 使用 NTLM 身份验证时，用于在 PrimaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的存储位置。 |
|PrimaryAccountNTLMX509StoreName | string，默认值为“MY”|静态| 使用 NTLM 身份验证时，用于在 PrimaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的存储名称。 |
|PrimaryAccountNTLMX509Thumbprint | string，默认值为“”|静态|使用 NTLM 身份验证时，用于在 PrimaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的指纹。 |
|PrimaryAccountType | string，默认值为“” |静态|FileStoreService 共享的 ACL 主体的主帐户类型。 |
|PrimaryAccountUserName | string，默认值为“” |静态|FileStoreService 共享的 ACL 主体的主帐户用户名。 |
|PrimaryAccountUserPassword | SecureString，默认值为空 |静态|FileStoreService 共享的 ACL 主体的主帐户密码。 |
|QueryOperationTimeout | 以秒为单位的时间，默认值为 60 |动态|指定以秒为单位的时间范围。 执行查询操作的超时时间。 |
|SecondaryAccountNTLMPasswordSecret | SecureString，默认值为空 |静态| 密码，用于在使用 NTLM 身份验证时用作种子以生成相同密码。 |
|SecondaryAccountNTLMX509StoreLocation | string，默认值为“LocalMachine” |静态|使用 NTLM 身份验证时，用于在 SecondaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的存储位置。 |
|SecondaryAccountNTLMX509StoreName | string，默认值为“MY” |静态|使用 NTLM 身份验证时，用于在 SecondaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的存储名称。 |
|SecondaryAccountNTLMX509Thumbprint | string，默认值为“”| 静态|使用 NTLM 身份验证时，用于在 SecondaryAccountNTLMPasswordSecret 上生成 HMAC 的 X509 证书的指纹。 |
|SecondaryAccountType | string，默认值为“”|静态| FileStoreService 共享的 ACL 主体的辅助帐户类型。 |
|SecondaryAccountUserName | string，默认值为“”| 静态|FileStoreService 共享的 ACL 主体的辅助帐户用户名。 |
|SecondaryAccountUserPassword | SecureString，默认值为空 |静态|FileStoreService 共享的 ACL 主体的辅助帐户密码。 |

## <a name="healthmanager"></a>HealthManager
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Bool，默认值为 false |静态|群集运行状况评估策略：启用按应用程序类型的运行状况评估。 |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool，默认值为 false |静态|群集运行状况评估策略：警告视为错误。 |
|MaxPercentUnhealthyApplications | Int，默认值为 0 |静态|群集运行状况评估策略：为确保群集运行正常，可允许的最高运行不正常应用程序百分比。 |
|MaxPercentUnhealthyNodes | Int，默认值为 0 |静态|群集运行状况评估策略：为确保群集运行正常，可允许的最高运行不正常节点百分比。 |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int，默认值为 10|静态|群集升级运行状况评估策略：为确保群集运行正常，可允许的最高增量不正常节点百分比 |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int，默认值为 15|静态|群集升级运行状况评估策略：为确保群集运行正常，升级域中可允许的最高增量不正常节点百分比 |

## <a name="hosting"></a>Hosting
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |整数，默认值为 10 |动态|系统在放弃之前重试失败激活的次数 |
|ActivationMaxRetryInterval |以秒为单位的时间，默认值为 300 |动态|在每次连续激活失败时，系统将重试激活最多 ActivationMaxFailureCount 次。 ActivationMaxRetryInterval 指定每次激活失败之后、重试之前等待的时间间隔 |
|ActivationRetryBackoffInterval |以秒为单位的时间，默认值为 5 |动态|每次激活失败的退让间隔；在每次连续激活失败后，系统将重试激活最多 MaxActivationFailureCount 次。 每次尝试的重试间隔是连续激活失败与激活退让间隔的积。 |
|ActivationTimeout| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(180)|动态| 指定以秒为单位的时间范围。 应用程序激活、停用和升级的超时时间。 |
|ApplicationHostCloseTimeout| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态| 指定以秒为单位的时间范围。 在自助激活进程中检测到 Fabric 退出时；FabricRuntime 会关闭用户主机 (applicationhost) 进程中的所有副本。 这是关闭操作的超时时间。 |
|ApplicationUpgradeTimeout| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(360)|动态| 指定以秒为单位的时间范围。 应用程序升级的超时时间。 如果超时时间小于 "ActivationTimeout"，则部署器失败。 |
|ContainerServiceArguments|wstring，默认为 L"-H localhost:2375 -H npipe://"|静态|Service Fabric (SF) 管理 docker 守护程序（在 Win10 等 windows 客户端计算机上除外）。 此配置允许用户指定启动时应传递到 Docker 守护程序的自定义参数。 指定自定义参数时，Service Fabric 不会将“--pidfile”参数以外的任何其他参数传递给 Docker 引擎。 因此，用户不应指定“--pidfile”参数作为其自定义参数的一部分。 此外，自定义参数应确保 Docker 守护程序侦听 Windows 上的默认名称管道（或 Linux 上的 Unix 域套接字），以便 Service Fabric 可以与其通信。|
|CreateFabricRuntimeTimeout|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态| 指定以秒为单位的时间范围。 同步 FabricCreateRuntime 调用的超时时间 |
|DeploymentMaxFailureCount|int，默认值为 20| 动态|重试 DeploymentMaxFailureCount 次应用程序部署后，节点上该应用程序的部署才会失败。| 
|DeploymentMaxRetryInterval| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(3600)|动态| 指定以秒为单位的时间范围。 部署的最大重试时间间隔。 每次连续失败后，重试时间间隔的计算结果为 Min（DeploymentMaxRetryInterval；连续失败计数 * DeploymentRetryBackoffInterval）（即取括号中的最小值） |
|DeploymentRetryBackoffInterval| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(10)|动态|指定以秒为单位的时间范围。 部署失败的回退时间间隔。 每次连续部署失败时，系统重试部署的次数会多达 MaxDeploymentFailureCount 次。 重试时间间隔是连续部署失败的产物，为部署回退时间间隔。 |
|EnableActivateNoWindow| bool，默认值为 FALSE|动态| 激活进程是在不使用任何控制台的情况下在后台中创建的。 |
|EnableDockerHealthCheckIntegration|bool，默认值为 TRUE|静态|实现 docker HEALTHCHECK 事件与 Service Fabric 系统运行状况报告的集成 |
|EnableProcessDebugging|bool，默认值为 FALSE|动态| 在调试器下启用启动应用程序主机 |
|EndpointProviderEnabled| bool，默认值为 FALSE|静态| 启用 Fabric 终结点资源管理。 需要指定 FabricNode 中开始和结尾应用程序端口范围。 |
|FabricContainerAppsEnabled| bool，默认值为 FALSE|静态| |
|FirewallPolicyEnabled|bool，默认值为 FALSE|静态| 允许为具有 ServiceManifest 中指定的显式端口的终结点资源打开防火墙端口 |
|GetCodePackageActivationContextTimeout|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态|指定以秒为单位的时间范围。 CodePackageActivationContext 调用的超时时间。 这不适用于临时服务。 |
|IPProviderEnabled|bool，默认值为 FALSE|静态|启用 IP 地址的管理。 |
|LinuxExternalExecutablePath|wstring，默认值为 L"/usr/bin/" |静态|节点上外部可执行命令的主目录。|
|NTLMAuthenticationEnabled|bool，默认值为 FALSE|静态| 启用对以其他用户身份运行的代码包使用 NTLM 的支持，以跨计算机的进程进行安全通信。 |
|NTLMAuthenticationPasswordSecret|SecureString，默认值为 Common::SecureString(L"")|静态|用于生成 NTLM 用户的密码的加密方式。 如果 NTLMAuthenticationEnabled 为 true，则必须设置。 由部署器进行验证。 |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan，默认值为 Common::TimeSpan::FromMinutes(3)|动态|指定以秒为单位的时间范围。 特定于环境的设置，主机以此定期时间间隔进行扫描，查找用于 FileStoreService NTLM 配置的新证书。 |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan，默认值为 Common::TimeSpan::FromMinutes(4)|动态| 指定以秒为单位的时间范围。 使用证书公用名称配置 NTLM 用户的超时时间。 FileStoreService 共享需要 NTLM 用户。 |
|RegisterCodePackageHostTimeout|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态| 指定以秒为单位的时间范围。 FabricRegisterCodePackageHost 同步调用的超时时间值。 这仅适用于多代码包应用程序主机，如 FWP |
|RequestTimeout|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(30)|动态| 指定以秒为单位的时间范围。 这表示用户的应用程序主机与各种托管的相关操作（如中心注册、运行时注册）的 Fabric 进程之间的通信超时时间。 |
|RunAsPolicyEnabled| bool，默认值为 FALSE|静态| 允许以运行结构进程的用户以外的本地用户身份运行代码包。 为启用此策略，必须以 SYSTEM 或具有 SeAssignPrimaryTokenPrivilege 的用户的身份运行 Fabric。 |
|ServiceFactoryRegistrationTimeout| TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态|指定以秒为单位的时间范围。 同步 Register(Stateless/Stateful)ServiceFactory 调用的超时时间 |
|ServiceTypeDisableFailureThreshold |整数，默认值为 1 |动态|这是失败计数的阈值，超过此值后，将通知 FailoverManager (FM) 禁用该节点上的服务类型，并尝试在另一个节点上放置对象。 |
|ServiceTypeDisableGraceInterval|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(30)|动态|指定以秒为单位的时间范围。 时间间隔，超过该时间间隔后禁用服务类型 |
|ServiceTypeRegistrationTimeout |以秒为单位的时间，默认值为 300 |动态|允许在结构中注册 ServiceType 的最长时间 |
|UseContainerServiceArguments|bool，默认值为 TRUE|静态|此配置指示承载跳过将参数（在配置 ContainerServiceArguments 中指定）传递到 Docker 守护程序。|

## <a name="httpgateway"></a>HttpGateway
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ActiveListeners |Uint，默认值为 50 |静态| 要发布到 http 服务器队列的读取数。 此配置控制 HttpGateway 可以满足的并发请求数。 |
|HttpGatewayHealthReportSendInterval |以秒为单位的时间，默认值为 30 |静态|指定以秒为单位的时间范围。 Http 网关将累积的运行状况报告发送至运行状况管理器的时间间隔。 |
|IsEnabled|Bool，默认值为 false |静态| 启用/禁用 HttpGateway。 HttpGateway 默认已禁用。 |
|MaxEntityBodySize |Uint，默认值为 4194304 |动态|提供可预期的 http 请求正文的最大大小。 默认值为 4MB。 如果请求的正文大小大于此值，Httpgateway 将无法满足该请求。 最小读取块区大小为 4096 个字节。 因此，该值必须 > = 4096。 |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ClientCopyTimeout | 以秒为单位的时间，默认值为 1800 |动态| 指定以秒为单位的时间范围。 对映像存储区服务的顶级复制请求的超时值。 |
|ClientDefaultTimeout | 以秒为单位的时间，默认值为 180 |动态| 指定以秒为单位的时间范围。 对映像存储区服务的所有未上传/未下载请求（如 exists、delete）的超时值。 |
|ClientDownloadTimeout | 以秒为单位的时间，默认值为 1800 |动态| 指定以秒为单位的时间范围。 对映像存储区服务的顶级下载请求的超时值。 |
|ClientListTimeout | 以秒为单位的时间，默认值为 600 |动态|指定以秒为单位的时间范围。 对映像存储区服务的顶级列表请求的超时值。 |
|ClientUploadTimeout |以秒为单位的时间，默认值为 1800 |动态|指定以秒为单位的时间范围。 对映像存储区服务的顶级上传请求的超时值。 |

## <a name="imagestoreservice"></a>ImageStoreService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|已启用 |Bool，默认值为 false |静态|ImageStoreService 的已启用标志。 默认值：false |
|MinReplicaSetSize | Int，默认值为 3 |静态|ImageStoreService 的 MinReplicaSetSize。 |
|PlacementConstraints | string，默认值为“” |静态| ImageStoreService 的 PlacementConstraints。 |
|QuorumLossWaitDuration | 以秒为单位的时间，默认值为 MaxValue |静态| 指定以秒为单位的时间范围。 ImageStoreService 的 QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration | 以秒为单位的时间，默认值为 60.0 * 30 |静态|指定以秒为单位的时间范围。 ImageStoreService 的 ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration | 以秒为单位的时间，默认值为 3600.0 * 2 |静态| 指定以秒为单位的时间范围。 ImageStoreService 的 StandByReplicaKeepDuration。 |
|TargetReplicaSetSize | Int，默认值为 7 |静态|ImageStoreService 的 TargetReplicaSetSize。 |

## <a name="ktllogger"></a>KtlLogger
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int，默认值为 1 |动态|该标志指示是否应自动且动态地配置内存设置。 如果设置为 0，则根据系统条件直接使用内存配置设置而不进行任何更改。 如果设置为 1，则自动配置内存设置，并可根据系统条件进行更改。 |
|MaximumDestagingWriteOutstandingInKB | Int，默认值为 0 |动态|共享日志可位于专用日志之前的 KB 数。 使用 0 表示没有限制。
|SharedLogId |string，默认值为“” |静态|共享日志容器的唯一 guid。 若要使用 Fabric 数据根目录下的默认路径，请设置为“”。 |
|SharedLogPath |string，默认值为“” |静态|要放置共享日志容器的位置的路径和文件名。 设置为“”表示使用 Fabric 数据根目录下的默认路径。 |
|SharedLogSizeInMB |Int，默认值为 8192 |静态|共享日志容器中要分配的 MB 数。 |
|WriteBufferMemoryPoolMaximumInKB | Int，默认值为 0 |动态|允许写入缓冲区内存池增长到的 KB 数。 使用 0 表示没有限制。 |
|WriteBufferMemoryPoolMinimumInKB |Int，默认值为 8388608 |动态|最初为写入缓冲区内存池分配的 KB 数。 设置为 0 表示没有限制，默认值应与以下 SharedLogSizeInMB 值保持一致。 |

## <a name="management"></a>管理
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AzureStorageMaxConnections | Int，默认值为 5000 |动态|最大并发 Azure 存储连接数。 |
|AzureStorageMaxWorkerThreads | Int，默认值为 25 |动态|最大并行工作线程数。 |
|AzureStorageOperationTimeout | 以秒为单位的时间，默认值为 6000 |动态|指定以秒为单位的时间范围。 完成 xstore 操作的超时时间。 |
|CleanupApplicationPackageOnProvisionSuccess|bool，默认值为 FALSE |动态|此配置启用或禁用成功预配的应用程序包自动清理。 |
|DisableChecksumValidation | Bool，默认值为 false |静态| 通过此配置可在应用程序预配过程中启用或禁用校验和验证。 |
|DisableServerSideCopy | Bool，默认值为 false |静态|此配置可以在应用程序预配过程中启用或禁用 ImageStore 上应用程序包的服务器端副本。 |
|ImageCachingEnabled | Bool，默认值为 true |静态|通过此配置可启用或禁用缓存。 |
|ImageStoreConnectionString |SecureString |静态|ImageStore 的根的连接字符串。 |
|ImageStoreMinimumTransferBPS | Int，默认值为 1024 |动态|群集和 ImageStore 之间的最小传输速率。 此值用于确定访问外部 ImageStore 时的超时时间。 仅当群集和 ImageStore 之间的延迟较高时可更改此值，以允许群集获得更多的时间从外部 ImageStore 进行下载。 |

## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap，默认值为 None|动态|为群集中的指标确定一组 MetricActivityThresholds。 如果 maxNodeLoad 大于 MetricActivityThresholds，则均衡会起作用。 定义碎片整理指标的负载量，等于或低于此值 Service Fabric 将认为该节点为空 |

## <a name="metricbalancingthresholds"></a>MetricBalancingThresholds
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap，默认值为 None|动态|为群集中的指标确定一组 MetricBalancingThresholds。 如果 maxNodeLoad/minNodeLoad 大于 MetricBalancingThresholds，则均衡会起作用。 如果至少一个 FD 或 UD 上的 maxNodeLoad/minNodeLoad 小于 MetricBalancingThresholds，则碎片整理会起作用。 |

## <a name="namingservice"></a>NamingService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int，默认值为 0 |静态|命名网关处的 LRU 服务说明缓存中可维持的最大条目数（设置为 0 表示无限制）。 |
|MaxClientConnections |Int，默认值为 1000 |动态|每个网关允许的最大客户端连接数。 |
|MaxFileOperationTimeout |以秒为单位的时间，默认值为 30 |动态|指定以秒为单位的时间范围。 文件存储服务操作的最大超时时间。 将拒绝指定更长超时时间的请求。 |
|MaxIndexedEmptyPartitions |Int，默认值为 1000 |动态|将在通知缓存中保持被索引状态以同步重新连接的客户端的最大空分区数。 将按查找版本的升序顺序，从索引中删除超出此数目的所有空分区。 重新连接的客户端仍然可以同步并接收错过的空分区更新，但是同步协议会更昂贵。 |
|MaxMessageSize |Int，默认值为 4\*1024\*1024 |静态|使用命名时客户端节点通信的最大消息大小。 DOS 攻击缓解，默认值为 4MB。 |
|MaxNamingServiceHealthReports | Int，默认值为 10 |动态|命名存储服务一次所报告的运行不正常的最大慢速操作数量。 如果设置为 0，则将发送所有慢速操作。 |
|MaxOperationTimeout |以秒为单位的时间，默认值为 600 |动态|指定以秒为单位的时间范围。 所允许的客户端操作的最大超时时间。 将拒绝指定更长超时时间的请求。 |
|MaxOutstandingNotificationsPerClient |Int，默认值为 1000 |动态|网关强行关闭客户端注册前的最大未完成通知数。 |
|MinReplicaSetSize | Int，默认值为 3 |不允许| 完成更新所需写入的最少命名服务副本数。 如果系统中的活动副本数少于此数量，则可靠性系统将拒绝对命名服务存储执行的更新操作，直到副本还原为止。 此值不应超过 TargetReplicaSetSize 的值。 |
|PartitionCount |Int，默认值为 3 |不允许|要创建的命名服务存储的分区数。 每个分区都拥有与其索引相对应的单个分区键，因此存在分区键 [0; PartitionCount)。 增加命名服务分区数可减少由任何备份副本集保持的数据的平均量，从而增加命名服务可以执行的规模；其代价是增加资源的利用（因为必须维护 PartitionCount*ReplicaSetSize 服务副本）。|
|PlacementConstraints | string，默认值为“” |不允许| 命名服务的放置约束。 |
|QuorumLossWaitDuration | 以秒为单位的时间，默认值为 MaxValue |不允许| 指定以秒为单位的时间范围。 命名服务进入仲裁丢失状态时，此计时器将启动。 指定时间到期后，FM 将不可用副本视为丢失，并尝试恢复仲裁。 请注意，这可能导致数据丢失。 |
|RepairInterval | 以秒为单位的时间，默认值为 5 |静态| 指定以秒为单位的时间范围。 针对授权所有者和名称所有者之间命名不一致情况的修复操作的时间间隔。 |
|ReplicaRestartWaitDuration | 以秒为单位的时间，默认值为 (60.0 * 30)|不允许| 指定以秒为单位的时间范围。 命名服务副本不可用时，此定时器将启动。 当它到期时，FM 将开始替换已经关闭的副本（暂不将其视为丢失）。 |
|ServiceDescriptionCacheLimit | Int，默认值为 0 |静态| 命名存储服务处的 LRU 服务说明缓存中可维持的最大条目数（设置为 0 表示无限制）。 |
|ServiceNotificationTimeout |以秒为单位的时间，默认值为 30 |动态|指定以秒为单位的时间范围。 将服务通知传送到客户端时使用的超时时间。 |
|StandByReplicaKeepDuration | 以秒为单位的时间，默认值为 3600.0 * 2 |不允许| 指定以秒为单位的时间范围。 命名服务副本从不可用状态恢复时，可能已被替换为另一副本。 此定时器决定 FM 在放弃备用副本之前保留其多长时间。 |
|TargetReplicaSetSize |Int，默认值为 7 |不允许|命名服务存储的每个分区的副本集数量。 增加副本集的数量会增加命名服务存储中信息的可靠性水平；减少此更改将导致信息由于节点故障而丢失；其代价是增加 Windows Fabric 上的负载以及对命名数据执行更新所花费的时间。|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap，默认值为 None|动态|每个指标名称的节点容量百分比；用作缓冲区，以便在故障转移的情况下在节点上保留一些可用空间。 |

## <a name="nodecapacities"></a>NodeCapacities
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |静态|不同指标的节点容量集合。 |

## <a name="nodedomainids"></a>NodeDomainIds
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |静态|描述节点所属的容错域。 通过用于描述数据中心中节点所在位置的 URI 定义容错域。  容错域 URI 的格式是 fd:/fd/，后跟 URI 路径段。|
|UpgradeDomainId |string，默认值为“” |静态|描述节点所属的升级域。 |

## <a name="nodeproperties"></a>NodeProperties
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |静态|节点属性的字符串键值对的集合。 |

## <a name="paas"></a>Paas
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ClusterId |string，默认值为“” |不允许|由 Fabric 用于配置保护的 X509 证书存储。 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|计数器 |String | 动态 |要收集的性能计数器的逗号分隔列表。 |
|IsEnabled |Bool，默认值为 true | 动态 |标志指示是否启用本地节点上的性能计数器集合。 |
|MaxCounterBinaryFileSizeInMB |Int，默认值为 1 | 动态 |每个性能计数器二进制文件的最大大小（以 MB 为单位）。 |
|NewCounterBinaryFileCreationIntervalInMinutes |Int，默认值为 10 | 动态 |在其之后创建新的性能计数器二进制文件的最大间隔（以秒为单位）。 |
|SamplingIntervalInSeconds |Int，默认值为 60 | 动态 |正在收集的性能计数器的采样间隔。 |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int，默认值为 0 | 动态|确定相关性约束的优先级：0：硬；1：软；负值：忽略。 |
|ApplicationCapacityConstraintPriority | Int，默认值为 0 | 动态|确定容量约束的优先级：0：硬；1：软；负值：忽略。 |
|AutoDetectAvailableResources|bool，默认值为 TRUE|静态|此配置会触发对节点上可用资源（CPU 和内存）的自动检测，将此配置设置为 true 时，如果用户已指定错误的节点容量或者根本未定义它们，我们会读取实际容量并更正它们，将此配置设置为 false 时 - 我们会跟踪用户已指定错误的节点容量这一警告；但不会纠正它们；这意味着用户希望将容量指定为 > 节点实际拥有的容量，或者如果未指定容量；它会认为是不限的容量 |
|BalancingDelayAfterNewNode | 以秒为单位的时间，默认值为 120 |动态|指定以秒为单位的时间范围。 添加新节点后，不在此时间段内启动平衡活动。 |
|BalancingDelayAfterNodeDown | 以秒为单位的时间，默认值为 120 |动态|指定以秒为单位的时间范围。 发生节点关闭事件后，不在此时间段内启动平衡活动。 |
|CapacityConstraintPriority | Int，默认值为 0 | 动态|确定容量约束的优先级：0：硬；1：软；负值：忽略。 |
|ConsecutiveDroppedMovementsHealthReportLimit | Int，默认值为 20 | 动态|定义在进行诊断并发出运行状况警告之前，删除 ResourceBalancer 发出的移动的连续次数。 负值：在此情况下没有发出任何警告。 |
|ConstraintFixPartialDelayAfterNewNode | 以秒为单位的时间，默认值为 120 |动态| 指定以秒为单位的时间范围。 添加新节点后，不在此时间段内修复 FaultDomain 和 UpgradeDomain 约束冲突。 |
|ConstraintFixPartialDelayAfterNodeDown | 以秒为单位的时间，默认值为 120 |动态| 指定以秒为单位的时间范围。 发生节点关闭事件后，不在此时间段内修复 FaultDomain 和 UpgradeDomain 约束冲突。 |
|ConstraintViolationHealthReportLimit | Int，默认值为 50 |动态| 定义在进行诊断并发出运行状况报告之前，违反约束的副本持续处于未解决状态的次数。 |
|DetailedConstraintViolationHealthReportLimit | Int，默认值为 200 |动态| 定义在进行诊断并发出详细运行状况报告之前，违反约束的副本持续未固定的次数。 |
|DetailedDiagnosticsInfoListLimit | Int，默认值为 15 |动态| 定义诊断中每个约束在截断前要包含的诊断条目数（附带详细信息）。|
|DetailedNodeListLimit | Int，默认值为 15 |动态| 定义在未放置副本报告中每个约束在截断前要包含的节点数。 |
|DetailedPartitionListLimit | Int，默认值为 15 |动态| 定义诊断中一个约束在截断前要包含的分区数（按诊断条目）。 |
|DetailedVerboseHealthReportLimit | Int，默认值为 200 | 动态|定义在发出详细运行状况报告之前，未放置的副本必须持续处于未放置状态的次数。 |
|FaultDomainConstraintPriority | Int，默认值为 0 |动态| 确定容错域约束的优先级：0：硬；1：软；负值：忽略。 |
|GlobalMovementThrottleCountingInterval | 以秒为单位的时间，默认值为 600 |静态| 指定以秒为单位的时间范围。 指示刚过去的用于跟踪每个域副本移动的时间间隔的长度（与 GlobalMovementThrottleThreshold 配合使用）。 要完全忽略全局限制，可以将其设置为 0。 |
|GlobalMovementThrottleThreshold | Uint，默认值为 1000 |动态| GlobalMovementThrottleCountingInterval 所指示的刚过去的时间间隔中的平衡阶段中所允许的最大移动数。 |
|GlobalMovementThrottleThresholdForBalancing | Uint，默认值为 0 | 动态|GlobalMovementThrottleCountingInterval 所指示的刚过去的时间间隔中的平衡阶段中所允许的最大移动数。 0 表示没有限制。 |
|GlobalMovementThrottleThresholdForPlacement | Uint，默认值为 0 |动态| GlobalMovementThrottleCountingInterval 所指示的刚过去的时间间隔中的放置阶段中所允许的最大移动数。0 表示没有限制。|
|GlobalMovementThrottleThresholdPercentage|double，默认值为 0|动态|在刚过去的、由 GlobalMovementThrottleCountingInterval 指定的时间间隔中，均衡和放置阶段所允许的最大总移动数（以群集中副本总数的百分比表示）。 0 表示没有限制。 如果指定了此参数和 GlobalMovementThrottleThreshold，则使用更保守的限制。|
|GlobalMovementThrottleThresholdPercentageForBalancing|double，默认值为 0|动态|在刚过去的、由 GlobalMovementThrottleCountingInterval 指定的时间间隔中，均衡阶段所允许的最大移动数（以 PLB 中副本总数的百分比表示）。 0 表示没有限制。 如果指定了此参数和 GlobalMovementThrottleThresholdForBalancing，则使用更保守的限制。|
|InBuildThrottlingAssociatedMetric | string，默认值为“” |静态| 此限制的关联指标名称。 |
|InBuildThrottlingEnabled | Bool，默认值为 false |动态| 决定是否启用内置限制。 |
|InBuildThrottlingGlobalMaxValue | Int，默认值为 0 |动态|全局范围内所允许的最大内置副本数。 |
|InterruptBalancingForAllFailoverUnitUpdates | Bool，默认值为 false | 动态|决定是否有任何类型的故障转移单元更新应中断快速或慢速平衡运行。 如果指定为“false”，则会在 FailoverUnit 出现以下情况时中断均衡运行：被创建/删除、缺少副本、更改了主副本位置或更改了副本数量。 在其他情况下不会中断均衡运行，包括 FailoverUnit 具有额外副本、更改了任何副本标志、仅更改了分区版本或任何其他情况。 |
|MinConstraintCheckInterval | 以秒为单位的时间，默认值为 1 |动态| 指定以秒为单位的时间范围。 定义在两个连续的约束检查循环之前必须经过的最短时间。 |
|MinLoadBalancingInterval | 以秒为单位的时间，默认值为 5 |动态| 指定以秒为单位的时间范围。 定义在两个连续平衡循环之前必须经过的最短时间。 |
|MinPlacementInterval | 以秒为单位的时间，默认值为 1 |动态| 指定以秒为单位的时间范围。 定义在两个连续的放置循环之前必须经过的最短时间。 |
|MoveExistingReplicaForPlacement | Bool，默认值为 true |动态|该设置决定放置过程中是否移动现有副本。 |
|MovementPerPartitionThrottleCountingInterval | 以秒为单位的时间，默认值为 600 |静态| 指定以秒为单位的时间范围。 指示刚过去的用于跟踪每个分区的副本移动的时间间隔的长度（与 MovementPerPartitionThrottleThreshold 配合使用）。 |
|MovementPerPartitionThrottleThreshold | Uint，默认值为 50 |动态| 如果分区副本的均衡相关移动数量在刚过去的、由 MovementPerPartitionThrottleCountingInterval 指示的间隔时间中已达到或超过 MovementPerFailoverUnitThrottleThreshold，则该分区不会发生均衡相关的移动。 |
|MoveParentToFixAffinityViolation | Bool，默认值为 false |动态| 该设置决定是否可通过移动父副本来修复相关性约束。|
|PartiallyPlaceServices | Bool，默认值为 true |动态| 决定在给定有限的适当节点的情况下，是否“全部或完全不”放置群集中的所有服务副本。|
|PlaceChildWithoutParent | Bool，默认值为 true | 动态|该设置决定如果没启用父副本，是否可以放置子服务副本。 |
|PlacementConstraintPriority | Int，默认值为 0 | 动态|决定放置约束的优先级：0：硬；1：软；负值：忽略。 |
|PlacementConstraintValidationCacheSize | Int，默认值为 10000 |动态| 限制用于快速验证和缓存放置约束表达式的表格的大小。 |
|PlacementSearchTimeout | 以秒为单位的时间，默认值为 0.5 |动态| 指定以秒为单位的时间范围。 这是放置服务时，返回结果之前可搜索的最长时间。 |
|PLBRefreshGap | 以秒为单位的时间，默认值为 1 |动态| 指定以秒为单位的时间范围。 定义 PLB 再次刷新状态之前必须经过的最短时间。 |
|PreferredLocationConstraintPriority | Int，默认值为 2| 动态|确定首选位置约束的优先级：0：硬；1：软；2：最佳；负值：忽略 |
|PreventTransientOvercommit | Bool，默认值为 false | 动态|确定 PLB 是否应该立即对由启动的移动所释放的资源进行计数。 默认情况下，PLB 可以在同一节点上发起移出和移入操作，这会造成暂时性过载。 将此参数设置为 true 可防止这种过载，并可禁用按需碎片整理（也称为 placementWithMove）。 |
|ScaleoutCountConstraintPriority | Int，默认值为 0 |动态| 确定横向扩展计数约束的优先级：0：硬；1：软；负值：忽略。 |
|SwapPrimaryThrottlingAssociatedMetric | string，默认值为“”|静态| 此限制的关联指标名称。 |
|SwapPrimaryThrottlingEnabled | Bool，默认值为 false|动态| 确定是否启用交换主限制。 |
|SwapPrimaryThrottlingGlobalMaxValue | Int，默认值为 0 |动态| 全局范围内所允许的最大交换主副本数。 |
|TraceCRMReasons |Bool，默认值为 true |动态|指定是否要寻找向操作事件通道移动（CRM 发出的移动）的原因。 |
|UpgradeDomainConstraintPriority | Int，默认值为 1| 动态|确定升级域约束的优先级：0：硬；1：软；负值：忽略。 |
|UseMoveCostReports | Bool，默认值为 false | 动态|指示 LB 忽略评分函数的成本元素，从而可能产生大量可优化平衡放置的移动。 |
|UseSeparateSecondaryLoad | Bool，默认值为 true | 动态|该设置决定是否使用不同的辅助负载。 |
|ValidatePlacementConstraint | Bool，默认值为 true |动态| 指定更新服务的服务说明时，是否验证服务的放置约束表达式。 |
|VerboseHealthReportLimit | Int，默认值为 20 | 动态|定义副本进入未放置状态的次数超过多少次后，便报告副本运行状况警告（如果已启用详细运行状况报告）。 |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | 以秒为单位的时间，默认值为 900 |动态|指定以秒为单位的时间范围。 在应用程序升级过程中，如果服务主机具有进入关闭状态的副本，系统在终止这类服务主机前所等待的时间。|
|FabricUpgradeMaxReplicaCloseDuration | 以秒为单位的时间，默认值为 900 |动态| 指定以秒为单位的时间范围。 在结构升级过程中，如果服务主机具有进入关闭状态的副本，系统在终止这类服务主机前所等待的时间。 |
|GracefulReplicaShutdownMaxDuration|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(120)|动态|指定以秒为单位的时间范围。 如果服务主机具有进入关闭状态的副本，则该配置决定系统在终止这类服务主机前所等待的时间。 如果此值设置为 0，则不会指示关闭副本。|
|NodeDeactivationMaxReplicaCloseDuration | 以秒为单位的时间，默认值为 900 |动态|指定以秒为单位的时间范围。 在节点停用过程中，如果服务主机具有进入关闭状态的副本，系统在终止这类服务主机前所等待的时间。 |
|PeriodicApiSlowTraceInterval | 以秒为单位的时间，默认值为 5 分钟 |动态| 指定以秒为单位的时间范围。 PeriodicApiSlowTraceInterval 定义 API 监视器追溯慢速 API 调用的时间间隔。 |
|ReplicaChangeRoleFailureRestartThreshold|int，默认值为 10|动态| 整数。 指定在主升级过程中 API 故障的次数，超过此次数后应用自动缓解措施（副本重启）。 |
|ReplicaChangeRoleFailureWarningReportThreshold|int，默认值为 2147483647|动态| 整数。 指定在主升级过程中 API 故障的次数，超过此次数后引发警告运行状况报告。|
|ServiceApiHealthDuration | 以秒为单位的时间，默认值为 30 分钟 |动态| 指定以秒为单位的时间范围。 ServiceApiHealthDuration 定义在等待多少时间后 API 仍未运行的话就报告其运行不正常。 |
|ServiceReconfigurationApiHealthDuration | 以秒为单位的时间，默认值为 30 |动态| 指定以秒为单位的时间范围。 ServiceReconfigurationApiHealthDuration 定义在报告运行不正常之前，等待服务 API 运行的时间。 这适用于影响可用性的 API 调用。|

## <a name="replication"></a>复制
| **Parameter** | **允许的值** | **升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan，默认值为 Common::TimeSpan::FromMilliseconds(15)|静态|指定以秒为单位的时间范围。 确定接收到操作后到发送回确认之前，复制器等待的时间。 在该时间段期间接收的其他操作将通过一条消息发送回其确认 -> 减少网络流量，同时可能降低复制器的吞吐量。|
|MaxCopyQueueSize|uint，默认值为 1024|静态|这是用于定义队列初始大小的最大值，该队列用于维护复制操作。 请注意，它必须是 2 的幂。 如果在运行时该队列增长到此大小，将限制主复制器和辅助复制器之间的操作。|
|MaxPrimaryReplicationQueueMemorySize|uint，默认值为 0|静态|这是主复制队列的最大值（以字节为单位）。|
|MaxPrimaryReplicationQueueSize|uint，默认值为 1024|静态|这是主复制队列中可以存在的最大操作数量。 请注意，它必须是 2 的幂。|
|MaxReplicationMessageSize|uint，默认值为 52428800|静态|复制操作的最大消息大小。 默认值为 50MB。|
|MaxSecondaryReplicationQueueMemorySize|uint，默认值为 0|静态|这是辅助复制队列的最大值（以字节为单位）。|
|MaxSecondaryReplicationQueueSize|uint，默认值为 2048|静态|这是辅助复制队列中可以存在的最大操作数量。 请注意，它必须是 2 的幂。|
|QueueHealthMonitoringInterval|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(30)|静态|指定以秒为单位的时间范围。 此值确定复制器用于监视复制操作队列中任何警告/错误运行状况事件的时间段。 如果值为 "0"，则禁用运行状况监视 |
|QueueHealthWarningAtUsagePercent|uint，默认值为 80|静态|此值确定复制队列使用率（以百分比表示），超过该值后我们会报告高队列使用率警告。 QueueHealthMonitoringInterval 的宽限期间隔后，进行此操作。 如果宽限期时间间隔内队列使用率低于此百分比|
|ReplicatorAddress|string，默认值为 L"localhost:0"|静态|采用字符串形式 -'IP:Port' 的终结点，Windows Fabric 复制器将其用于与其他副本建立连接以发送/接收操作。|
|ReplicatorListenAddress|string，默认值为 L"localhost:0"|静态|采用字符串形式 -'IP:Port' 的终结点，Windows Fabric 复制器将其用于从其他副本接收操作。|
|ReplicatorPublishAddress|string，默认值为 L"localhost:0"|静态|采用字符串形式 -'IP:Port' 的终结点，Windows Fabric 复制器将其用于向其他副本发送操作。|
|RetryInterval|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(5)|静态|指定以秒为单位的时间范围。 丢失或拒绝操作时，此计时器确定复制器重试发送该操作的频率。|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parameter** | **允许的值** | **升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|IsEnabled|bool，默认值为 FALSE |静态|控制是否在群集中启用该服务。 |

## <a name="runas"></a>RunAs
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|RunAsAccountName |string，默认值为“” |动态|指示 RunAs 帐户名称。 仅需用于“DomainUser”或“ManagedServiceAccount”帐户类型。 有效值为“domain\user”或“user@domain”。 |
|RunAsAccountType|string，默认值为“” |动态|指示 RunAs 帐户类型。 需用于任何 RunAs 部分，有效值为“DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”。|
|RunAsPassword|string，默认值为“” |动态|指示 RunAs 帐户密码。 仅需用于“DomainUser”帐户类型。 |

## <a name="runasdca"></a>RunAs_DCA
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|RunAsAccountName |string，默认值为“” |动态|指示 RunAs 帐户名称。 仅需用于“DomainUser”或“ManagedServiceAccount”帐户类型。 有效值为“domain\user”或“user@domain”。 |
|RunAsAccountType|string，默认值为“” |动态|指示 RunAs 帐户类型。 需用于任何 RunAs 部分，有效值为“LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”。 |
|RunAsPassword|string，默认值为“” |动态|指示 RunAs 帐户密码。 仅需用于“DomainUser”帐户类型。 |

## <a name="runasfabric"></a>RunAs_Fabric
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|RunAsAccountName |string，默认值为“” |动态|指示 RunAs 帐户名称。 仅需用于“DomainUser”或“ManagedServiceAccount”帐户类型。 有效值为“domain\user”或“user@domain”。 |
|RunAsAccountType|string，默认值为“” |动态|指示 RunAs 帐户类型。 需用于任何 RunAs 部分，有效值为“LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”。 |
|RunAsPassword|string，默认值为“” |动态|指示 RunAs 帐户密码。 仅需用于“DomainUser”帐户类型。 |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|RunAsAccountName |string，默认值为“” |动态|指示 RunAs 帐户名称。 仅需用于“DomainUser”或“ManagedServiceAccount”帐户类型。 有效值为“domain\user”或“user@domain”。 |
|RunAsAccountType|string，默认值为“” |动态|指示 RunAs 帐户类型。 需用于任何 RunAs 部分，有效值为“LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem”。 |
|RunAsPassword|string，默认值为“” |动态|指示 RunAs 帐户密码。 仅需用于“DomainUser”帐户类型。 |

## <a name="security"></a>“安全”
| **Parameter** | **允许的值** |**升级策略**| **指导或简短说明** |
| --- | --- | --- | --- |
|AADClientApplication|string，默认值为 L""|静态|表示 Fabric 客户端的本机客户端应用程序名称或 ID |
|AADClusterApplication|string，默认值为 L""|静态|表示群集的 Web API 应用程序名称或 ID |
|AADTenantId|string，默认值为 L""|静态|租户 ID (GUID) |
|AdminClientCertThumbprints|string，默认值为 L""|动态|管理员角色客户端使用的证书的指纹。 该参数是以逗号分隔的名称列表。 |
|AdminClientClaims|string，默认值为 L""|动态|管理员客户端所需的所有可能的声明；其格式与 ClientClaims 相同；此列表会从内部添加到 ClientClaims；所以不需要另外将相同的条目添加到 ClientClaims。 |
|AdminClientIdentities|string，默认值为 L""|动态|管理员角色结构客户端的 Windows 标识；用于授权特权结构操作。 该参数是以逗号分隔的列表；每个条目是域帐户名或组名称。 为方便起见，自动向运行 fabric.exe 的帐户分配管理员角色；组 ServiceFabricAdministrators 也是这样。 |
|CertificateExpirySafetyMargin|TimeSpan，默认值为 Common::TimeSpan::FromMinutes(43200)|静态|指定以秒为单位的时间范围。 证书过期的安全宽限期；过期日期接近此时间值时证书运行状况报告状态从“OK”更改为“警告”。 默认值为 30 天。 |
|CertificateHealthReportingInterval|TimeSpan，默认值为 Common::TimeSpan::FromSeconds(3600 * 8)|静态|指定以秒为单位的时间范围。 指定证书运行状况报告的时间间隔；默认值为 8 小时；设置为 0 会禁用证书运行状况报告 |
|ClientCertThumbprints|string，默认值为 L""|动态|客户端用于与群集通信的证书的指纹；群集使用此指纹授权传入连接。 该参数是以逗号分隔的名称列表。 |
|ClientClaimAuthEnabled|bool，默认值为 FALSE|静态|指示是否对客户端启用基于声明的身份验证；将此参数设置 true 会隐式设置 ClientRoleEnabled。 |
|ClientClaims|string，默认值为 L""|动态|客户端连接到网关所需的所有可能的声明。 这是一个 'OR' 列表：ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... 每个 ClaimsEntry 均为 "AND" 列表：ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientIdentities|string，默认值为 L""|动态|FabricClient 的 Windows 标识；命名网关使用该参数授权传入连接。 该参数是以逗号分隔的列表；每个条目是域帐户名或组名称。 为方便起见，自动允许运行 fabric.exe 的帐户；组 ServiceFabricAllowedUsers 和 ServiceFabricAdministrators 也是这样。 |
|ClientRoleEnabled|bool，默认值为 FALSE|静态|指示是否启用了客户端角色。设置为 true，则基于客户端标识为其分配角色。 对于 V2；启用该角色意味着不在 AdminClientCommonNames/AdminClientIdentities 中的客户端只能执行只读操作。 |
|ClusterCertThumbprints|string，默认值为 L""|动态|允许加入群集的证书的指纹；以逗号分隔的名称列表。 |
|ClusterCredentialType|string，默认值为 L"None"|不允许|指示为保护群集安全而使用的安全凭据的类型。 有效值为 "None/X509/Windows" |
|ClusterIdentities|string，默认值为 L""|动态|群集节点的 Windows 标识；用于群集成员身份授权。 该参数是以逗号分隔的列表；每个条目是域帐户名或组名称 |
|ClusterSpn|string，默认值为 L""|不允许|群集的服务主体名称；结构以单个域用户（gMSA/域用户帐户）运行。 该参数是租用侦听器和 fabric.exe 中的侦听器的 SPN：联合侦听器、内部复制侦听器、运行时服务侦听器和命名网关侦听器。 结构以计算机帐户运行时该参数应留空；在这种情况下从侦听器传输地址连接端计算侦听器 SPN。 |
|CrlCheckingFlag|uint，默认值为 0x40000000|动态|默认证书链验证标记；可能被特定于组件的标记重写；例如 Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY 设置为 0 会禁用 CRL 检查，支持值的完整列表由 CertGetCertificateChain 的 dwFlags 记录：http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan，默认值为 Common::TimeSpan::FromMinutes(15)|动态|指定以秒为单位的时间范围。 遇到脱机错误后给定证书的 CRL 检查的禁用时间；是否可以忽略 CRL 脱机错误。 |
|CrlOfflineHealthReportTtl|TimeSpan，默认值为 Common::TimeSpan::FromMinutes(1440)|动态|指定以秒为单位的时间范围。 |
|DisableFirewallRuleForDomainProfile| bool，默认值为 TRUE |静态| 指示是否不应对域配置文件启用防火墙规则 |
|DisableFirewallRuleForPrivateProfile| bool，默认值为 TRUE |静态| 指示是否不应对专用配置文件启用防火墙规则 | 
|DisableFirewallRuleForPublicProfile| bool，默认值为 TRUE | 静态|指示是否不应对公共配置文件启用防火墙规则 |
|FabricHostSpn| string，默认值为 L"" |静态| FabricHost 的服务主体名称；结构作为单个域用户（gMSA/域用户帐户）运行并且 FabricHost 在计算机帐户下运行时。 该参数是 FabricHost 的 IPC 侦听器的 SPN；由于 FabricHost 在计算机帐户下运行，所以该参数默认留空 |
|IgnoreCrlOfflineError|bool，默认值为 FALSE|动态|服务器端验证传入客户端证书时，是否忽略 CRL 脱机错误 |
|IgnoreSvrCrlOfflineError|bool，默认值为 TRUE|动态|客户端验证传入服务器证书时，是否忽略 CRL 脱机错误；默认值为 true。 具有吊销的服务器证书的攻击需要破坏 DNS；比具有吊销的客户端证书的攻击更难。 |
|ServerAuthCredentialType|string，默认值为 L"None"|静态|指示为保护 FabricClient 和群集之间的通信而使用的安全凭据的类型。 有效值为 "None/X509/Windows" |
|ServerCertThumbprints|string，默认值为 L""|动态|群集用于与客户端通信的服务器证书的指纹；客户端使用该指纹对群集进行身份验证。 该参数是以逗号分隔的名称列表。 |
|SettingsX509StoreName| string，默认值为 L"MY"| 动态|结构用于保护配置的 X509 证书存储 |
|UseClusterCertForIpcServerTlsSecurity|bool，默认值为 FALSE|静态|是否使用群集证书保护 IPC 服务器 TLS 传输单元 |
|X509Folder|string，默认值为 /var/lib/waagent|静态|X509 证书和私钥所在的文件夹 |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap，默认值为 None|动态| |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ActivateNode |string，默认值为“Admin” |动态| 用于激活节点的安全性配置。 |
|CancelTestCommand |string，默认值为“Admin” |动态| 取消特定的 TestCommand（如果处于运行中）。 |
|CodePackageControl |string，默认值为“Admin” |动态| 用于重启代码包的安全性配置。 |
|CreateApplication |string，默认值为“Admin” | 动态|用于应用程序创建的安全性配置。 |
|CreateComposeDeployment|string，默认值为 L"Admin"| 动态|创建由组合文件说明的组合部署 |
|CreateName |string，默认值为“Admin” |动态|用于命名 URI 创建的安全性配置。 |
|CreateService |string，默认值为“Admin” |动态| 用于服务创建的安全性配置。 |
|CreateServiceFromTemplate |string，默认值为“Admin” |动态|用于通过模板创建服务的安全性配置。 |
|DeactivateNode |string，默认值为“Admin” |动态| 用于停用节点的安全性配置。 |
|DeactivateNodesBatch |string，默认值为“Admin” |动态| 用于停用多个节点的安全性配置。 |
|删除 |string，默认值为“Admin” |动态| 用于映像存储区客户端删除操作的安全性配置。 |
|DeleteApplication |string，默认值为“Admin” |动态| 用于应用程序删除的安全性配置。 |
|DeleteComposeDeployment|string，默认值为 L"Admin"| 动态|删除组合部署 |
|DeleteName |string，默认值为“Admin” |动态|用于命名 URI 删除的安全性配置。 |
|DeleteService |string，默认值为“Admin” |动态|用于服务删除的安全性配置。 |
|EnumerateProperties |string，默认值为“Admin\|\|User” | 动态|用于枚举命名属性的安全性配置。 |
|EnumerateSubnames |string，默认值为“Admin\|\|User” |动态| 用于枚举命名 URI 的安全性配置。 |
|FileContent |string，默认值为“Admin” |动态| 用于传输映像存储客户端文件（群集外部）的安全性配置。 |
|FileDownload |string，默认值为“Admin” |动态| 用于启动映像存储客户端文件下载（群集外部）的安全性配置。 |
|FinishInfrastructureTask |string，默认值为“Admin” |动态| 用于完成基础结构任务的安全性配置。 |
|GetChaosReport | string，默认值为“Admin\|\|User” |动态| 获取给定时间范围内混沌的状态。 |
|GetClusterConfiguration | string，默认值为“Admin\|\|User” | 动态|在分区上引入 GetClusterConfiguration。 |
|GetClusterConfigurationUpgradeStatus | string，默认值为“Admin\|\|User” |动态| 在分区上引入 GetClusterConfigurationUpgradeStatus。 |
|GetFabricUpgradeStatus |string，默认值为“Admin\|\|User” |动态| 用于轮询群集升级状态的安全性配置。 |
|GetNodeDeactivationStatus |string，默认值为“Admin” |动态| 用于检查停用状态的安全性配置。 |
|GetNodeTransitionProgress | string，默认值为“Admin\|\|User” |动态| 用于获取节点转换命令进度的安全配置。 |
|GetPartitionDataLossProgress | string，默认值为“Admin\|\|User” | 动态|获取调用数据丢失 API 调用的进度。 |
|GetPartitionQuorumLossProgress | string，默认值为“Admin\|\|User” |动态| 获取调用仲裁丢失 API 调用的进度。 |
|GetPartitionRestartProgress | string，默认值为“Admin\|\|User” |动态| 获取重启分区 API 调用的进度。 |
|GetServiceDescription |string，默认值为“Admin\|\|User” |动态| 用于长时间轮询服务通知和读取服务描述的安全性配置。 |
|GetStagingLocation |string，默认值为“Admin” |动态| 用于检索映像存储客户端暂存位置的安全性配置。 |
|GetStoreLocation |string，默认值为“Admin” |动态| 用于检索映像存储客户端存储位置的安全性配置。 |
|GetUpgradeOrchestrationServiceState|string，默认值为 L"Admin"| 动态|在每个分区上引发 GetUpgradeOrchestrationServiceState |
|GetUpgradesPendingApproval |string，默认值为“Admin” |动态| 在分区上引入 GetUpgradesPendingApproval。 |
|GetUpgradeStatus |string，默认值为“Admin\|\|User” |动态| 用于轮询应用程序升级状态的安全性配置。 |
|InternalList |string，默认值为“Admin” | 动态|用于映像存储客户端文件列表操作（内部）的安全性配置。 |
|InvokeContainerApi|wstring，默认值为 L"Admin"|动态|调用容器 API |
|InvokeInfrastructureCommand |string，默认值为“Admin” |动态| 用于基础结构任务管理命令的安全性配置。 |
|InvokeInfrastructureQuery |string，默认值为“Admin\|\|User” | 动态|用于查询基础结构任务的安全性配置。 |
|列出 |string，默认值为“Admin\|\|User” | 动态|用于映像存储客户端文件列表操作的安全性配置。 |
|MoveNextFabricUpgradeDomain |string，默认值为“Admin” |动态| 用于使用显式升级域恢复群集升级的安全性配置。 |
|MoveNextUpgradeDomain |string，默认值为“Admin” |动态| 用于使用显式升级域恢复应用程序升级的安全性配置。 |
|MoveReplicaControl |string，默认值为“Admin” | 动态|移动副本。 |
|NameExists |string，默认值为“Admin\|\|User” | 动态|用于检查命名 URI 存在的安全性配置。 |
|NodeControl |string，默认值为“Admin” |动态| 用于启动、停止和重启节点的安全性配置。 |
|NodeStateRemoved |string，默认值为“Admin” |动态| 用于报告删的节点状态的安全性配置。 |
|Ping |string，默认值为“Admin\|\|User” |动态| 用于客户端 ping 的安全性配置。 |
|PredeployPackageToNode |string，默认值为“Admin” |动态| 预部署 API。 |
|PrefixResolveService |string，默认值为“Admin\|\|User” |动态| 用于基于投诉的服务前缀解析的安全配置。 |
|PropertyReadBatch |string，默认值为“Admin\|\|User” |动态| 用于命名属性读取操作的安全性配置。 |
|PropertyWriteBatch |string，默认值为“Admin” |动态|用于命名属性写入操作的安全性配置。 |
|ProvisionApplicationType |string，默认值为“Admin” |动态| 用于应用程序类型预配的安全性配置。 |
|ProvisionFabric |string，默认值为“Admin” |动态| 用于预配 MSI 和/或群集清单的安全性配置。 |
|查询 |string，默认值为“Admin\|\|User” |动态| 用于查询的安全性配置。 |
|RecoverPartition |string，默认值为“Admin” | 动态|用于恢复分区的安全性配置。 |
|RecoverPartitions |string，默认值为“Admin” | 动态|用于恢复多个分区的安全性配置。 |
|RecoverServicePartitions |string，默认值为“Admin” |动态| 用于恢复服务分区的安全性配置。 |
|RecoverSystemPartitions |string，默认值为“Admin” |动态| 用于恢复系统服务分区的安全性配置。 |
|RemoveNodeDeactivations |string，默认值为“Admin” |动态| 用于在多个节点上还原停用操作的安全性配置。 |
|ReportFabricUpgradeHealth |string，默认值为“Admin” |动态| 用于恢复群集升级并提供当前升级进度的安全性配置。 |
|ReportFault |string，默认值为“Admin” |动态| 用于报告故障的安全性配置。 |
|ReportHealth |string，默认值为“Admin” |动态| 用于报告运行状况的安全性配置。 |
|ReportUpgradeHealth |string，默认值为“Admin” |动态| 用于恢复应用程序升级并提供当前升级进度的安全性配置。 |
|ResetPartitionLoad |string，默认值为“Admin\|\|User” |动态| 用于 failoverUnit 的重置负载的安全性配置。 |
|ResolveNameOwner |string，默认值为“Admin\|\|User” | 动态|用于解析命名 URI 所有者的安全性配置。 |
|ResolvePartition |string，默认值为“Admin\|\|User” | 动态|用于解析系统服务的安全性配置。 |
|ResolveService |string，默认值为“Admin\|\|User” |动态| 用于基于投诉的服务解析的安全配置。 |
|ResolveSystemService|string，默认值为 L"Admin\|\|User"|动态| 用于解析系统服务的安全性配置 |
|RollbackApplicationUpgrade |string，默认值为“Admin” |动态| 用于回滚应用程序升级的安全性配置。 |
|RollbackFabricUpgrade |string，默认值为“Admin” |动态| 用于回滚群集升级的安全性配置。 |
|ServiceNotifications |string，默认值为“Admin\|\|User” |动态| 用于基于时间的服务通知的安全配置。 |
|SetUpgradeOrchestrationServiceState|string，默认值为 L"Admin"| 动态|在分区上引发 SetUpgradeOrchestrationServiceState |
|StartApprovedUpgrades |string，默认值为“Admin” |动态| 在分区上引入 StartApprovedUpgrades。 |
|StartChaos |string，默认值为“Admin” |动态| 启动混沌 - 如果尚未启动。 |
|StartClusterConfigurationUpgrade |string，默认值为“Admin” |动态| 在分区上引入 StartClusterConfigurationUpgrade。 |
|StartInfrastructureTask |string，默认值为“Admin” | 动态|用于启动基础结构任务的安全性配置。 |
|StartNodeTransition |string，默认值为“Admin” |动态| 用于启动节点转换的安全性配置。 |
|StartPartitionDataLoss |string，默认值为“Admin” |动态| 在分区上引入数据丢失。 |
|StartPartitionQuorumLoss |string，默认值为“Admin” |动态| 在分区上引入仲裁丢失。 |
|StartPartitionRestart |string，默认值为“Admin” |动态| 同时重启分区的部分或所有副本。 |
|StopChaos |string，默认值为“Admin” |动态| 停止混沌 - 如果已启动。 |
|ToggleVerboseServicePlacementHealthReporting | string，默认值为“Admin\|\|User” |动态| 用于切换详细服务放置运行状况报告的安全配置。 |
|UnprovisionApplicationType |string，默认值为“Admin” |动态| 用于取消设置应用程序类型的安全性配置。 |
|UnprovisionFabric |string，默认值为“Admin” |动态| 用于取消设置 MSI 和/或群集清单的安全性配置。 |
|UnreliableTransportControl |string，默认值为“Admin” |动态| 添加和删除行为的不可靠传输。 |
|UpdateService |string，默认值为“Admin” |动态|用于服务更新的安全性配置。 |
|UpgradeApplication |string，默认值为“Admin” |动态| 用于启动或中断应用程序升级的安全性配置。 |
|UpgradeComposeDeployment|string，默认值为 L"Admin"| 动态|升级组合部署 |
|UpgradeFabric |string，默认值为“Admin” |动态| 用于启动群集升级的安全性配置。 |
|上载 |string，默认值为“Admin” | 动态|用于映像存储客户端上传操作的安全性配置。 |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap，默认值为 None |动态|客户端证书的 X509 颁发者证书存储；名称 = clientIssuerCN；值 = 逗号分隔的存储列表 |

## <a name="securityclientx509names"></a>Security/ClientX509Names
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap，默认值为 None|动态| |

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap，默认值为 None |动态|群集证书的 X509 颁发者证书存储；名称 = clusterIssuerCN；值 = 逗号分隔的存储列表 |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap，默认值为 None|动态| |

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap，默认值为 None |动态|服务器证书的 X509 颁发者证书存储；名称 = serverIssuerCN；值 = 逗号分隔的存储列表 |

## <a name="securityserverx509names"></a>Security/ServerX509Names
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap，默认值为 None|动态| |

## <a name="setup"></a>设置
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|ContainerNetworkName|string，默认值为 L""| 静态 |设置容器网络时要使用的网络名称。|
|ContainerNetworkSetup|bool，默认值为 FALSE| 静态 |是否设置容器网络。|
|FabricDataRoot |String | 不允许 |Service Fabric 数据根目录。 Azure 默认位置为 d:\svcfab |
|FabricLogRoot |String | 不允许 |Service Fabric 日志根目录。 这是放置 SF 日志和跟踪信息的位置。 |
|NodesToBeRemoved|string，默认值为“”| 动态 |应在配置升级过程中删除的节点。 （仅适用于独立部署）|
|ServiceRunAsAccountName |String | 不允许 |运行 Fabric 主机服务的帐户名称。 |
|SkipFirewallConfiguration |Bool，默认值为 false | 不允许 |指定是否需要由系统设置防火墙设置。 仅当使用 Windows 防火墙时适用。 如果使用第三方防火墙，则必须打开端口以供系统和应用程序使用 |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|提供程序 |string，默认值为“DSTS” |静态|要启用的令牌验证提供程序的逗号分隔列表（有效的提供程序是：DSTS、AAD）。 目前只能在任何时候启用单个提供程序。 |

## <a name="traceetw"></a>Trace/Etw
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|级别 |Int，默认值为 4 | 动态 |跟踪 etw 级别可以采用值 1、2、3、4。 必须使跟踪级别保持在 4 才可受到支持 |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | 时间以秒为单位，默认值为 0.015 | 静态 | 指定以秒为单位的时间范围。 确定接收到操作后到发送回确认之前，复制器等待的时间。 在该时间段期间接收的其他操作将通过一条消息发送回其确认 -> 减少网络流量，同时可能降低复制器的吞吐量。 |
|MaxCopyQueueSize |Uint，默认值为 16384 | 静态 |这是用于定义队列初始大小的最大值，该队列用于维护复制操作。 请注意，它必须是 2 的幂。 如果在运行时该队列增长到此大小，将限制主复制器和辅助复制器之间的操作。 |
|MaxPrimaryReplicationQueueMemorySize |Uint，默认值为 0 | 静态 |这是主复制队列的最大值（以字节为单位）。 |
|MaxPrimaryReplicationQueueSize |Uint，默认值为 8192 | 静态 |这是主复制队列中可以存在的最大操作数量。 请注意，它必须是 2 的幂。 |
|MaxReplicationMessageSize |Uint，默认值为 52428800 | 静态 | 复制操作的最大消息大小。 默认值为 50MB。 |
|MaxSecondaryReplicationQueueMemorySize |Uint，默认值为 0 | 静态 |这是辅助复制队列的最大值（以字节为单位）。 |
|MaxSecondaryReplicationQueueSize |Uint，默认值为 16384 | 静态 |这是辅助复制队列中可以存在的最大操作数量。 请注意，它必须是 2 的幂。 |
|ReplicatorAddress |string，默认值为“localhost:0” | 静态 | 采用字符串形式 -'IP:Port' 的终结点，Windows Fabric 复制器将其用于与其他副本建立连接以发送/接收操作。 |

## <a name="transport"></a>传输
| **Parameter** | **允许的值** |**升级策略** |**指导或简短说明** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|时间跨度，默认值是 Common::TimeSpan::FromSeconds(60)|静态|指定以秒为单位的时间范围。 正在传入和正在接收的端（包括安全模式下的安全协商）上连接设置的超时时间 |
|ResolveOption|string，默认值为 L"unspecified"|静态|确定解析 FQDN 的方法。  有效值为 "unspecified/ipv4/ipv6"。 |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool，默认值为 true |静态| 基于目标状态文件的自动轮训和升级操作。 |
|MinReplicaSetSize |Int，默认值为 0 |静态 |UpgradeOrchestrationService 的 MinReplicaSetSize。
|PlacementConstraints | string，默认值为“” |静态| UpgradeOrchestrationService 的 PlacementConstraints。 |
|QuorumLossWaitDuration | 以秒为单位的时间，默认值为 MaxValue |静态| 指定以秒为单位的时间范围。 UpgradeOrchestrationService 的 QuorumLossWaitDuration。 |
|ReplicaRestartWaitDuration | 以秒为单位的时间，默认值为 60 分钟|静态| 指定以秒为单位的时间范围。 UpgradeOrchestrationService 的 ReplicaRestartWaitDuration。 |
|StandByReplicaKeepDuration | 以秒为单位的时间，默认值为 60*24*7 分钟 |静态| 指定以秒为单位的时间范围。 UpgradeOrchestrationService 的 StandByReplicaKeepDuration。 |
|TargetReplicaSetSize |Int，默认值为 0 |静态 |UpgradeOrchestrationService 的 TargetReplicaSetSize。 |
|UpgradeApprovalRequired | Bool，默认值为 false | 静态|此设置可让升级代码需要管理员批准才能继续操作。 |

## <a name="upgradeservice"></a>UpgradeService
| **Parameter** | **允许的值** | **升级策略** | **指导或简短说明** |
| --- | --- | --- | --- |
|BaseUrl | string，默认值为“” |静态|UpgradeService 的 BaseUrl。 |
|ClusterId | string，默认值为“” |静态|UpgradeService 的 ClusterId。 |
|CoordinatorType | string，默认值为“WUTest”|不允许|UpgradeService 的 CoordinatorType。 |
|MinReplicaSetSize | Int，默认值为 2 |不允许| UpgradeService 的 MinReplicaSetSize。 |
|OnlyBaseUpgrade | Bool，默认值为 false |动态|UpgradeService 的 OnlyBaseUpgrade。 |
|PlacementConstraints |string，默认值为“” |不允许|升级服务的 PlacementConstraints。 |
|TargetReplicaSetSize | Int，默认值为 3 |不允许| UpgradeService 的 TargetReplicaSetSize。 |
|TestCabFolder | string，默认值为“” |静态| UpgradeService 的 TestCabFolder。 |
|X509FindType | string，默认值为“”|动态| UpgradeService 的 X509FindType。 |
|X509FindValue | string，默认值为“” |动态| UpgradeService 的 X509FindValue。 |
|X509SecondaryFindValue | string，默认值为“” |动态| UpgradeService 的 X509SecondaryFindValue。 |
|X509StoreLocation | string，默认值为“” |动态| UpgradeService 的 X509StoreLocation。 |
|X509StoreName | string，默认值为“My”|动态|UpgradeService 的 X509StoreName。 |

## <a name="next-steps"></a>后续步骤
有关群集管理的详细信息，请阅读以下文章：

[在 Azure 群集中添加、滚动更新和删除证书](service-fabric-cluster-security-update-certs-azure.md) 

