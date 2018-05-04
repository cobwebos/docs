---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 系统和通信保护
description: FedRAMP Web 应用程序自动化 - 系统和通信保护
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6749ad50cd1ea1cd4ec6ca2f86fef43a9f1515d9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="system-and-communications-protection-sc"></a>系统和通信保护 (SC)

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

## <a name="nist-800-53-control-sc-1"></a>NIST 800-53 控制 SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>系统和通信保护策略及过程

**SC-1** 组织应制定、记录并向[分配：组织定义的人员或角色]宣传系统和通信保护策略，以处理目标、范围、角色、责任、管理层承诺、组织实体协调性和符合性；并应制定、记录和宣传系统和通信保护过程，以推动实施系统和通信保护策略及相关系统和通信保护控制；应按照[分配：组织定义的频率]评审并更新当前系统和通信保护策略；并应按照[分配：组织定义的频率]评审并更新系统和通信保护过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统和通信保护策略及过程便可足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800-53 控制 SC-2

#### <a name="application-partitioning"></a>应用分区

**SC-2** 信息系统应将用户功能（包括用户界面服务）与信息系统管理功能分隔开来。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图通过强制实施逻辑访问控制和系统体系结构，将用户功能与系统管理功能分隔开来。 用户功能仅限于客户部署的 Web 应用界面。 系统管理功能界面不同于用户界面。 所有管理连接都是通过管理子网中的安全堡垒主机 (jumpbox) 实现，子网中包含网络安全组规则，可以适当限制对生产资源的访问。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800-53 控制 SC-3

#### <a name="security-function-isolation"></a>安全功能隔离

**SC-3** 信息系统应将安全功能与非安全功能隔离开来。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 向每个进程分配专用虚拟地址空间，以保持每个执行进程都有独立的执行域。 此外，解决方案还实现了旨在根据需要隔离安全功能的体系结构和访问控制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800-53 控制 SC-4

#### <a name="information-in-shared-resources"></a>共享资源中的信息

**SC-4** 信息系统应防止通过共享系统资源未经授权地意外传输信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 操作系统负责管理资源（例如，内存、存储），要求只有拥有相应权限的用户和角色才能访问信息。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800-53 控制 SC-5

#### <a name="denial-of-service-protection"></a>拒绝服务保护

**SC-5** 信息系统应采用[分配：组织定义的安全措施]，避免或限制下列类型的拒绝服务攻击造成的影响：[分配：组织定义的拒绝服务攻击类型或引用此类信息的源]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的应用程序网关包含 Web 应用程序防火墙和负载均衡功能。 可缩放的可用性集中部署了支持 Web 层、数据库层和 Active Directory 的虚拟机。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800-53 控制 SC-6

#### <a name="resource-availability"></a>资源可用性

**SC-6** 信息系统应按照[选择（一个或多个）：优先级；配额；[分配：组织定义的安全措施]]分配[分配：组织定义的资源]，从而保护资源可用性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 每个 Windows 进程提供执行程序所需的资源。 资源优先级由操作系统进行管理。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800-53 控制 SC-7.a

#### <a name="boundary-protection"></a>边界保护

**SC-7.a** 信息系统应监视并控制系统外部边界和系统内部关键边界处的通信。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署应用程序网关、负载均衡器，并配置网络安全组规则来控制外部边界和内部子网之间的通信。 为方便客户监视，Log Analytics 收集应用程序网关、负载均衡器、网络安全组事件和诊断日志。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-7b"></a>NIST 800-53 控制 SC-7.b

#### <a name="boundary-protection"></a>边界保护

**SC-7.b** 信息系统应为可公开访问的系统组件实现从[选择：物理；逻辑]上不同于内部组织网络的子网。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图在包含独立 Web 子网、数据库子网、Active Directory 子网和管理子网的体系结构中部署资源。 子网从逻辑上是以应用于各个子网的网络安全组规则进行分隔，以限制子网之间只能有系统和管理功能所必需的流量（例如，外部流量无法访问数据库子网、管理子网或 Active Directory 子网）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800-53 控制 SC-7.c

#### <a name="boundary-protection"></a>边界保护

**SC-7.c** 信息系统应只通过托管接口连接到外部网络或信息系统，此类接口由根据组织安全体系结构排列的边界保护设备组成。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署应用程序网关来管理客户部署的 Web 应用的外部连接。 外部管理访问连接只能由管理子网中部署的堡垒主机/跳板机实现，子网中应用了网络安全规则，以限制只有授权 IP 地址才能进行外部连接。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800-53 控制 SC-7 (3)

#### <a name="boundary-protection--access-points"></a>边界保护 | 接入点

**SC-7 (3)** 组织应限制连接到信息系统的外部网络连接数量。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署两个公共 IP 地址：一个与应用程序网关相关联，另一个与管理堡垒主机/jumpbox 相关联。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-4a"></a>NIST 800-53 控制 SC-7 (4).a

#### <a name="boundary-protection--external-telecommunications-services"></a>边界保护 | 外部电信服务

**SC-7 (4).a** 组织应为每个外部电信服务实现托管接口。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署两个公共 IP 地址：一个与应用程序网关相关联，另一个与管理堡垒主机/jumpbox 相关联。 这些接口的管理是通过软件定义的网络进行启用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800-53 控制 SC-7 (4).b

#### <a name="boundary-protection--external-telecommunications-services"></a>边界保护 | 外部电信服务

**SC-7 (4).b** 组织应为每个托管接口制定流量策略。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署两个公共 IP 地址：一个与应用程序网关相关联，另一个与管理堡垒主机/jumpbox 相关联。 这些接口的管理是通过软件定义的网络进行启用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800-53 控制 SC-7 (4).c

#### <a name="boundary-protection--external-telecommunications-services"></a>边界保护 | 外部电信服务

**SC-7 (4).c** 组织应保护跨每个接口传输的信息的保密性和完整性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的 Web 应用程序网关配置了 HTTPS 侦听器，以确保通信会话的保密性和完整性。 连接到跳板机的远程桌面连接也经过加密，以保护机密性和完整性。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800-53 控制 SC-7 (4).d

#### <a name="boundary-protection--external-telecommunications-services"></a>边界保护 | 外部电信服务

**SC-7 (4).d** 组织应记录流量策略的每个例外，以及支持的使命/业务需求及其持续时间。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户不负责数据中心运营，无法添加电信服务。 所有电信服务均由 Microsoft Azure 提供和托管。 此控制继承自 Azure。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800-53 控制 SC-7 (4).e

#### <a name="boundary-protection--external-telecommunications-services"></a>边界保护 | 外部电信服务

**SC-7 (4).e** 组织应按照[分配：组织定义的频率]评审流量策略例外，并删除明确使命/业务需求不再支持的例外。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户不负责数据中心运营，无法添加电信服务。 所有电信服务均由 Microsoft Azure 提供和托管。 此控制继承自 Azure。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800-53 控制 SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>边界保护 | 默认拒绝/例外允许

**SC-7 (5)** 托管接口处的信息系统应默认拒绝和例外允许（即，全部拒绝和例外允许）网络通信流量。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的网络安全组中应用的规则集使用“默认拒绝”方案进行配置。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800-53 控制 SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>边界保护 | 阻止远程设备发生隧道分离

**SC-7 (7)** 信息系统应（连同远程设备）防止设备与系统建立非远程连接，同时又通过与外部网络中资源建立的其他某连接进行通信。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级远程设备配置策略可以解决隧道分离问题。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800-53 控制 SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>边界保护 | 将流量路由到已验证代理服务器

**SC-7 (8)** 信息系统应通过托管接口处的已验证代理服务器，将[分配：组织定义的内部通信流量]路由到[分配：组织定义的外部网络]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责通过已验证代理将客户定义的信息路由到外部网络。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800-53 控制 SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>边界保护 | 防止未经授权的渗漏

**SC-7 (10)** 组织应防止跨托管接口发生未经授权的信息渗漏。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责防止跨托管接口发生未经授权的信息渗漏。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800-53 控制 SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>边界保护 | 基于主机的保护

**SC-7 (12)** 组织应在[分配：组织定义的信息系统组件]处实现[分配：组织定义的基于主机的边界保护机制]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机配置并启用了基于主机的防火墙。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800-53 控制 SC-7 (13)

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>边界保护 | 隔离安全工具/机制/支持组件

**SC-7 (13)** 组织应在其他系统组件中实现包含托管接口的物理独立子网，将[分配：组织定义的信息安全工具、机制和支持组件]与其他内部信息系统组件隔离开来。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图在包含独立管理子网的体系结构中部署资源，以便客户能够部署信息安全工具和支持组件。 子网从逻辑上以网络安全组规则进行分隔。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800-53 控制 SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>边界保护 | 以安全妥善的方式发生故障

**SC-7 (18)** 信息系统应在边界保护设备操作失败时以安全妥善的方式发生故障。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 在 Azure 上部署的系统范围内，没有物理边界保护设备。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 在各个地理位置部署独立的冗余网关服务器和 SSL VPN。 如果网关系统出现故障，它会以安全妥善的方式发生故障，并且限制只能访问环境。 为了建立与 Microsoft Azure 环境的连接，用户必须与 Microsoft Azure 托管的活动网关服务器建立单独连接。 <br /> 此外，如果 Microsoft Azure 网络设备（包括边缘路由器、访问路由器、负载均衡器、聚合交换机和 TORS）发生故障，受影响的电路会断开，因此是以安全妥善的方式发生故障。 Microsoft Azure 网络设备故障不会导致系统外部的信息进入设备，也不会允许未经授权地发布信息。 借助内置冗余，Microsoft Azure 资产在发生故障时不会影响可用性。 |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800-53 控制 SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>边界保护 | 动态隔离/划分

**SC-7 (20)** 信息系统应支持将[分配：组织定义的信息系统组件]与其他系统组件动态隔离/划分开来。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确保系统能够动态隔离客户部署的资源。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800-53 控制 SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>边界保护 | 隔离信息系统组件

**SC-7 (21)** 组织应采用边界保护机制，分隔支持[分配：组织定义的使命和/或企业机能]的[分配：组织定义的信息系统组件]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图在包含独立 Web 子网、数据库子网、Active Directory 子网和管理子网的体系结构中部署资源。 子网从逻辑上是以应用于各个子网的网络安全组规则进行分隔，以限制子网之间只能有系统和管理功能所必需的流量。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800-53 控制 SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>传输保密性和完整性

**SC-8** 信息系统应保护传输信息的[选择（一个或多个）：保密性；完整性]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | SI 8 (1) 实现便可足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800-53 控制 SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>传输保密性和完整性 | 加密或备用物理保护

**SC-8 (1)** 信息系统应在传输过程中实现加密机制，以[选择（一个或多个）：防止遭他人未经授权地披露信息；检测信息更改]，除非另受[分配：组织定义的备用物理安全措施]保护。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图配置的资源仅使用安全协议进行通信。 应用程序网关的 WAF 组件配置为，接受通过 HTTPS/TLS 外部使用的通信器，并仅通过 HTTPS/TLS 与后端池进行通信。 SQL Server 配置为仅通过 HTTPS/TLS 进行通信。 远程桌面服务配置为使用安全连接。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800-53 控制 SC-10

#### <a name="network-disconnect"></a>网络连接中断

**SC-10** 信息系统应在会话结束时或[分配：组织定义的时间段]内无任何操作后，终止与通信会话相关联的网络连接。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 远程桌面会话的身份验证是由 Active Directory 进行管理。 在 Active Directory 中为用户禁用访问权限后，远程会话便会立即终止。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800-53 控制 SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>加密密钥建立和管理

**SC-12** 组织应根据[分配：组织定义的密钥生成、分发、存储、访问和销毁要求]，为信息系统内应用的必要加密建立并管理加密密钥。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署 Azure Key Vault。 Azure 密钥保管库可帮助保护云应用程序和服务使用的加密密钥和机密。 Azure Key Vault 可以使用 FIPS 140-2 级别 2 硬件安全模块 (HSM) 密钥生成功能生成密钥。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800-53 控制 SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>加密密钥建立和管理 | 可用性

**SC-12 (1)** 组织应在用户丢失加密密钥时保护信息的可用性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Key Vault 用于存储此蓝图中使用的加密密钥和机密。 Key Vault 简化了用于访问和加密数据的密钥的密钥管理过程。 以下验证器存储在 Key Vault 中：部署帐户的 Azure 密码、虚拟机管理员密码、SQL Server 服务帐户密码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800-53 控制 SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>加密密钥建立和管理 | 对称密钥

**SC-12 (2)** 组织应使用[选择：符合 NIST FIPS 要求；经 NSA 批准]的关键管理技术和流程，生成、控制和分发对称加密密钥。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Key Vault 用于生成、控制和分发加密密钥。 Azure Key Vault 可以使用 FIPS 140-2 级别 2 硬件安全模块 (HSM) 密钥生成功能生成密钥。 在 Azure Key Vault 中，密钥是在安全加密容器内进行存储和管理。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800-53 控制 SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>加密密钥建立和管理 | 非对称密钥

**SC-12 (3)** 组织应使用[选择：经 NSA 批准的密钥管理技术和流程；经批准的 PKI 3 类证书或预配密钥材料；经批准的 PKI 3 类或 4 类证书以及保护用户私钥的硬件安全令牌]生成、控制和分发非对称加密密钥。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责生成、控制和分发非对称加密密钥（如果用于客户部署的资源的话）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800-53 控制 SC-13

#### <a name="cryptographic-protection"></a>加密保护

**SC-13** 信息系统应根据适用的联邦法律、行政命令、指令、政策、法规和标准，实现[分配：组织定义的加密用途和每种用途所需的加密类型]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图采用 Windows 身份验证、远程桌面和 BitLocker。 这些组件可以配置为依赖 FIPS 140 验证的加密模块。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800-53 控制 SC-15.a

#### <a name="collaborative-computing-devices"></a>协作计算设备

**SC-15.a** 信息系统应禁止远程激活协作计算设备，但有以下例外：[分配：组织定义的允许远程激活的例外]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署协作计算设备。 注意：在 Azure 上部署的系统范围内，有物理协作计算设备。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800-53 控制 SC-15.b

#### <a name="collaborative-computing-devices"></a>协作计算设备

**SC-15.b** 信息系统应向设备实际用户明确指明使用协作计算设备。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署协作计算设备。 注意：在 Azure 上部署的系统范围内，有物理协作计算设备。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800-53 控制 SC-17

#### <a name="public-key-infrastructure-certificates"></a>公钥基础结构证书

**SC-17** 组织应根据[分配：组织定义的证书策略]颁发公钥证书，或从批准的服务提供商处获取公钥证书。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖企业级公钥基础结构颁发证书。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800-53 控制 SC-18.a

#### <a name="mobile-code"></a>移动代码

**SC-18.a** 组织应定义可接受和不可接受的移动代码以及移动代码技术。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统和通信保护过程可以定义可接受和不可接受的移动代码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800-53 控制 SC-18.b

#### <a name="mobile-code"></a>移动代码

**SC-18.b** 组织应为可接受的移动代码和移动代码技术制定使用情况限制和实施指南。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级系统和通信保护过程可以制定移动代码使用情况限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800-53 控制 SC-18.c

#### <a name="mobile-code"></a>移动代码

**SC-18.c** 组织应授权、监视和控制信息系统内的移动代码使用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可以依赖企业级流程来授权、监视和控制移动代码使用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800-53 控制 SC-19.a

#### <a name="voice-over-internet-protocol"></a>IP 语音 (VoIP)

**SC-19.a** 组织应根据恶意使用可能会给信息系统带来的损害，为 IP 语音 (VoIP) 技术制定使用情况限制和实施指南。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署 IP 语音技术。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800-53 控制 SC-19.b

#### <a name="voice-over-internet-protocol"></a>IP 语音 (VoIP)

**SC-19.b** 组织应授权、监视和控制信息系统内的 VoIP 使用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图未部署 IP 语音技术。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800-53 控制 SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>安全名称/地址解析服务（权威源）

**SC-20.a** 除了系统响应外部名称/地址解析查询而返回的权威名称解析数据外，信息系统应提供额外的数据原始身份验证和完整性验证项目。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责安全名称和地址解析服务。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800-53 控制 SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>安全名称/地址解析服务（权威源）

**SC-20.b** 信息系统应提供一种途径，以便指明子区域的安全状态，并启用验证在分布式分层命名空间中运行的父域和子域之间的信任链（如果子级支持安全解析服务的话）。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责安全名称和地址解析服务。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800-53 控制 SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>安全名称/地址解析服务（递归或缓存解析程序）

**SC-21** 信息系统应对系统从权威源收到的名称/地址解析响应，请求并执行数据原始身份验证和数据完整性验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责将客户部署的资源配置为，对系统从权威源收到的名称/地址解析响应，请求并执行数据原始身份验证和数据完整性验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800-53 控制 SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>名称体系结构和预配/地址解析服务

**SC-22** 统一为组织提供名称/地址解析服务的信息系统应为容错系统，并实现内部/外部角色分隔。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确保为客户部署的资源提供地址解析服务的系统应为容错系统，并实现内部/外部角色分隔。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800-53 控制 SC-23

#### <a name="session-authenticity"></a>会话验证

**SC-23** 信息系统应保护通信会话验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 对此蓝图部署的资源（包括 Azure 门户、远程桌面连接和 Web 应用程序网关）的远程访问使用 TLS 进行保护。 TLS 提供会话级通信验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800-53 控制 SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>会话验证 | 注销时让会话标识符失效

**SC-23 (1)** 信息系统应在用户注销或其他会话终止时让会话标识符失效。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 对此蓝图部署的资源（包括 Azure 门户、远程桌面连接和 Web 应用程序网关）的远程访问使用 TLS 进行保护。 Azure 门户和远程桌面会话在用户注销时让会话标识符失效。 Web 会话失效是通过 Azure 应用程序网关（即 Web 应用防火墙 (WAF) 规则）强制实施。 WAF 应用每会话 Cookie 相关性，并在客户端 30 分钟（部署到组织专属规则后可配置）无操作后执行会话超时。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800-53 控制 SC-24

#### <a name="fail-in-known-state"></a>发生故障时处于已知状态

**SC-24** 信息系统应在发生故障时处于[分配：组织定义的故障类型]的[分配：组织定义的已知状态]，同时在故障中保留[分配：组织定义的系统状态信息]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确保客户部署的资源在发生故障时处于已知状态。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800-53 控制 SC-28

#### <a name="protection-of-information-at-rest"></a>保护静态信息

**SC-28** 信息系统应保护[分配：组织定义的静态信息]的[选择（一个或多个）：保密性；完整性]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | SC-28 (1) 实现便可足以满足这项控制要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800-53 控制 SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>保护静态信息 | 加密保护

**SC-28 (1)** 信息系统应实现加密机制，以防[分配：组织定义的信息系统组件]上的[分配：组织定义的信息]遭他人未经授权地披露和修改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机实现磁盘加密，以保护静态信息的保密性和完整性。 适用于 Windows 的 Azure 磁盘加密是使用 Windows 的 BitLocker 功能进行实现。 SQL Server 配置为使用透明数据加密 (TDE)，实时加密和解密数据和日志文件，从而保护静态信息。 TDE 可确保存储的数据免遭他人未经授权的访问。 客户可以选择实现额外的应用级控制，以保护所存储信息的完整性。 此蓝图部署的所有存储 blob 的保密性和完整性通过使用 Azure 存储服务加密 (SSE) 进行保护。 SSE 使用 256 位 AES 加密保护 Azure 存储帐户中的静态数据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800-53 控制 SC-39

#### <a name="process-isolation"></a>进程隔离

**SC-39** 信息系统应保持每个执行进程都有独立的执行域。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图部署的虚拟机运行 Windows 操作系统。 Windows 向每个进程分配专用虚拟地址空间，以保持每个执行进程都有独立的执行域。 |
| **提供商 (Microsoft Azure)** | 不适用 |
