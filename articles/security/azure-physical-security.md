---
title: Azure 设施、场地和物理安全性 | Microsoft Docs
description: 本文介绍 Azure 数据中心，包括物理基础结构、安全性与合规性产品。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101337"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure 设施、场地和物理安全性
Microsoft 云由[全球分布式数据中心基础结构](https://azure.microsoft.com/global-infrastructure/)组成，该基础结构支持数千个联机服务，并跨越全球 100 多个高度安全的设施。

该基础结构旨在使应用程序更靠近全球用户、预留数据的驻留位置，并为客户提供全面的符合性与复原选项。 Azure 在全球设立了 52 个区域，并已在 140 个国家/地区推出。

区域是指通过大规模弹性网络互连的一系列数据中心。 该网络默认包含内容分发、负载均衡、冗余和加密功能。 Azure 包含的全球区域比任何其他云提供商所包含的都多，因此允许用户灵活地选择部署应用程序所需的位置。

Azure 区域组织为地域形式。 Azure 地域保证数据驻留、主权、符合性和恢复能力的要求在地域边界内得到遵从。

地域允许具有特定数据驻留和符合性要求的客户保持他们的数据和应用程序相邻近。 通过与专用的高容量网络基础设施相连，地域具有容错能力，可承受整个区域的故障。

可用性区域是 Azure 区域中的物理上独立的位置。 每个可用性区域都由一个或多个数据中心组成，这些数据中心都配置了独立电源、冷却和网络。 可用性区域允许客户运行任务关键型应用程序，同时具有高可用性和低延迟复制。

下图显示了 Azure 全球基础结构如何将同一数据驻留边界内的区域和可用性区域配对，以实现高可用性、灾难恢复和备份。

![数据驻留边界][1]

数据中心的广泛地理覆盖范围使得 Microsoft 非常靠近客户，以降低网络延迟并实现异地冗余的备份和故障转移。

## <a name="physical-security"></a>物理安全性
Microsoft 设计、构建和运营数据中心的方式能够严格控制对存储客户数据的区域的物理访问。 Microsoft 理解保护客户数据的重要性，并承诺帮助保护包含客户数据的数据中心。 Microsoft 专门设立了一个完整的部门来设计、构建和运营支持 Azure 的物理设施。 此团队在维持一流物理安全性方面投入了大量的人力物力。

Microsoft 采用分层方法实现物理安全性，以减少未经授权的用户获取数据和数据中心资源的物理访问权限的风险。 Microsoft 管理的数据中心具有广泛的保护层：在设施周边、建筑物周边、建筑物内部和数据中心楼层上实施访问权限审批。 物理安全层包括：

- 访问权限请求和审批 – 必须在抵达数据中心之前请求访问权限。 必须提供来访的有效业务理由，例如，出于法规或审核目的。 Microsoft 员工根据“访问必要性”审批所有请求。 “访问必要性”依据有助于将在数据中心完成某个任务所需的人数减到最少。 获取授权后，个人只能根据批准的业务理由访问数据中心的离散区域。 权限的有效期限制为特定的一段时间，在允许的时间段后过期。

- 设施周边 - 抵达数据中心时，必须经过完善定义的访问点。 通常，由钢筋混凝土制成的高墙会围住周边的每一次土地。 数据中心的周围有摄像头，安全团队全年无休地监控视频。

- 建筑物入口 - 数据中心的入口由专业的保安人员值守，他们经受过严格的培训和背景检查。 这些保安人员还会例行巡视数据中心，同时也会全年无休地监控数据中心内部的摄像头视频。

- 建筑物内部 - 进入建筑物后，必须使用生物识别特征通过双重身份验证，然后才能继续在数据中心内部走动。 如果你的身份通过验证，则可以进入已获批访问的数据中心区域。 只能在该区域中逗留批准的一段时间。

- 数据中心楼层 – 你只能进入获批进入的楼层。 必须通过全身金属探测扫描。 为了减少在我们不知情的情况下，未经授权的数据进入或离开数据中心的风险，只有获批准的设备可以进入数据中心楼层。 此外，视频摄像头会监控每个服务器机架的正面和背面。 当你离开数据中心楼层时，同时需要通过全身金属探测扫描。 若要离开数据中心，必须通过其他安全扫描。

访问者必须在离开 Microsoft 设施时交回徽章。

## <a name="physical-security-reviews"></a>物理安全性评审
我们定期对设施执行物理安全性评审，确保数据中心正常满足 Microsoft Azure 的安全要求。 数据中心托管提供商人员不会提供 Microsoft Azure 服务管理。 这些人员没有 Azure 系统的登录访问权限，也没有 Azure 机房和机舱的物理访问权限。

## <a name="data-bearing-devices"></a>数据承载设备
Microsoft 使用最佳做法过程和[符合 NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01) 的数据擦除解决方案。 对于无法擦除的硬盘驱动器，将使用销毁过程来销毁该驱动器，并避免恢复信息。 销毁过程可能包括解体、切碎、粉碎或焚烧。 适当的处置方式将按照资产类型来决定， 同时会保留销毁记录。  

## <a name="equipment-disposal"></a>设备处置
Microsoft Azure 代表客户实现这一原则。 在系统使用寿命结束时，Microsoft 操作人员会遵循严格的数据处理过程和硬件处置过程，确保不会将包含客户数据的硬件提供给不受信任的一方使用。 对于支持安全擦除的驱动器，我们会遵循安全擦除方法（通过硬盘驱动器固件）。 对于无法擦除的硬盘驱动器，将使用销毁过程来销毁该驱动器，并避免恢复信息。 销毁过程可能包括解体、切碎、粉碎或焚烧。 适当的处置方式将按照资产类型来决定， 同时会保留销毁记录。 所有 Microsoft Azure 服务使用已批准的介质存储和处置管理服务。

## <a name="compliance"></a>合规性
Azure 基础结构的设计和管理符合广泛的国际和行业特定标准，例如 ISO 27001、HIPAA、FedRAMP、SOC 1 和 SOC 2。 此外还符合国家/地区特定的标准，包括澳大利亚的 IRAP、英国的 G-Cloud 和新加坡的 MTCS。 严苛的第三方审核（例如英国标准协会进行的审核）可验证 Azure 是否遵循严格的安全控制标准。

有关 Azure 遵守的合规标准的完整列表，请参阅[合规性产品](https://www.microsoft.com/trustcenter/compliance/complianceofferings)。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [Azure 基础结构的完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
