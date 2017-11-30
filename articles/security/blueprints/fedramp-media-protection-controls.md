---
title: "FedRAMP Azure Blueprint Automation - 媒体保护"
description: "FedRAMP Web 应用 - 媒体保护"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: fe86fd92-ef6b-4d17-a4a2-de6796d251d0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 9cb33abc7ab88aaa54a77308c7863f8128f2e6c7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="media-protection-mp"></a>媒体保护 (MP)

> [!NOTE]
> 美国国家标准技术研究所 (NIST) 和美国商务部在 NIST 特刊 800-53 修订版 4 中定义了这些控制。 请参阅 NIST 800-53 修订版 4，了解每项控制的测试过程和相关指南。

## <a name="nist-800-53-control-mp-1"></a>NIST 800-53 控制 MP-1

#### <a name="media-protection-policy-and-procedures"></a>媒体保护策略和过程

**MP-1** 组织应制定、记录并向[分配：组织定义的人员或角色]宣传媒体保护策略，以处理目标、范围、角色、责任、管理层承诺、组织实体协调性和符合性；并应制定、记录和宣传媒体保护过程，以推动实施媒体保护策略及相关媒体保护控制；应按照[分配：组织定义的频率]评审并更新当前媒体保护策略；并应按照[分配：组织定义的频率]评审并更新媒体保护过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级媒体保护策略和过程便可足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-mp-2"></a>NIST 800-53 控制 MP-2

#### <a name="media-access"></a>媒体访问

**MP-2** 组织应限制只有[分配：组织定义的人员或角色]才能访问[分配：组织定义的数字媒体和/或非数字媒体类型]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 已通过实施 Microsoft 安全策略实现了媒体访问控制。 对数字媒体的逻辑访问是通过 Active Directory 组策略对象 (AD GPO) 和安全组进行控制。 对所有媒体的物理访问受数据中心访问过程限制。 只有出于正当商业目的的人员，才能访问数据。 若要详细了解已采用的数据中心访问控制，请参阅“PE-3 物理访问控制”。 “资产保护标准”规定了保护 Microsoft Azure 数据中心内信息资产的保密性、完整性和可用性所需的安全措施。 |


 ## <a name="nist-800-53-control-mp-3a"></a>NIST 800-53 控制 MP-3.a

#### <a name="media-marking"></a>媒体标记

**MP-3.a** 组织应标记信息系统媒体，指明信息的分发限制、处理注意事项和适用的安全标记（若有）。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 在 Microsoft 数据中心内使用 HBI、MBI 或 LBI（高、中或低业务影响）标志标记资产，这些标志要求采取不同级别的安全和处理预防措施。 资产所有者必须对 Microsoft 数据中心内存储的资产进行分类。 有关详细信息，请参阅“资产分类标准”和“资产保护标准”。 |


 ## <a name="nist-800-53-control-mp-3b"></a>NIST 800-53 控制 MP-3.b

#### <a name="media-marking"></a>媒体标记

**MP-3.b** 组织应免除对[分配：组织定义的信息系统媒体类型]进行标记，只要媒体一直留在[分配：组织定义的控制区域]内。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 要求资产所有者为资产分配资产分类，所有资产都必须遵守这项要求。 在 Microsoft 数据中心环境内，资产是指服务器、网络设备和磁带。 不使用其他数字媒体，如 U 盘、外部/可移动硬盘或 CD/DVD。 数据中心内不使用非数字媒体。 |


 ## <a name="nist-800-53-control-mp-4a"></a>NIST 800-53 控制 MP-4.a

#### <a name="media-storage"></a>媒体存储

**MP-4.a** 组织应在[分配：组织定义的控制区域]中实际控制并安全存储[分配：组织定义的数字和/或非数字媒体类型]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 数字媒体资产实际安全存储在数据中心主机托管空间内。 Microsoft 数据中心包含多层物理访问控制（访问提醒、生物识别；若要详细了解物理访问控制，请参阅 PE-3）和视频监视，以确保存储安全性。 数字媒体包括服务器、网络设备和用于备份的磁带。 数据中心环境内不使用非数字媒体。 |


 ## <a name="nist-800-53-control-mp-4b"></a>NIST 800-53 控制 MP-4.b

#### <a name="media-storage"></a>媒体存储

**MP-4.b** 组织应保护信息系统媒体，直到使用批准的设备、技术和过程销毁或清理媒体。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | 在 Microsoft 数据中心主机托管空间内，Microsoft Azure 数字媒体资产在资产生存期内通过物理访问控制 (PE-3) 和逻辑访问控制 (IA-2) 获得保护。 Microsoft Azure 资产先通过符合 NIST SP 800-88 的方法进行清理、清除或销毁，再进行处置。 对于资产销毁，Microsoft Azure 利用现场资产销毁服务。 |


 ## <a name="nist-800-53-control-mp-5a"></a>NIST 800-53 控制 MP-5.a

#### <a name="media-transport"></a>媒体传输

**MP-5.a** 组织应使用[分配：组织定义的安全措施]，保护和控制在控制区域外传输时的[分配：组织定义的信息系统媒体类型]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft 数据中心内的 Microsoft Azure 数字媒体包括服务器、网络设备以及用于备份的磁带和磁盘（适当时）。 Microsoft 数据中心内不使用非数字媒体。 Microsoft 利用以下三种方法，保护在数据中心外传输的媒体：1) 安全传输、2) 加密、3) 清理、清除或销毁。 |


 ## <a name="nist-800-53-control-mp-5b"></a>NIST 800-53 控制 MP-5.b

#### <a name="media-transport"></a>媒体传输

**MP-5.b** 组织应维护信息系统媒体在控制区域外传输时的可信度。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 通过以下符合 NIST SP 800-88 指南的方法，维护资产在数据中心外传输时的可信度：对资产一致执行清理/清除、资产销毁、加密、精确库存，并在传输过程中跟踪和保护监管链。 |


 ## <a name="nist-800-53-control-mp-5c"></a>NIST 800-53 控制 MP-5.c

#### <a name="media-transport"></a>媒体传输

**MP-5.c** 组织应记录与信息系统媒体传输相关的活动。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 先保留库存记录，然后才会在传输过程中进行传输、跟踪和保护监管链，并在传输后执行资产清理/清除、资产销毁、资产接收和库存验证。 |


 ## <a name="nist-800-53-control-mp-5d"></a>NIST 800-53 控制 MP-5.d

#### <a name="media-transport"></a>媒体传输

**MP-5.d** 组织应限制只有已获授权的人员，才能参与信息系统媒体传输相关活动。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 通过保护监管链，限制只有已获授权的人员，才能参与资产传输活动。 使用锁、防篡改封条和验证资产库存要求，可以确保只有已获授权的人员，才能参与资产传输活动。 |


 ### <a name="nist-800-53-control-mp-5-4"></a>NIST 800-53 控制 MP-5 (4)

#### <a name="media-transport--cryptographic-protection"></a>媒体传输 | 加密保护

**MP-5 (4)** 信息系统应实现加密机制，以保护在数字媒体中存储的信息在控制区域外传输时的保密性和完整性。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 使用数据保护服务 (DPS)，管理使用 FIPS 140-2 级别 3 验证的加密模块（证书 #1694）和 HSM（证书 #1178）的加密密钥，以保护磁带上的 AES 256 位加密数据。 |


 ## <a name="nist-800-53-control-mp-6a"></a>NIST 800-53 控制 MP-6.a

#### <a name="media-sanitization"></a>媒体清理

**MP-6.a** 组织应在对资产进行处置、解除组织控制或解除控制以供重用前，根据适用的联邦和组织标准和策略，使用[分配：组织定义的清理技术和过程]清理[分配：组织定义的信息系统媒体]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 要求在重用或处置资产前，使用 Microsoft Azure 批准的工具，通过符合 NIST SP 800-88 媒体清理指南的方法清理/清除 Microsoft Azure 数据中心环境内的数字媒体。 Microsoft Azure 数据中心环境内不使用非数字媒体。 |


 ## <a name="nist-800-53-control-mp-6b"></a>NIST 800-53 控制 MP-6.b

#### <a name="media-sanitization"></a>媒体清理

**MP-6.b** 组织应采用强度和完整性与信息安全类别或分类相当的清理机制。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 使用数据擦除单元和过程，通过与 Microsoft Azure 资产分类相当且符合 NIST SP 800-88 的方法清理/清除数据。 对于需要销毁的资产，Microsoft Azure 利用现场资产销毁服务。 |


 ### <a name="nist-800-53-control-mp-6-1"></a>NIST 800-53 控制 MP-6 (1)

#### <a name="media-sanitization--review--approve--track--document--verify"></a>媒体清理 | 评审/审核/跟踪/记录/验证

**MP-6 (1)** 组织应评审、审核、跟踪、记录和验证媒体清理和处置操作。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 已根据 NIST SP 800-88 中与“资产分类标准”和“资产保护标准”相关的指南，实现了媒体清理过程。 所有磁性或电子媒体均根据与 Azure 资产分类相当的 NIST SP 800-88 规范进行清理/清除。 Azure 利用 Extreme Protocol Solutions (EPS) 中的数据擦除单元。 EPS 软件支持与清理和清除/安全擦除有关的 NIST SP 800-88 要求。 |


 ### <a name="nist-800-53-control-mp-6-2"></a>NIST 800-53 控制 MP-6 (2)

#### <a name="media-sanitization--equipment-testing"></a>媒体清理 | 设备测试

**MP-6 (2)** 组织应按照[分配：组织定义的频率]测试清理设备和过程，以验证是否已实现预期清理。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 使用数据擦除单元和过程，通过符合 NIST SP 800-88 的方法清理/清除数据。 DCS 操作每 180 天测试一次 Microsoft Azure 数据擦除单元和过程，以便清除数据。 在测试中，DCS 操作通过对测试的硬盘进行取样分析，以确认数据擦除单元是否已清理数据，从而验证是否已实现预期清理 |


 ### <a name="nist-800-53-control-mp-6-3"></a>NIST 800-53 控制 MP-6 (3)

#### <a name="media-sanitization--nondestructive-techniques"></a>媒体清理 | 非销毁性技术

**MP-6 (3)** 在下列情况下，组织应先向便携存储设备应用非销毁性清理技术，然后再将此类设备连接到信息系统：[分配：组织定义的必须清理便携存储设备的情况]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 确保 Azure 数据中心遵循“数据中心服务 Runbook”中的“工具和可移动媒体安全过程”，以防便携存储设备上的恶意软件感染政府环境。 此过程规定应先对 U 盘执行以下操作，然后才能将 U 盘用于政府环境： <br /> (1) 在首次使用前或重用于其他工具时，格式化首次从制造商或供应商处购买的 U 盘。 <br /> (2) 先扫描要用于政府指定区域的任何 U 盘是否有恶意软件，再将 U 盘放入相应区域。 <br /> (3) 在政府指定区域内使用 U 盘后，先格式化 U 盘，再允许其离开相应区域。 <br /> “工具和可移动媒体安全过程”还要求决不能将任何丢失、丢弃、被盗或错放的 U 盘重新引入 Azure 数据中心，而是要进行编录和销毁。 |


 ## <a name="nist-800-53-control-mp-7"></a>NIST 800-53 控制 MP-7

#### <a name="media-use"></a>媒体使用

**MP-7** 组织应采用[分配：组织定义的安全保护措施]，在[分配：组织定义的信息系统或系统组件]上[选择：限制；禁止]使用[分配：组织定义的信息系统媒体类型]。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 要求资产所有者为资产分配资产分类，所有资产都必须遵守这项要求。 在 Microsoft Azure 数据中心环境内，资产是指服务器和网络设备。 其他数字媒体（如 U 盘）是通过规定如何管理这些设备的特定策略和过程进行管理。 不使用 CD/DVD。 数据中心内不使用非数字媒体。 Microsoft Azure 数据中心环境内的数字媒体使用通过覆盖的 CCTV 受到全天候监视。 有关详细信息，请参阅 PE-06。 |


 ### <a name="nist-800-53-control-mp-7-1"></a>NIST 800-53 控制 MP-7 (1)

#### <a name="media-use--prohibit-use-without-owner"></a>媒体使用 | 禁止使用没有所有者的资产

**MP-7 (1)** 组织应禁止在组织信息系统中使用没有可识别所有者的便携存储设备。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有客户控制的媒体。 |
| **提供商 (Microsoft Azure)** | Microsoft 限制只能使用数据中心管理人员通过“DCS 工具和可移动媒体过程”明确批准的可写入、可移动媒体。 如“Microsoft 数据中心工作规程和法规”文档中所述，禁止在所有生产区域中使用个人拥有的媒体或没有可识别所有者的媒体。 |
