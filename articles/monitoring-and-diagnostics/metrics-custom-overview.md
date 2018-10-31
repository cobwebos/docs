---
title: Azure Monitor 中的自定义指标
description: 了解 Azure Monitor 中的自定义指标及其建模方式。
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344738"
---
# <a name="custom-metrics-in-azure-monitor"></a>Azure Monitor 中的自定义指标

在 Azure 中部署资源和应用程序时，需要开始收集遥测数据，以洞察它们的性能和运行状况。 Azure 提供了一些现成的指标供你在部署资源时使用。 这些指标称为标准指标或平台指标。 但是，这些指标在性质上有限制。 可能需要收集一些自定义性能指标或特定于业务的指标才能提供更深入的见解。
可以通过应用程序遥测、Azure 资源上运行的代理甚至从外到内的监视系统收集这些“自定义”指标，然后将其直接提交给 Azure Monitor。 发布到 Azure Monitor 之后，可以连同 Azure 发出的标准指标一起浏览、查询 Azure 资源和应用程序的自定义指标，并针对其发出警报。

## <a name="send-custom-metrics"></a>发送自定义指标
可以通过多种方法将自定义指标发送到 Azure Monitor。
- 使用 Application Insights SDK 检测应用程序并将自定义遥测数据发送到 Azure Monitor 
- 在 [Azure VM](metrics-store-custom-guestos-resource-manager-vm.md)、[虚拟机规模集](metrics-store-custom-guestos-resource-manager-vmss.md)、[经典 VM](metrics-store-custom-guestos-classic-vm.md) 或[经典云服务](metrics-store-custom-guestos-classic-cloud-service.md)上安装 Windows 诊断扩展，并将性能计数器发送到 Azure Monitor 
- 在 Azure Linux VM 上安装 [InfluxDB Telegraf 代理](metrics-store-custom-linux-telegraf.md)，并使用 Azure Monitor 输出插件发送指标
- 将自定义指标[直接发送到 Azure Monitor REST API](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

将自定义指标发送到 Azure Monitor 时，报告的每个数据点（或值）必须包括以下信息：

### <a name="authentication"></a>身份验证
若要将自定义指标提交到 Azure Monitor，提交指标的实体需在请求的“Bearer”标头中提供有效的 Azure Active Directory 令牌。 可通过几种支持的方法获取有效的持有者令牌：
1. [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)：为 Azure 资源本身（例如 VM）提供标识。 MSI 旨在授予资源权限来执行特定的操作 - 例如，允许资源发出有关其自身的指标。 可为某个资源（或其 MSI）授予针对另一个资源的“监视指标发布者”权限，使该 MSI 也能发出其他资源的指标。
2. [AAD 服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) - 此处的方案是，可向某个 AAD 应用程序（服务）分配发出有关 Azure 资源的指标的权限。
为了对请求进行身份验证，Azure Monitor 将使用 AAD 公钥来验证应用程序令牌。 现有的“监视指标发布者”角色已拥有此权限，可在 Azure 门户中使用此权限。 可以根据服务主体要发出哪些资源的自定义指标，在所需的范围（订阅、资源组或特定资源）为服务主体授予“监视指标发布者”角色。

> [!NOTE]
> 请求用于发出自定义指标的 AAD 令牌时，请确保请求该令牌的受众/资源是 https://monitoring.azure.com/（请务必包含尾部的“/”）

### <a name="subject"></a>主题
此属性捕获自定义指标报告的 Azure 资源 ID。 将在 API 调用的 URL 中为此信息编码。 每个 API 只能提交单个 Azure 资源的指标值。

> [!NOTE]
> 无法针对资源组或订阅的资源 ID 发出自定义指标。
>
>

### <a name="region"></a>区域
此属性捕获针对其发出指标的资源所部署到的 Azure 区域。 必须将指标发出到资源部署区域所在的同一个 Azure Monitor 区域终结点。 例如，部署在美国西部的 VM 的自定义指标必须发送到美国西部区域 Azure Monitor 终结点。 区域信息也在 API 调用的 URL 中编码。

> [!NOTE]
> 在公共预览期，自定义指标只在一部分 Azure 区域中可用。 本文稍后的部分提供了受支持区域的列表。
>
>

### <a name="timestamp"></a>Timestamp
发送到 Azure Monitor 的每个数据点必须带有时间戳标记。 此时间戳捕获测量/收集指标值的日期时间。 Azure Monitor 接受带有过去最多 20 分钟和将来最多 5 分钟时间戳的指标数据。

### <a name="namespace"></a>命名空间
命名空间可将类似的指标分类或分组到一起。 使用命名空间能够在可收集不同见解或性能指标的指标组之间实现隔离。 例如，可以使用一个名为 *ContosoMemoryMetrics* 的命名空间来跟踪用于分析应用的内存用量指标，并使用名为 *ContosoAppTransaction* 的另一个命名空间来跟踪有关应用程序中用户事务的所有指标。

### <a name="name"></a>名称
正在报告的指标的名称。 通常，该名称具有足够的自述性，可帮助识别所要测量的指标。 例如，用于测量给定 VM 上所用内存字节数的指标可以使用类似于“已使用内存字节数”的名称。

### <a name="dimension-keys"></a>维度键
维度是一个键/值对，帮助描述有关收集的指标的附加特征。 使用附加特征可以收集用于提供更深入见解的指标的详细信息。 例如，“已使用内存字节数”指标可以包含名为“进程”的维度键，用于捕获 VM 上的每个进程消耗的内存字节数。 这样，便可以筛选指标，以查看特定的进程使用了多少内存，或者识别内存用量最高的 5 个进程。
每个自定义指标最多可以包含 10 个维度。

### <a name="dimension-values"></a>维度值
报告指标数据点时，所报告的指标的每个维度键都有一个对应的维度值。 例如，若要报告 VM 上 ContosoApp 使用的内存：

* 指标名称是“已使用内存字节数”
* 维度键是“进程”
* 维度值是“ContosoApp.exe”

发布指标值时，只能为每个维度键指定一个维度值。 如果收集 VM 上多个进程的相同“内存使用率”指标，则可以报告该时间戳的多个指标值。 每个指标值将为“进程”维度键指定不同的维度值。

### <a name="metric-values"></a>指标值
Azure Monitor 以一分钟粒度间隔存储所有指标。 我们知道，在给定的分钟内，某个指标可能需要多次采样（例如 “CPU 利用率”），或者需要针对多个离散事件进行测量（例如 “登录事务延迟”）。 若要在 Azure Monitor 中限制发出和支付的原始值数目，可在本地预先聚合并发出值：

* 最小值：在给定的分钟内从所有样本/测量值中观测到的最小值
* 最大值：在给定的分钟内从所有样本/测量值中观测到的最大值
* 总和：在给定的分钟内从所有样本/测量值中观测到的所有值的总和
* 计数：在给定的分钟内创建的样本/测量值数目

例如，如果在给定的分钟内，应用中出现 4 个登录事务，为每个事务测量到的延迟为：

|事务 1|事务 2|事务 3|事务 4|
|---|---|---|---|
|7 毫秒|4 毫秒|13 毫秒|16 毫秒|
|

发布到 Azure Monitor 的最终指标将是：
* 最小值：4
* 最大值：16
* 总和：40
* 计数：4

如果应用程序无法在本地预先聚合，并需要在收集后立即发出每个离散样本或事件，则你可以发出原始测量值。
例如，每当应用中出现登录事务时，就可以向 Azure Monitor 发布包含单个测量值的指标。 因此，对于花费了 12 毫秒的登录事务，指标发布内容将是：
* 最小值：12
* 最大值：12
* 总和：12
* 计数：1

使用此过程可以发出在给定的分钟内，相同指标与维度的组合的多个值。 然后，Azure Monitor 将会提取在给定分钟内发出的所有原始值，并将其聚合在一起。

### <a name="sample-custom-metric-publication"></a>样本自定义指标发布
以下示例在虚拟机的“内存分析”指标命名空间下创建名为“已使用内存字节数”的自定义指标。 该指标包含名为“进程”的单个维度。 对于给定的时间戳，我们将发出两个不同进程的指标值：

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> Application Insights、Windows Azure 诊断扩展和 InfluxData Telegraf 代理已配置为针对正确的区域终结点发出指标值，每次发出都会携带上述所有属性。
>
>

## <a name="custom-metric-definitions"></a>自定义指标的定义
在发出自定义指标之前，无需在 Azure Monitor 中预定义该指标。 由于发布的每个指标数据点包含命名空间、名称和维度信息，首次将自定义指标发送到 Azure Monitor 时，会自动创建指标定义。 然后，可在通过指标定义发出其指标的任何资源上发现此指标定义。

> [!NOTE]
> Azure Monitor 尚不支持为自定义指标定义“单位”。

## <a name="using-custom-metrics"></a>使用自定义指标
将自定义指标提交到 Azure Monitor 之后，可以通过 Azure 门户浏览它们、通过 Azure Monitor REST API 查询它们，或对其创建警报，以便在满足特定的条件时可以收到通知。
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>通过 Azure 门户浏览自定义指标
1.  转到 [Azure 门户](https://portal.azure.com)
2.  选择“监视”边栏选项卡
3.  单击“指标”
4.  选择已针对其发出自定义指标的资源
5.  选择自定义指标的指标命名空间
6.  选择自定义指标

## <a name="supported-regions"></a>支持的区域
在公共预览期，发布自定义指标的功能只在一部分 Azure 区域中可用。 这意味着，只能发布某个受支持区域中的资源的指标。 下表列出了自定义指标支持的 Azure 区域，以及这些区域中的资源的指标应发布到的相应终结点。

|Azure 区域|区域终结点前缀|
|---|---|
|美国东部|https://eastus.monitoring.azure.com/|
|美国中南部|https://southcentralus.monitoring.azure.com/|
|美国中西部|https://westcentralus.monitoring.azure.com/|
|美国西部 2|https://westus2.monitoring.azure.com/|
|东南亚|https://southeastasia.monitoring.azure.com/|
|北欧|https://northeurope.monitoring.azure.com/|
|西欧|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>配额和限制
Azure Monitor 针对自定义指标实施以下用量限制。

|类别|限制|
|---|---|
|活动的时序/订阅/区域|50,000|
|每个指标的维度键数|10|
|指标命名空间、指标名称、维度键和维度值的字符串长度|256 个字符|
活动的时序定义为包含过去 12 小时内发布的指标值的指标、维度键、维度值的任意唯一组合。

## <a name="next-steps"></a>后续步骤
在不同的服务中使用自定义指标 
 - [虚拟机](metrics-store-custom-guestos-resource-manager-vm.md)
 - [虚拟机规模集](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [虚拟机（经典）](metrics-store-custom-guestos-classic-vm.md)
 - [使用 Telegraf 代理的 Linux 虚拟机](metrics-store-custom-linux-telegraf.md)
 - [REST API](metrics-store-custom-rest-api.md)
 - [云服务（经典）](metrics-store-custom-guestos-classic-cloud-service.md)
 