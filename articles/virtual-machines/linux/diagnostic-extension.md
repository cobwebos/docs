---
title: Azure 计算 - Linux 诊断扩展 |Microsoft Docs
description: 如何配置 Azure Linux 诊断扩展 (LAD)，以收集 Azure 中运行的 Linux VM 的指标和日志事件。
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: agaiha
ms.openlocfilehash: 84be206ef02cca9cc321e9764c016ca79816edf5
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>使用 Linux 诊断扩展监视指标和日志

本文档介绍新的 Linux 诊断扩展 3.0 版。

> [!IMPORTANT]
> 有关 2.3 版和更早版本，请参阅[此文档](./classic/diagnostic-extension-v2.md)。

## <a name="introduction"></a>介绍

Linux 诊断扩展可帮助用户监视 Microsoft Azure 上运行的 Linux VM 的运行状况。 它具有以下功能：

* 从 VM 收集系统性能指标，并存储在指定存储帐户中的特定表中。
* 从 syslog 检索日志事件，并存储在指定存储帐户中的特定表中。
* 让用户能够自定义收集并上传的数据指标。
* 让用户能够自定义要收集和上传的事件的 syslog 辅助参数和严重性级别。
* 让用户能够将指定日志文件上传到指定的存储表。
* 支持将指标和日志事件发送到指定存储帐户中的任意 EventHub 终结点和 JSON 格式的 blob。

此扩展适用于这两种 Azure 部署模型。

## <a name="installing-the-extension-in-your-vm"></a>在 VM 中安装扩展

可以使用 Azure PowerShell cmdlet、Azure CLI 脚本或 Azure 部署模板启用此扩展。 有关详细信息，请参阅[扩展功能](./extensions-features.md)。

Azure 门户不能用于启用或配置 LAD 3.0。 相反，它可用于安装并配置 2.3 版。 Azure 门户图形和警报可处理来自两个扩展版本的数据。

这些安装说明和[可下载示例配置](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)会将 LAD 3.0 配置为：

* 捕获并存储 LAD 2.3 提供的指标；
* 捕获一组有用的文件系统指标，此为 LAD 3.0 新增功能；
* 捕获 LAD 2.3 允许的默认 syslog 收集；
* 允许 Azure 门户体验，以便对 VM 指标进行制图以及就其发送警报。

可下载配置只是一个示例；请对其进行修改以适应你的需求。

### <a name="prerequisites"></a>先决条件

* Azure Linux 代理 2.2.0 版或更高版本。 大部分 Azure VM Linux 库映像包含 2.2.7 版或更高版本。 运行 `/usr/sbin/waagent -version` 以确认 VM 上安装的版本。 如果 VM 正在运行较早版本的来宾代理，请按照[以下说明](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)将其更新。
* **Azure CLI**。 在计算机上[设置 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 环境。
* wget 命令（如尚无，请运行 `sudo apt-get install wget`）。
* 现有 Azure 订阅以及其中用于存储数据的现有存储帐户。

### <a name="sample-installation"></a>示例安装

在前三行填写正确的参数，然后以 root 身份执行此脚本：

```bash
# Set your Azure VM diagnostic parameters correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 9999-12-31T23:59Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

示例配置的 URL 及其内容可能会有所更改。 下载门户设置 JSON 文件的副本，并根据需要进行自定义。 构造的任何模板或自动化应使用自己的副本，而不是每次下载该 URL。

### <a name="updating-the-extension-settings"></a>更新扩展设置

更改“受保护”或“公开”设置后，通过运行相同的命令将其部署到 VM。 如果设置中有任何变化，更新的设置将发送到扩展。 LAD 将重载配置并自行重启。

### <a name="migration-from-previous-versions-of-the-extension"></a>从以前版本的扩展迁移

扩展的最新版本是 3.0。 任何旧版本 (2.x) 都已弃用，可能会在 2018 年 7 月 31 日或之后取消发布。

> [!IMPORTANT]
> 此扩展引入了对扩展配置的重大更改。 其中一项更改是为了提高扩展的安全性；因此，无法维持与 2.x 版本的向后兼容性。 此外，此扩展的扩展发布服务器与 2.x 版本的发布服务器不同。
>
> 若要从扩展的 2.x 版迁移到此新版本，必须卸载旧扩展（在旧发布服务器名称下），然后安装扩展的 3.0 版。

建议：

* 启用自动次要版本升级后再安装扩展。
  * 在经典的部署模型 VM 上，如果要通过 Azure XPLAT CLI 或Powershell 安装扩展，请指定“3.*”作为版本。
  * 在 Azure 资源管理器部署模型 VM 上，在 VM 部署模板中加入“"autoUpgradeMinorVersion": true”。
* 为 LAD 3.0 使用新的/不同的存储帐户。 LAD 2.3 和 LAD 3.0 之间存在几个小的不兼容性，使得共享帐户变得麻烦：
  * LAD 3.0 将 syslog 事件存储在不同名称的表中。
  * LAD 3.0 中 `builtin` 指标的 counterSpecifier 字符串不同。

## <a name="protected-settings"></a>受保护的设置

此组配置信息不应公开的敏感信息，例如存储凭据。 扩展以加密形式传输和存储这些设置。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

名称 | 值
---- | -----
storageAccountName | 扩展写入数据的存储帐户的名称。
storageAccountEndPoint | （可选）标识存储帐户所在云的终结点。 如果缺少此设置，则 LAD 默认为 Azure 公有云`https://core.windows.net`。 若要使用 Azure Germany、Azure 政府或 Azure China 中的存储帐户，请相应地设置此值。
storageAccountSasToken | Blob 服务和表服务的[帐户 SAS 令牌](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) (`ss='bt'`)，适用于容器和对象 (`srt='co'`)，用于授予添加、创建、列出、更新和写入权限 (`sp='acluw'`)。 请勿使用前导问号 (?)。
mdsdHttpProxy | （可选）允许扩展连接到指定存储帐户和终结点所需的 HTTP 代理信息。
sinksConfig | （可选）可将指标和事件传递到的替换目标的详细信息。 扩展所支持的每个数据接收器的具体详细信息将在下面各节中介绍。


> [!NOTE]
> 使用 Azure 部署模板部署扩展时，必须提前创建好存储帐户和 SAS 令牌并将其传递给模板。 无法在单个模板中部署 VM、存储帐户和配置扩展。 当前不支持在模板中创建 SAS 令牌。

可通过 Azure 门户轻松构造所需的 SAS 令牌。

1. 选择想要扩展写入的通用存储帐户
1. 在左侧菜单的“设置”部分选择“共享访问签名”
1. 如上所述设置相应部分
1. 单击“生成 SAS”按钮。

![图像](./media/diagnostic-extension/make_sas.png)

将生成的 SAS 复制到 storageAccountSasToken 字段中；删除前导问号（“?”）。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

此可选部分用于定义扩展要将其收集到的信息发送到的附加目的地。 “sink”数组包含每个附加数据接收器的对象。 “type”属性确定对象中的其他属性。

元素 | 值
------- | -----
名称 | 在扩展配置中其他位置用于引用此接收器的字符串。
type | 要定义的接收器的类型。 确定此类型实例中的其他值（如果有）。

Linux 诊断扩展 3.0 版支持两种接收器类型：EventHub 和 JsonBlob。

#### <a name="the-eventhub-sink"></a>EventHub 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

“sasURL”条目包含应将数据发布到的事件中心的完整 URL，包括 SAS 令牌。 LAD 要求 SAS 命名启用发送声明的策略。 示例：

* 创建名为 `contosohub` 的事件中心命名空间
* 在名为 `syslogmsgs` 的命名空间中创建事件中心
* 在名为 `writer` 的事件中心上创建启用发送声明的共享访问策略

如果创建在 UTC 2018 年 1 月 1 日午夜之前有效的 SAS，sasURL 值可能为：

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

有关为事件中心生成 SAS 令牌的详细情况，请参阅[此网页](../../event-hubs/event-hubs-authentication-and-security-model-overview.md)。

#### <a name="the-jsonblob-sink"></a>JsonBlob 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

定向到 JsonBlob 接收器的数据将存储在 Azure 存储中的 blob 内。 每个 LAD 实例每小时为每个接收器名称创建一个 blob。 每个 blob 总是包含一个语法上有效的 JSON 对象数组。 新条目以基元形式添加到数组。 blob 存储在与接收器同名的容器中。 用于 blob 容器名称的 Azure 存储规则也适用于 JsonBlob 接收器的名称：长度介于 3 到 63 个字符之间，可使用小写字母数字 ASCII 字符或短划线。

## <a name="public-settings"></a>公用设置

此结构包含多个设置块，这些块控制扩展收集的信息。 每个设置都是可选的。 如果指定 `ladCfg`，则还必须指定 `StorageAccount`。

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

元素 | 值
------- | -----
StorageAccount | 扩展写入数据的存储帐户的名称。 必须与[受保护的设置](#protected-settings)中指定的名称相同。
mdsdHttpProxy | （可选）与[受保护的设置](#protected-settings)中的相同。 如果设置，则专用值将重写公用值。 将包含机密（如密码）的代理设置放在[受保护的设置](#protected-settings)中。

将在下面各节中详细介绍剩余的元素。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

此可选结构控制指标和日志的收集，以传递到 Azure Metrics 服务和其他数据接收器。 必须指定 `performanceCounters` 和/或 `syslogEvents`。 必须指定 `metrics` 结构。

元素 | 值
------- | -----
eventVolume | （可选）控制在存储表中创建的分区数。 必须是 `"Large"`、`"Medium"` 或 `"Small"`。 如果未指定，默认值为 `"Medium"`。
sampleRateInSeconds | （可选）两次收集原始（未聚合）指标之间的默认时间间隔。 支持的最小采样率为 15 秒。 如果未指定，默认值为 `15`。

#### <a name="metrics"></a>指标

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

元素 | 值
------- | -----
resourceId | VM 或 VM 所属虚拟机规模集的 Azure 资源管理器资源 ID。 如果配置中使用了任何 JsonBlob 接收器，也必须指定此设置。
scheduledTransferPeriod | 计算聚合指标并将转移到 Azure Metrics 的频率，以 IS 8601 时间间隔形式表示。 最小传输周期为 60 秒，即 PT1M。 必须指定至少一个 scheduledTransferPeriod。

performanceCounters 节中指定的指标样本每 15 秒收集一次，或者按计数器明确定义的采样率进行收集。 如果出现多个 scheduledTransferPeriod 频率（如示例所示），则每个聚合都将独立计算。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

此可选部分控制指标的收集。 每个 [scheduledTransferPeriod](#metrics) 的原始样本聚合产生以下值：

* 平均值
* minimum
* maximum
* 上一次收集的值
* 用于计算聚合的原始样本数

元素 | 值
------- | -----
sinks | （可选）LAD 将聚合指标结果发送到的接收器的名称的逗号分隔列表。 所有聚合指标都将发布到列出的每个接收器。 请参阅 [sinksConfig](#sinksconfig)。 示例：`"EHsink1, myjsonsink"`。
type | 标识指标的实际提供程序。
class | 与“counter”一起标识提供程序的命名空间中的特定指标。
counter | 与“class”一起标识提供程序的命名空间中的特定指标。
counterSpecifier | 标识 Azure Metrics 命名空间中的特定指标。
条件 | （可选）选择指标适用对象的特定实例，或选择该对象所有实例的聚合。 有关详细信息，请参阅 [`builtin` 指标定义](#metrics-supported-by-builtin)。
sampleRate | IS 8601 时间间隔，用于设置收集此指标原始样本的速率。 如果未设置，则收集时间间隔由 [sampleRateInSeconds](#ladcfg) 的值设置。 支持的最短采样率为 15 秒 (PT15S)。
单位 | 应为以下字符串之一：“Count”、“Bytes”、“Seconds”、“Percent”、“CountPerSecond”、“BytesPerSecond”、“Millisecond”。 定义指标的单位。 所收集数据的使用者会预期收集到的数据值与此单位匹配。 LAD 将忽略此字段。
displayName | Azure Metrics 中要附加到此数据的标签（使用由相关区域设置指定的语言）。 LAD 将忽略此字段。

counterSpecifier 是一个任意标识符。 Azure 门户绘图和警报功能等指标使用者使用 counterSpecifier 作为标识指标或指标实例的“关键字”。 对于 `builtin` 指标，建议使用以 `/builtin/` 开头的 counterSpecifier 值。 如果要收集指标的特定实例，建议将该实例的标识符附加到 counterSpecifier 值。 下面是一些示例：

* `/builtin/Processor/PercentIdleTime` - 所有 vCPU 的平均空闲时间
* `/builtin/Disk/FreeSpace(/mnt)` - /mnt 文件系统的可用空间
* `/builtin/Disk/FreeSpace` - 已装入的所有文件系统的平均可用空间

LAD 和 Azure 门户都不需要 counterSpecifier 值匹配任何模式。 请以相同的模式构造各个 counterSpecifier 值。

指定 `performanceCounters` 时，LAD 始终将数据写入 Azure 存储中的表。 可将相同的数据写入 JSON blob 和/或事件中心，但不能禁止将数据存储到表中。 配置为使用相同存储帐户名称和终结点的所诊断扩展有实例，其指标和日志会添加到同一个表中。 如果有过多 VM 写入同一表分区，则 Azure 可能限制写入该分区。 eventVolume 设置会使条目分散在 1 个 (Small)、10 个 (Medium) 或 100 个 (Large) 分区中。 通常，“Medium”足以确保流量不被限制。 Azure 门户的 Azure Metrics 功能使用此表中的数据来生成图形或触发警报。 表名是这些字符串的串联：

* `WADMetrics`
* 表中存储的聚合值的“scheduledTransferPeriod”
* `P10DV2S`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `WADMetricsPT1HP10DV2S20170410` 和 `WADMetricsPT1MP10DV2S20170609`。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

此可选部分控制 syslog 中日志事件的收集。 如果省略此部分，则不会捕获 syslog 事件。

syslogEventConfiguration 收集将为相关的每个 syslog 辅助参数创建一个条目。 如果特定辅助参数的 minSeverity 为“NONE”，或者该辅助参数并未出现在元素中，则不会捕获该辅助参数下的任何事件。

元素 | 值
------- | -----
sinks | 要将单个日志事件发布到的接收器的名称的逗号分隔列表。 与 syslogEventConfiguration 中的限制匹配的所有日志事件都会发布到列出的每个接收器。 示例：“EHforsyslog”
facilityName | Syslog 辅助参数名称（例如“LOG\_USER”或“LOG\_LOCAL0”）。 有关完整列表，请参阅 [syslog 手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“facility”部分。
minSeverity | Syslog 严重性级别（例如“LOG\_ERR”或“LOG\_INFO”）。 有关完整列表，请参阅 [syslog手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“level”部分。 扩展将捕获发送到该辅助参数的等于或高于指定级别的事件。

指定 `syslogEvents` 时，LAD 始终将数据写入 Azure 存储中的表。 可将相同的数据写入 JSON blob 和/或事件中心，但不能禁止将数据存储到表中。 此表的分区行为与 `performanceCounters` 的描述相同。 表名是这些字符串的串联：

* `LinuxSyslog`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `LinuxSyslog20170410` 和 `LinuxSyslog20170609`。

### <a name="perfcfg"></a>perfCfg

此可选部分控制任意 [OMI](https://github.com/Microsoft/omi) 查询的执行。

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
命名空间 | （可选）应在其中执行查询的 OMI 命名空间。 如果未指定，则默认值为“root/scx”，由 [ System Center 跨平台提供程序](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation)实现。
query | 要执行的 OMI 查询。
表 | （可选）指定存储帐户中的 Azure 存储表（请参阅[受保护的设置](#protected-settings)）。
频率 | （可选）两次执行查询之间的秒数。 默认值为 300（5 分钟）；最小值为 15 秒。
sinks | （可选）应将原始样本指标结果发布到的附加接收器的名称的逗号分隔列表。 扩展或 Azure Metrics 不计算这些原始样本的聚合。

必须指定“表”和/或“接收器”。

### <a name="filelogs"></a>fileLogs

控制日志文件的捕获。 LAD 在文件中写入新的文本行时捕获这些行，并将其写入表行和/或任何指定的接收器（JsonBlob 或 EventHub）。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
file | 要监视和捕获的日志文件的完整路径名。 路径名必须命名单个文件；它不能命名目录，也不能包含通配符。
表 | （可选）指定的存储帐户（在受保护的配置中指定）中的 Azure 存储表，文件“结尾”处的新行将写入此表。
sinks | （可选）日志行发送到的附加接收器的名称的逗号分隔列表。

必须指定“表”和/或“接收器”。

## <a name="metrics-supported-by-the-builtin-provider"></a>内置提供程序支持的指标

内置指标提供程序可提供大量用户最感兴趣的指标。 这些指标分为五个大类：

* 处理器
* 内存
* 网络
* 文件系统
* 磁盘

### <a name="builtin-metrics-for-the-processor-class"></a>处理器类的内置指标

处理器类指标提供有关 VM 中处理器使用情况的信息。 聚合百分比时，结果是所有 CPU 的平均值。 在双 vCPU VM 中，如果一个 vCPU 100% 忙，另一个 vCPU 100% 空闲，则报告的 PercentIdleTime 将是 50。 如果在相同时段内每个 vCPU 都是 50% 忙，则报告的结果也将是 50。 在四 vCPU VM 中，如果一个 vCPU 100% 忙，其他 vCPU 空闲，则报告的 PercentIdleTime 将是 75。

counter | 含义
------- | -------
PercentIdleTime | 聚合窗口期内，处理器执行内核空闲循环的时间所占百分比
PercentProcessorTime | 执行非空闲线程的时间所占百分比
PercentIOWaitTime | 等待 IO 操作完成的时间所占百分比
PercentInterruptTime | 执行硬件/软件中断和 DPC（延迟过程调用）的时间所占百分比
PercentUserTime | 聚合窗口期非空闲时间内，更常处于常规优先级的用户所花费的时间所占百分比
PercentNiceTime | 非空闲时间内，处于降低（良好）优先级的时间所占百分比
PercentPrivilegedTime | 非空闲时间内，处于特权（内核）模式的时间所占百分比

前四个计数器结果之和应为 100%。 后三个计数器结果之和也应为 100%，它们对 PercentProcessorTime、PercentIOWaitTime 和 PercentInterruptTime 之和（非空闲时间）进行了细分。

若要获取跨所有处理器聚合的单个指标，请设置 `"condition": "IsAggregate=TRUE"`。 若要获取特定处理器的指标，如四 vCPU VM 的第二个逻辑处理器，请设置 `"condition": "Name=\\"1\\""`。 逻辑处理器编号处于 `[0..n-1]` 范围内。

### <a name="builtin-metrics-for-the-memory-class"></a>内存类的内置指标

内存类指标提供有关内存使用率、分页和交换的信息。

counter | 含义
------- | -------
AvailableMemory | 可用物理内存 (MB)
PercentAvailableMemory | 可用物理内存占内存总量的百分比
UsedMemory | 已用物理内存 (MB)
PercentUsedMemory | 已用物理内存占内存总量的百分比
PagesPerSec | 总分页（读取/写入）
PagesReadPerSec | 从后备存储（交换文件、程序文件、映射文件等）读取的页面数
PagesWrittenPerSec | 写入后备存储（交换文件、映射文件等）的页面数
AvailableSwap | 未使用的交换空间 (MB)
PercentAvailableSwap | 未使用的交换空间占交换空间总量的百分比
UsedSwap | 已使用的交换空间 (MB)
PercentUsedSwap | 已使用的交换空间占交换空间总量的百分比

此类指标仅有一个实例。 “condition”属性没有有用的设置，应当省略。

### <a name="builtin-metrics-for-the-network-class"></a>网络类的内置指标

网络类指标提供有关自启动以来各个网络接口上网络活动的信息。 LAD 不会公开带宽指标，该指标可从主机指标中检索。

counter | 含义
------- | -------
BytesTransmitted | 自启动以来发送的字节数总计
BytesReceived | 自启动以来接收的字节数总计
BytesTotal | 自启动以来发送或接收的字节数总计
PacketsTransmitted | 自启动以来发送的包数总计
PacketsReceived | 自启动以来接收的包数总计
TotalRxErrors | 自启动以来接收的错误数
TotalTxErrors | 自启动以来发送的错误数
TotalCollisions | 自启动以来网络端口报告的冲突数

 虽然此类拥有实例，但是 LAD 不支持捕获跨所有网络设备聚合的网络指标。 若要获取特定接口（如 eth0）的指标，请设置 `"condition": "InstanceID=\\"eth0\\""`。

### <a name="builtin-metrics-for-the-filesystem-class"></a>文件系统类的内置指标

文件系统类指标提供有关文件系统使用情况的信息。 将报告绝对值和百分比值，因为这些指标将向普通用户（而不是 root 用户）显示。

counter | 含义
------- | -------
FreeSpace | 可用磁盘空间（字节）
UsedSpace | 已用磁盘空间（字节）
PercentFreeSpace | 可用空间百分比
PercentUsedSpace | 已用空间百分比
PercentFreeInodes | 未使用的 Inode 的百分比
PercentUsedInodes | 所有文件系统中已分配的（使用中）inode 的百分比之和
BytesReadPerSecond | 每秒读取的字节数
BytesWrittenPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒读取或写入操作数

可通过设置 `"condition": "IsAggregate=True"`，获取跨所有文件系统的聚合值。 可通过设置 `"condition": 'Name="/mnt"'`，获取已装入的特定文件系统（如“/mnt”）的值。

### <a name="builtin-metrics-for-the-disk-class"></a>磁盘类的内置指标

磁盘类指标提供有关磁盘设备使用情况的信息。 这些统计信息适用于整个驱动器。 如果设备上有多个文件系统，则针对该设备的计数器将有效地跨所有文件系统聚合。

counter | 含义
------- | -------
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒总操作数
AverageReadTime | 每个读取操作的平均秒数
AverageWriteTime | 每个写入操作的平均秒数
AverageTransferTime | 每个操作的平均秒数
AverageDiskQueueLength | 队列中磁盘操作的平均数
ReadBytesPerSecond | 每秒读取的字节数
WriteBytesPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数

可通过设置 `"condition": "IsAggregate=True"`，获取跨所有磁盘的聚合值。 若要获取特定设备（例如 /dev/sdf1）的信息，请设置 `"condition": "Name=\\"/dev/sdf1\\""`。

## <a name="installing-and-configuring-lad-30-via-cli"></a>通过 CLI 安装并配置 LAD 3.0

假设受保护的设置位于 PrivateConfig 文件中，而公用配置信息位于 PublicConfig. json 中，请运行以下命令：

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

该命令假定你使用 Azure CLI 的 Azure 资源管理模式 (arm)。 若要为经典部署模型 (ASM) VM 配置 LAD，请切换到“asm”模式 (`azure config mode asm`)，并在命令中省略资源组名称。 有关详细信息，请参阅[跨平台 CLI 文档](https://docs.microsoft.com/azure/xplat-cli-connect)。

## <a name="an-example-lad-30-configuration"></a>LAD 3.0 配置示例

基于前述定义，下面提供一个包含解释的 LAD 3.0 扩展配置示例。 要将此示例应用于具体情况，应使用自己的存储帐户名称、帐户 SAS 令牌和 EventHubs SAS 令牌。

### <a name="privateconfigjson"></a>PrivateConfig.json

这些专用设置将配置以下几项内容：

* 存储帐户
* 匹配的帐户 SAS 令牌
* 几个接收器（有 SAS 令牌的 JsonBlob 或 EventHubs）

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

这些公用设置将使 LAD 执行以下操作：

* 将 percent-processor-time 和 used-disk-space 指标上传到 `WADMetrics*` 表
* 将 syslog 辅助参数“user”和严重性“info”之下的消息上传到 `LinuxSyslog*` 表
* 将原始 OMI 查询结果（PercentProcessorTime 和 PercentIdleTime）上传到指定的 `LinuxCPU` 表
* 将文件 `/var/log/myladtestlog` 中的追加行上传到 `MyLadTestLog` 表

无论如何，数据都还会上传到以下位置：

* Azure Blob 存储（容器名称在 JsonBlob 接收器中定义）
* EventHubs 终结点（在 EventHubs 接收器中指定）

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

配置中的 `resourceId` 必须与 VM 或虚拟机规模集中的相匹配。

* Azure 平台指标绘图和警报了解你所用 VM 的 resourceId。 它会预期能够使用 resourceId 查找关键字找到该 VM 的数据。
* 如果使用 Azure 自动缩放，则自动缩放配置中的 resourceId 必须与 LAD 使用的 resourceId 相匹配。
* LAD 所编写的 JsonBlob 名称包含 resourceId。

## <a name="view-your-data"></a>查看数据

使用 Azure 门户查看性能数据或设置警报：

![图像](./media/diagnostic-extension/graph_metrics.png)

`performanceCounters` 数据始终存储在 Azure 存储表中。 Azure 存储 API 适用于多种语言和平台。

发送到 JsonBlob 接收器的数据存储在[受保护的设置](#protected-settings)中指定的存储帐户中的 blob 内。 可使用任意 Azure Blob 存储 API 使用这些 blob 数据。

此外，可使用这些 UI 工具来访问 Azure 存储中的数据：

* Visual Studio 服务器资源管理器。
* [Microsoft Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/ "Azure 存储资源管理器")。

这是 Microsoft Azure 存储资源管理器会话的快照，它显示了测试 VM 上正确配置的 LAD 3.0 扩展生成的 Azure 存储表和容器。 此图与[示例 LAD 3.0 配置](#an-example-lad-30-configuration)不完全匹配。

![图像](./media/diagnostic-extension/stg_explorer.png)

请参阅相关 [ EventHubs 文档](../../event-hubs/event-hubs-what-is-event-hubs.md)，了解如何使用发布到 EventHubs 终结点的消息。

## <a name="next-steps"></a>后续步骤

* 在 [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) 中为收集的指标创建指标警报。
* 为指标创建[监控图表](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。
* 了解如何使用指标[创建虚拟机规模集](/azure/virtual-machines/linux/tutorial-create-vmss)以控制自动缩放。
