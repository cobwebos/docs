---
title: Azure 付款处理蓝图 - 物理访问要求
description: PCI DSS 要求 9
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: b702f3d3a06b1d47c2853ab51ccf4872441da03d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的物理访问要求 
## <a name="pci-dss-requirement-9"></a>PCI DSS 要求 9

**限制对持卡人数据的物理访问**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

对数据进行物理访问或者对持卡人数据所在的系统进行物理访问意味着，用户在访问设备或数据的同时有机会删除系统或硬拷贝，因此应进行适当的限制。 就要求 9 来说，“现场人员”是指全职和兼职的员工、临时员工、承包商和顾问，是实实在在存在于实体所在地区的人。 “访问者”是指供应商、任何现场人员的客人、服务工人或任何需要短暂（通常不超过一天）进入设施的人员。 “介质”是指所有纸质和电子形式的介质，其中包含持卡人数据。

## <a name="pci-dss-requirement-91"></a>PCI DSS 要求 9.1

**9.1** 使用适当的设施进入控制措施，限制和监视对持卡人数据环境中系统的物理访问。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责实施、强制和监视针对数据中心的物理访问安全措施。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-911"></a>PCI DSS 要求 9.1.1

**9.1.1** 使用摄像机和/或访问控制机制监视对敏感区域的每次物理访问。 审核收集的数据并将其与其他时间进入时的情况进行关联。 存储时间至少为三个月，除非法律对此有其他限制性规定。

> [!NOTE]
> “敏感区域”是指任何数据中心、服务器房间或用于存储、处理或传输持卡人数据的系统所在的任何区域。 这不包括面向公众的只存在销售点终端的区域，例如零售店中的收银员区域。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责实施、强制和监视针对数据中心的 CCTV 和生物识别访问控制机制。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-912"></a>PCI DSS 要求 9.1.2

**9.1.2** 实施物理的和/或逻辑的控制，限制用户接触可以公开使用的网络插孔。 

例如，可以禁用公共区域以及可供访问者访问的区域中的网络插孔，只在明确允许进行网络访问的时候启用。 也可实施相关的流程，确保在网络插孔已启用的区域访问者始终有人陪同。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 平台中没有可公开使用的网络插孔。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-913"></a>PCI DSS 要求 9.1.3

**9.1.3** 限制对无线接入点、网关、手持设备、网络/通信硬件和电信线路的物理访问。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 对 Microsoft Azure 网络硬件进行物理访问会受到严格的控制，控制措施包括：访问列表、多种形式的身份验证、入口处的物理障碍、要求提供业务需求，经批准后才能访问设备。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



## <a name="pci-dss-requirement-92"></a>PCI DSS 要求 9.2

**9.2** 制定相关过程来轻松识别现场人员和访问者，其中包括：
- 标识现场人员和访问者（例如，发放徽章）
- 更改访问要求
- 撤销或终止使用现场人员和过期访问者的标识（例如 ID 徽章）。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责实施、强制和监视物理访问安全措施以及员工或承包商在访问数据中心时使用的标识。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



## <a name="pci-dss-requirement-93"></a>PCI DSS 要求 9.3

**9.3** 控制现场人员对敏感区域进行的物理访问，如下所示：
- 访问必须获得授权并基于个人职能。
- 访问权限在终止后立即撤销，所有物理访问机制（例如钥匙、访问卡等）必须返回或禁用。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft 数据中心的访问授权使用授权访问列表进行控制，该列表由数据中心团队根据“尽量减少特权”的原则进行审批。 访问控制列表按季进行审核、验证和更新。<br /><br />Microsoft Azure 数据中心使用各种物理访问设备，例如外围入口、电子访问徽章读取器、生物识别读取器、限制门/门户、防重用设备。 会持续监视访问徽章设备。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



## <a name="pci-dss-requirement-94"></a>PCI DSS 要求 9.4

**9.4** 通过实施相关过程来标识和授权访问者。 过程应包括以下内容。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责确保预先批准的交付项已在安全的收货间收到，该收货间与信息处理设施进行了物理隔离，并由获得授权的人员监视。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-941"></a>PCI DSS 要求 9.4.1

**9.4.1** 访问者在进入持卡人数据进行处理或维护的区域之前必须获得授权，在进入后全程有人陪同。


**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责确保预先批准的交付项已在安全的收货间收到，该收货间与信息处理设施进行了物理隔离，并由获得授权的人员监视。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-942"></a>PCI DSS 要求 9.4.2

**9.4.2** 访问者在进行身份验证后会获得一个徽章或其他标识，该徽章或标识会过期，并且可以用来对访问者和现场人员进行肉眼区分。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft 数据中心访问权限必须预先获得批准，获得授权的访问者必须使用物理的安全方式在入口点签入，在进入前需提供有效的身份证明。 徽章可以清楚地指示员工。 承包商和访问者收到的临时徽章必须在离开设施时交回。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-943"></a>PCI DSS 要求 9.4.3

**9.4.3** 访问者必须在离开设施之前或过期之日交回徽章。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 访问者必须在离开 Microsoft 设施时交回徽章。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-944"></a>PCI DSS 要求 9.4.4

**9.4.4** 使用访问者日志，保留访问者在设施地点、计算机房间和数据中心（在其中进行持卡人数据存储或传输操作）进行的活动的物理审核记录。
在日志中记录访问者的姓名、所代表的公司，以及允许其进行物理访问的现场人员。
此日志至少保留三个月，除非法律对此有其他限制。

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 负责保留访问者日志，作为访问者在设施地点、计算机房间和数据中心（在其中进行持卡人数据存储或传输操作）进行的活动的物理审核记录。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



## <a name="pci-dss-requirement-95"></a>PCI DSS 要求 9.5

**9.5** 以物理方式保护所有介质。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-951"></a>PCI DSS 要求 9.5.1

**9.5.1** 将介质备份存储在安全的位置，最好是一个场外设施，例如备用或备份站点，或者商业存储设施。 至少每年审核一次该位置的安全性。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-96"></a>PCI DSS 要求 9.6

**9.6** 始终严格控制对任何类型介质（包括以下类型）的内部或外部分发。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-961"></a>PCI DSS 要求 9.6.1

**9.6.1** 对介质分类，以便确定数据的敏感程度。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-962"></a>PCI DSS 要求 9.6.2

**9.6.2** 通过有保障的快递公司或其他可以准确追踪的送货方式寄送介质。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-963"></a>PCI DSS 要求 9.6.3

**9.6.3** 确保管理人员审核任何及所有从受保护区域移动的介质（包括将介质分发给个人的情况）。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-97"></a>PCI DSS 要求 9.7

**9.7** 始终严格控制介质的存储和可访问性。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-971"></a>PCI DSS 要求 9.7.1

**9.7.1** 始终以清单方式对所有介质进行适当的记录，至少每年对介质进行一次清单记录。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



## <a name="pci-dss-requirement-98"></a>PCI DSS 要求 9.8

**9.8** 出于商业或法律原因而不再需要介质时，销毁这些介质，如下所示。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-981"></a>PCI DSS 要求 9.8.1

**9.8.1** 将硬拷贝材料粉碎、焚烧或变成纸浆，使他人无法重新构造持卡人数据。 保护用于存储要销毁的材料的存储容器。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有数据存储在 Azure SQL 数据库中。 PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-982"></a>PCI DSS 要求 9.8.2

**9.8.2** 使电子介质上的持卡人数据无法恢复，这样他人就无法重新构造持卡人数据。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 数据析构技术各不相同，具体取决于要销毁的数据对象的类型，即它是订阅、存储、虚拟机还是数据库。 在 Microsoft Azure 多租户环境中，必须小心确保一个客户的数据不会“漏”到其他客户的数据中，或者当客户删除数据时，其他客户（大多数情况下包括曾经拥有这些数据的客户）也无法访问那些被删除的数据。<br /><br />Microsoft Azure 遵循有关介质清理的 NIST 800-88 准则，解决了确保数据不是随意发布的这一主要考量。 这些准则包含电子清理和物理清理。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 删除在部署中使用的资源组即可整个删除 Contoso Webstore。|



## <a name="pci-dss-requirement-99"></a>PCI DSS 要求 9.9

**9.9** 对通过与卡直接物理交互捕获支付卡数据的设备进行保护，防止出现篡改和替换现象。

> [!NOTE]
> 这些要求适用于在销售点的有卡支付（即刷卡或插卡）中使用的读卡设备。 该要求不适用于手动键输入组件，例如计算机键盘和 POS 键盘。 

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 利用 Azure 来记录所有系统更改。<br /><br />Log Analytics 可广泛记录更改。 可以检查和验证更改的准确性。 如需更具体的指南，请参阅 [PCI 指南 - 日志记录和审核](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-991"></a>PCI DSS 要求 9.9.1

**9.9.1** 确保设备列表始终为最新。 列表应包括以下内容：
- 品牌，设备的型号
- 设备的位置（例如，设备所在地点或设施的地址）
- 设备序列号或其他用于表示唯一身份的方法

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供参考体系结构和一个列表，其中包含在其部署文档中使用的所有服务。|



### <a name="pci-dss-requirement-992"></a>PCI DSS 要求 9.9.2

**9.9.2** 定期检查设备表面，看是否存在篡改现象（例如，向设备添加读卡器）或替换现象（例如，可以通过检查序列号或其他设备特征来验证该设备是否尚未被替换为欺骗性设备）。

> [!NOTE]
> 例如，表明设备可能被篡改或替换的特征包括：向设备插入了不正常的附件或电缆、安全标签缺失或更改、外壳破损或颜色变化、更改了序列号或其他外部标记。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-993"></a>PCI DSS 要求 9.9.3

**9.9.3** 为相关人员提供培训，让其了解各种篡改或替换设备的企图。 培训应包括以下内容：
- 验证任何声称自己是维修或维护人员的第三方人员的身份，然后再授予其进行设备修改或故障诊断所需的访问权限。
- 不在未验证的情况下安装、更换或返回设备。
- 留意在设备周围出现的可疑行为（例如，不明人员尝试拔出或打开设备）。
- 向合适的人员（例如，管理员或安全官员）报告可疑行为以及表明设备遭篡改或替换的情况。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



## <a name="pci-dss-requirement-910"></a>PCI DSS 要求 9.10

**9.10** 确保记录在限制对持卡人数据的物理访问时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|




