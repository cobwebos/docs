---
title: Azure 安全中心提供的支持功能 |Microsoft Docs
description: 本文档提供 Azure 安全中心支持的服务列表。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: ad662cdeb0a87e57eb9e3e7480d120be1c6c729e
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218255"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure 安全中心提供支持的功能

> [!NOTE]
>某些功能仅适用于标准层。 如果尚未注册安全中心的标准层，则可以使用免费试用期。 有关详细信息，请参阅[安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)。

以下部分介绍了可用于[支持的平台](security-center-os-coverage.md)的安全中心功能。

* [虚拟机/服务器](#vm-server-features)
* [PaaS 服务](#paas-services)


## 支持虚拟机/服务器的功能<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|服务器|Windows|||Linux|||定价|
|----|----|----|----|----|----|----|----|
|**环境**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**虚拟机**|**虚拟机规模集**||**虚拟机**|**虚拟机规模集**|
|VMBA 威胁检测警报|✔|✔|✔|✔（在支持的版本上）|✔（在支持的版本上）|✔|建议 (免费) 威胁检测 (标准)|
|基于网络的威胁检测警报|✔|✔|X|✔|✔|X|标准|
|Microsoft Defender ATP 集成|✔（在支持的版本上）|✔（在支持的版本上）|✔|X|X|X|标准|
|允许缺失修补程序|✔|✔|✔|✔|✔|✔|免费|
|安全配置|✔|✔|✔|✔|✔|✔|免费|
|Endpoint protection 评估|✔|✔|✔|X|X|X|免费|
|JIT VM 访问|✔|X|X|✔|X|X|标准|
|自适应应用程序控件|✔|X|✔|✔|X|✔|标准|
|FIM|✔|✔|✔|✔|✔|✔|标准|
|磁盘加密评估|✔|✔|X|✔|✔|X|免费|
|第三方部署|✔|X|X|✔|X|X|免费|
|NSG 评估|✔|✔|X|✔|✔|X|免费|
|无文件威胁检测|✔|✔|✔|X|X|X|标准|
|网络映射|✔|✔|X|✔|✔|X|标准|
|自适应网络控制|✔|✔|X|✔|✔|X|标准|
|合规性仪表板 & 报表|✔|✔|✔|✔|✔|✔|标准|
|Docker 托管的 IaaS 容器上的建议和威胁检测|X|X|X|✔|✔|✔|标准|

### 支持的 endpoint protection 解决方案<a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果发现其中一个终结点保护解决方案，安全中心将建议不要安装。

有关为每个保护生成建议的时间的信息, 请参阅[Endpoint Protection 评估和建议](security-center-endpoint-protection.md)。

| Endpoint Protection| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| 趋势微–所有版本 * | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 服务器系列  | 否 | 是 **\*** |
| Sophos V9.x +| Linux 服务器系列  | 否 | 是的 **\***  |

 **\*** 覆盖状态和支持数据当前仅可用于与受保护订阅关联的 Log Analytics 工作区中，而不会在 Azure 安全中心门户中反映出来。

> [!NOTE]
>
> - 在 Windows Server 2008 R2 虚拟机上检测 System Center Endpoint Protection (SCEP) 需要在 PowerShell 3.0（或更高版本）之后安装 SCEP。
> - 深度安全代理支持检测趋势微保护。  不支持 OfficeScan 代理。


## PaaS 服务支持的<a name="paas-services"></a>功能

Azure 安全中心支持以下 PaaS 资源：

|服务|建议 (免费)|威胁检测 (标准)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Blob 存储|✔| ✔|
|存储帐户|✔| 不可用|
|应用服务|✔| ✔|
|Functions|✔| X|
|云服务|✔| X|
|VNet|✔| 不可用|
|Subnet|✔| 不可用|
|NIC|✔| 不可用|
|NSG|✔| 不可用|
|订阅|✔ **| ✔|
|Batch 帐户|✔| X|
|Service fabric 帐户|✔| X|
|自动化帐户|✔| X|
|负载均衡器|✔| X|
|搜索|✔| X|
|服务总线命名空间|✔| X|
|流分析|✔| X|
|事件中心命名空间|✔| X|
|逻辑应用|✔| X|
|Redis|✔| 不可用|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|密钥保管库|✔| X|

\* 公共预览版目前支持这些功能。

\*\*Azure Active Directory （Azure AD）建议仅适用于标准订阅。

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据和 Log Analytics 代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。
- 查看[支持安全中心的平台](security-center-os-coverage.md)。
- 详细了解[Azure 安全中心中的 vm & 服务器的威胁检测](security-center-alerts-iaas.md)。
- 查找[有关使用 Azure 安全中心的常见问题](security-center-faq.md)。
- 查找[关于 Azure 安全性和符合性的博客文章](https://blogs.msdn.com/b/azuresecurity/)。
