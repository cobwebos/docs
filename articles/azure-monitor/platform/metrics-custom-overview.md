---
title: Azure Monitor 中的自定义指标（预览）
description: 了解 Azure Monitor 中的自定义指标及其建模方式。
author: ancav
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2020
ms.subservice: metrics
ms.openlocfilehash: 4891d7272516caf4944219907d81ee4fb89e0189
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837305"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor 中的自定义指标（预览）

在 Azure 中部署资源和应用程序时，需要开始收集遥测数据，以洞察它们的性能和运行状况。 Azure 提供一些现成的指标。 这些指标称为[标准或平台](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)。 但是，它们在性质上有限制。 

可能需要收集一些自定义性能指标或特定于业务的指标才能提供更深入的见解。 可以通过应用程序遥测、Azure 资源上运行的代理甚至从外到内的监视系统收集这些**自定义**指标，然后将其直接提交给 Azure Monitor。 发布到 Azure Monitor 之后，可以连同 Azure 发出的标准指标一起浏览、查询 Azure 资源和应用程序的自定义指标，并针对其发出警报。

Azure Monitor 自定义指标在公共预览版中是最新的。 

## <a name="methods-to-send-custom-metrics"></a>用于发送自定义指标的方法

可以通过多种方法将自定义指标发送到 Azure Monitor：
- 使用 Azure Application Insights SDK 检测应用程序并将自定义遥测数据发送到 Azure Monitor。 
- 在 [Azure VM](collect-custom-metrics-guestos-resource-manager-vm.md)、[虚拟机规模集](collect-custom-metrics-guestos-resource-manager-vmss.md)、[经典 VM](collect-custom-metrics-guestos-vm-classic.md) 或[经典云服务](collect-custom-metrics-guestos-vm-cloud-service-classic.md)上安装 Windows Azure 诊断 (WAD) 扩展，并将性能计数器发送到 Azure Monitor。 
- 在 Azure Linux VM 上安装 [InfluxData Telegraf 代理](collect-custom-metrics-linux-telegraf.md)，并使用 Azure Monitor 输出插件发送指标。
- 将自定义度量值[直接发送到 Azure Monitor REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md) `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`。

## <a name="pricing-model-and-rentention"></a>定价模型和保留

有关何时为自定义指标和指标查询启用计费的详细信息，请查看[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)。 此页提供了所有指标的特定价格详细信息，包括自定义指标和指标查询。 总而言之，将标准指标（平台指标）引入 Azure Monitor 指标存储不会产生费用，但自定义指标在输入正式上市时产生成本。 指标 API 查询会产生成本。

自定义指标的保留[时间与平台指标的时间相同](data-platform-metrics.md#retention-of-metrics)。 

> [!NOTE]  
> 通过 Application Insights SDK 发送给 Azure Monitor 的指标按引入日志数据计费。 仅当已选择 "[对自定义指标维度启用警报](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation)" Application Insights 功能时，它们才会产生额外的指标费用。 此复选框使用自定义指标 API 将数据发送到 Azure Monitor 指标数据库，以允许更复杂的警报。  详细了解[你所在地区](https://azure.microsoft.com/pricing/details/monitor/)的[Application Insights 定价模型](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model)和价格。


## <a name="how-to-send-custom-metrics"></a>如何发送自定义指标

将自定义指标发送到 Azure Monitor 时，报告的每个数据点或值必须包括以下信息。

### <a name="authentication"></a>身份验证
若要将自定义指标提交到 Azure Monitor，提交指标的实体需在请求的 **Bearer** 标头中提供有效的 Azure Active Directory (Azure AD) 令牌。 可通过几种支持的方法获取有效的持有者令牌：
1. [Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 为 Azure 资源本身（例如 VM）提供一个标识。 托管服务标识 (MSI) 旨在授予资源权限来执行特定的操作。 例如，允许资源发出有关其自身的指标。 可为某个资源或其 MSI 授予针对另一个资源的“监视指标发布者”权限。**** 获取此权限后，该 MSI 也能发出其他资源的指标。
2. [Azure AD 服务主体](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)。 在此方案中，可向某个 Azure AD 应用程序或服务分配发出有关 Azure 资源的指标的权限。
为了对请求进行身份验证，Azure Monitor 将使用 Azure AD 公钥来验证应用程序令牌。 现有的“监视指标发布者”角色已拥有此权限。**** 可在 Azure 门户中使用此权限。 可以根据服务主体要发出哪些资源的自定义指标，在所需的范围为该服务主体授予“监视指标发布者”角色。**** 范围的示例包括订阅、资源组或特定资源。

> [!TIP]  
> 请求用于发出自定义指标的 Azure AD 令牌时，请确保请求该令牌的受众或资源是 `https://monitoring.azure.com/`。 请务必包含尾部的“/”。

### <a name="subject"></a>使用者
此属性捕获自定义指标报告的 Azure 资源 ID。 将在 API 调用的 URL 中为此信息编码。 每个 API 只能提交单个 Azure 资源的指标值。

> [!NOTE]  
> 无法针对资源组或订阅的资源 ID 发出自定义指标。


### <a name="region"></a>区域
此属性捕获针对其发出指标的资源所部署到的 Azure 区域。 必须将指标发出到资源部署区域所在的同一个 Azure Monitor 区域终结点。 例如，部署在美国西部的 VM 的自定义指标必须发送到美国西部区域 Azure Monitor 终结点。 区域信息也在 API 调用的 URL 中编码。

> [!NOTE]  
> 在公共预览期，自定义指标只在一部分 Azure 区域中可用。 本文稍后的部分提供了受支持区域的列表。
>
>

### <a name="timestamp"></a>Timestamp
发送到 Azure Monitor 的每个数据点必须带有时间戳标记。 此时间戳捕获测量或收集指标值的日期时间。 Azure Monitor 接受带有过去最多 20 分钟和将来最多 5 分钟时间戳的指标数据。 时间戳必须采用 ISO 8601 格式。

### <a name="namespace"></a>命名空间
命名空间可将类似的指标分类或分组到一起。 使用命名空间能够在可收集不同见解或性能指标的指标组之间实现隔离。 例如，你可能有一个名为**contosomemorymetrics**的命名空间，用于跟踪分析你的应用程序的内存使用度量值。 另一个名为**contosoapptransaction**的命名空间可能会跟踪有关应用程序中的用户事务的所有指标。

### <a name="name"></a>名称
“名称”是正在报告的指标的名称。**** 通常，该名称具有足够的自述性，可帮助识别所要测量的指标。 以测量给定 VM 上所用内存字节数的指标为例， 该指标可以使用类似于“已使用内存字节数”的名称。****

### <a name="dimension-keys"></a>维度键
维度是一个键/值对，帮助描述有关收集的指标的附加特征。 使用附加特征可以收集用于提供更深入见解的指标的详细信息。 例如，“已使用内存字节数”指标可以包含名为“进程”的维度键，用于捕获 VM 上的每个进程消耗的内存字节数。******** 使用此键可以筛选指标，以查看特定的进程使用了多少内存，或者识别内存用量最高的 5 个进程。
维度是可选的，并非所有指标都可能包含维度。 一个自定义指标最多可以包含 10 个维度。

### <a name="dimension-values"></a>维度值
报告指标数据点时，所报告的指标的每个维度键都有一个对应的维度值。 例如，可以报告 VM 上 ContosoApp 使用的内存：

* 指标名称是“已使用内存字节数”。****
* 维度键是“进程”。****
* 维度值是“ContosoApp.exe”。****

发布指标值时，只能为每个维度键指定一个维度值。 如果收集 VM 上多个进程的相同“内存使用率”指标，则可以报告该时间戳的多个指标值。 每个指标值将为“进程”维度键指定不同的维度值。****
维度是可选的，并非所有指标都可能包含维度。 如果指标帖子定义维度键，则相应的维度值是必需的。

### <a name="metric-values"></a>指标值
Azure Monitor 以一分钟粒度间隔存储所有指标。 我们知道，在给定的分钟内，某个指标可能需要多次采样， 例如 CPU 利用率。 或者，可能需要针对多个离散事件进行测量， 例如登录事务延迟。 若要在 Azure Monitor 中限制发出和支付的原始值数目，可在本地预先聚合并发出值：

* **最小值**：在给定的分钟内从所有样本和测量值中观测到的最小值。
* **最大值**：在给定的分钟内从所有样本和测量值中观测到的最大值。
* **总和**：在给定的分钟内从所有样本和测量值中观测到的所有值的总和。
* **计数**：在给定的分钟内创建的样本和测量值数目。

例如，如果你在给定一分钟内，你的应用程序有四个登录事务，则每个的结果测量的延迟可能如下所示：

|事务 1|事务 2|事务 3|事务 4|
|---|---|---|---|
|7 毫秒|4 毫秒|13 毫秒|16 毫秒|
|

发布到 Azure Monitor 的最终指标如下：
* 最小值：4
* 最大值：16
* 总和：40
* 计数：4

如果应用程序无法在本地预先聚合，并需要在收集后立即发出每个离散样本或事件，则你可以发出原始测量值。 例如，每当应用中出现登录事务时，就可以向 Azure Monitor 发布包含单个测量值的指标。 因此，对于花费了 12 毫秒的登录事务，指标发布内容如下：
* 最小值：12
* 最大值：12
* 总和：12
* 计数：1

使用此过程可以发出在给定的分钟内，相同指标与维度的组合的多个值。 然后，Azure Monitor 将会提取在给定分钟内发出的所有原始值，并将其聚合在一起。

### <a name="sample-custom-metric-publication"></a>样本自定义指标发布
以下示例在虚拟机的“内存分析”指标命名空间下创建名为“已使用内存字节数”的自定义指标。******** 该指标包含名为“进程”的单个维度。**** 对于给定的时间戳，我们将发出两个不同进程的指标值：

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
> Application Insights、诊断扩展和 InfluxData Telegraf 代理已配置为针对正确的区域终结点发出指标值，每次发出都会携带上述所有属性。
>
>

## <a name="custom-metric-definitions"></a>自定义指标的定义
在发出自定义指标之前，无需在 Azure Monitor 中预定义该指标。 发布的每个指标数据点包含命名空间、名称和维度信息。 因此，首次将自定义指标发送到 Azure Monitor 时，会自动创建指标定义。 然后，可在通过指标定义发出其指标的任何资源上发现此指标定义。

> [!NOTE]  
> Azure Monitor 尚不支持定义自定义指标的**单位**。

## <a name="using-custom-metrics"></a>使用自定义指标
将自定义指标提交到 Azure Monitor 之后，可以通过 Azure 门户浏览它们，以及通过 Azure Monitor REST API 查询它们。 还可以对其创建警报，以便在满足特定的条件时收到通知。

> [!NOTE]
> 你需要成为 "读者" 或 "参与者" 角色才能查看自定义指标。

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>通过 Azure 门户浏览自定义指标
1.    转到 [Azure 门户](https://portal.azure.com)。
2.    选择“监视”窗格。****
3.    选择“指标”****。
4.    选择已针对其发出自定义指标的资源。
5.    选择自定义指标的指标命名空间。
6.    选择自定义指标。

## <a name="supported-regions"></a>支持的区域
在公共预览期，发布自定义指标的功能只在一部分 Azure 区域中可用。 此限制意味着，只能发布某个受支持区域中的资源的指标。 下表列出了自定义指标支持的 Azure 区域。 另外还列出了这些区域中的资源的指标应发布到的相应终结点：

|Azure 区域 |区域终结点前缀|
|---|---|
| **美国和加拿大** | |
|美国中西部 | https：\//westcentralus.monitoring.azure.com/ |
|美国西部 2       | https：\//westus2.monitoring.azure.com/ |
|美国中北部 | https：\//northcentralus.monitoring.azure.com
|美国中南部| https：\//southcentralus.monitoring.azure.com/ |
|美国中部      | https：\//centralus.monitoring.azure.com |
|加拿大中部 | https：\//canadacentral.monitoring.azure.comc
|美国东部| https：\//eastus.monitoring.azure.com/ |
| **欧洲** | |
|北欧    | https：\//northeurope.monitoring.azure.com/ |
|西欧     | https：\//westeurope.monitoring.azure.com/ |
|英国南部 | https：\//uksouth.monitoring.azure.com
|法国中部 | https：\//francecentral.monitoring.azure.com |
| **非洲** | |
|南非北部 | https：\//southafricanorth.monitoring.azure.com
| **亚洲** | |
|印度中部 | https：\//centralindia.monitoring.azure.com
|澳大利亚东部 | https：\//australiaeast.monitoring.azure.com
|日本东部 | https：\//japaneast.monitoring.azure.com
|东南亚  | https：\//southeastasia.monitoring.azure.com |
|东亚 | https：\//eastasia.monitoring.azure.com
|韩国中部   | https：\//koreacentral.monitoring.azure.com

## <a name="latency-and-storage-retention"></a>延迟和存储保留

添加全新指标或添加到指标的新维度最多可能需要2到3分钟的时间才能显示。 在系统中，在99% 的时间内，数据应该显示不到30秒。 

如果删除某一度量值或删除维度，则更改可能要从系统中删除一周。

## <a name="quotas-and-limits"></a>配额和限制
Azure Monitor 针对自定义指标实施以下用量限制：

|类别|限制|
|---|---|
|活动的时序/订阅/区域|50,000|
|每个指标的维度键数|10|
|指标命名空间、指标名称、维度键和维度值的字符串长度|256 个字符|

活动的时序定义为包含过去 12 小时内发布的指标值的指标、维度键或维度值的任意唯一组合。

## <a name="next-steps"></a>后续步骤
在不同的服务中使用自定义指标： 
 - [虚拟机](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [虚拟机规模集](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure 虚拟机（经典）](collect-custom-metrics-guestos-vm-classic.md)
 - [使用 Telegraf 代理的 Linux 虚拟机](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [经典云服务](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
