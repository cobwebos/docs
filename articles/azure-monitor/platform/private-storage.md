---
title: 在 Azure Monitor 中使用客户托管的存储帐户 Log Analytics
description: 为 Log Analytics 方案使用自己的存储帐户
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 9d54e6eb84e3269eb95f8d314875474f78536652
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526419"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>在 Azure Monitor 中使用客户托管的存储帐户 Log Analytics

Log Analytics 依赖于各种情况下的 Azure 存储。 此使用通常会自动管理。 但是，某些情况要求你提供和管理你自己的存储帐户，也称为客户管理的存储帐户。 本文档详细介绍了客户管理的存储的使用情况，用于引入 WAD/LAD 日志、专用链接特定方案和 CMK 加密。 

> [!NOTE]
> 假设格式设置和内容可能会更改，则建议您不要对内容进行依赖 Log Analytics 上传到客户管理的存储。

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>引入 Azure 诊断扩展日志 (WAD/LAD) 
Azure 诊断扩展代理 (也分别称为 Windows 和 Linux 代理的 WAD 和 LAD，) 收集各种操作系统日志，并将它们存储在客户管理的存储帐户上。 然后，可以将这些日志引入 Log Analytics 来查看和分析这些日志。
如何从存储帐户收集 Azure 诊断扩展日志使用 [Azure 门户](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) 将存储帐户作为存储数据源连接到 Log Analytics 工作区，或者通过调用 [存储见解 API](https://docs.microsoft.com/rest/api/loganalytics/connectedsources/storage%20insights/createorupdate)将存储帐户连接到工作区。

支持的数据类型：
* Syslog
* Windows 事件
* Service Fabric
* ETW 事件
* IIS 日志

## <a name="using-private-links"></a>使用专用链接
在某些用例中，当使用专用链接连接到 Azure Monitor 资源时，需要客户托管的存储帐户。 其中一种情况是引入自定义日志或 IIS 日志。 这些数据类型首先作为 blob 上传到中介 Azure 存储帐户，然后引入到工作区。 同样，某些 Azure Monitor 解决方案可能会使用存储帐户来存储 Azure 安全中心解决方案使用的大型文件，如 Watson 转储文件。 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>需要客户管理的存储的专用链接方案
* 引入自定义日志和 IIS 日志
* 允许 ASC 解决方案收集 Watson 转储文件

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>如何通过专用链接使用客户管理的存储帐户
##### <a name="workspace-requirements"></a>工作区要求
通过专用链接连接到 Azure Monitor 时，Log Analytics 代理只能将日志发送到通过专用链接链接到网络的工作区。 此规则要求正确配置 Azure Monitor 专用链接范围 (AMPLS) 对象，将其连接到你的工作区，然后通过专用链接将 AMPLS 连接到你的网络。 有关 AMPLS 配置过程的详细信息，请参阅 [使用 Azure 专用链接安全地将网络连接到 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)。 
##### <a name="storage-account-requirements"></a>存储帐户要求
要使存储帐户成功连接到专用链接，必须执行以下操作：
* 位于 VNet 或对等互连网络上，并通过专用链接连接到 VNet。 这允许 VNet 上的代理将日志发送到存储帐户。
* 与它所链接到的工作区位于同一区域。
* 允许 Azure Monitor 访问存储帐户。 如果选择仅允许选择网络访问存储帐户，则还应允许此例外： "允许受信任的 Microsoft 服务访问此存储帐户"。 这允许 Log Analytics 读取引入到此存储帐户的日志。
* 如果工作区也处理来自其他网络的流量，则应将存储帐户配置为允许来自相关网络/internet 的传入流量。

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>将你的存储帐户链接到 Log Analytics 工作区
可以通过 [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) 或 [REST API](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts)将存储帐户链接到工作区。 适用的 dataSourceType 值：
* CustomLogs –在引入过程中将存储用于自定义日志和 IIS 日志。
* AzureWatson –使用 ASC (Azure 安全中心上传的 Watson 转储文件) 解决方案。 有关管理保留、替换链接的存储帐户和监视存储帐户活动的详细信息，请参阅 [管理链接存储帐户](#managing-linked-storage-accounts)。 

## <a name="encrypting-data-with-cmk"></a>用 CMK 加密数据
Azure 存储会对存储帐户中的所有静态数据进行加密。 默认情况下，它使用 Microsoft 托管的密钥对数据进行加密 (MMK) 。 但是，Azure 存储将允许使用 Azure 密钥保管库中客户托管的密钥 (CMK) 来加密存储数据。 你可以将自己的密钥导入 Azure Key Vault 中，也可以使用 Azure Key Vault Api 来生成密钥。
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>需要客户管理的存储帐户的 CMK 方案
* 用 CMK 加密日志-警报查询
* 用 CMK 加密保存的查询

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>如何将 CMK 应用于客户管理的存储帐户
##### <a name="storage-account-requirements"></a>存储帐户要求
存储帐户和 Key Vault 必须在同一个区域中，但可以在不同的订阅中。 有关 Azure 存储加密和密钥管理的详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

##### <a name="apply-cmk-to-your-storage-accounts"></a>将 CMK 应用于你的存储帐户
若要将 Azure 存储帐户配置为使用客户管理的密钥与 Azure Key Vault，请[Azure portal](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal?toc=/azure/storage/blobs/toc.json)使用 Azure 门户[PowerShell](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-powershell?toc=/azure/storage/blobs/toc.json)或[CLI](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-cli?toc=/azure/storage/blobs/toc.json)。 

## <a name="managing-linked-storage-accounts"></a>管理链接存储帐户

若要将存储帐户链接或取消链接到工作区，请使用 [Azure CLI](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage) 或 [REST API](https://docs.microsoft.com/rest/api/loganalytics/linkedstorageaccounts)。

##### <a name="create-or-modify-a-link"></a>创建或修改链接
当你将存储帐户链接到工作区时，Log Analytics 会开始使用它，而不是服务所拥有的存储帐户。 可以 
* 注册多个存储帐户以分散它们之间的日志负载
* 为多个工作区重复使用同一存储帐户

##### <a name="unlink-a-storage-account"></a>取消存储帐户链接
若要停止使用存储帐户，请将存储从工作区中取消链接。 取消链接工作区中的所有存储帐户意味着 Log Analytics 将尝试依赖于服务托管的存储帐户。 如果网络对 internet 的访问权限有限，则这些存储可能不可用，任何依赖于存储的情况都将失败。

##### <a name="replace-a-storage-account"></a>替换存储帐户
若要替换用于引入的存储帐户，
1.  **创建新存储帐户的链接。** 日志记录代理将获取更新的配置，并开始将数据发送到新的存储。 此过程可能需要几分钟的时间。
2.  **然后取消链接旧存储帐户，以便代理停止写入已删除的帐户。** 数据引入过程将一直读取此帐户中的数据，直到全部引入。 在所有日志引入完之前，请不要删除存储帐户。

### <a name="maintaining-storage-accounts"></a>维护存储帐户
##### <a name="manage-log-retention"></a>管理日志保留
使用自己的存储帐户时，保留时间取决于你。 换句话说，Log Analytics 不会删除存储在专用存储上的日志。 相反，应设置策略以根据你的喜好处理负载。

##### <a name="consider-load"></a>考虑负载
在开始限制请求之前，存储帐户可以处理读取和写入请求的某个负载 (请参阅 [Blob 存储的可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account) ，了解更多详细信息) 。 限制会影响引入日志所用的时间。 如果你的存储帐户超载，请注册额外的存储帐户，以便在它们之间分布负载。 若要监视存储帐户的容量和性能，请查看 [Azure 门户中的见解]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview)。

### <a name="related-charges"></a>相关费用
存储帐户按存储的数据量、存储类型和冗余类型进行收费。 有关详细信息，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs)和[表存储定价](https://azure.microsoft.com/pricing/details/storage/tables)。


## <a name="next-steps"></a>后续步骤

- 了解 [如何使用 Azure 专用链接安全地将网络连接到 Azure Monitor](private-link-security.md)
- 了解 [Azure Monitor 客户管理的密钥](customer-managed-keys.md)
