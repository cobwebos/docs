---
title: Azure 安全性和符合性蓝图 - FedRAMP Web 应用程序自动化 - 标识与身份验证
description: FedRAMP Web 应用程序自动化 - 标识和身份验证
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="identification-and-authentication-ia"></a>标识和身份验证 (IA)

## <a name="nist-800-53-control-ia-1"></a>NIST 800-53 控制措施 IA-1

> [!NOTE]
> 本文档所述的控制措施由美国国家标准技术研究所 (NIST) 和美国商务部定义，作为 NIST 特别出版物 800-53 修订版 4 的一部分。 请参阅 NIST 800-53 修订版 4，了解每项控制措施的测试过程和指南。

#### <a name="identification-and-authentication-policy-and-procedures"></a>标识和身份验证政策和过程

**IA-1** 组织开发、记录和分发到[赋值：组织定义的个人或角色]标识和身份验证策略，该策略已解决用途、范围、角色、职责、管理承诺、组织实体之间的协调和合规性；促进标识和身份验证策略及其关联的标识和身份验证控制措施的实施过程；检查和更新当前标识和身份验证策略[赋值：组织定义的频率]；标识和身份验证过程[赋值：组织定义的频率]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户的企业级标识和身份验证策略和过程可能足以应对此控制措施。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-2"></a>NIST 800-53 控制措施 IA-2

#### <a name="identification-and-authentication-organizational-users"></a>标识和身份验证（组织用户）

**IA-2** 信息系统唯一标识和身份验证组织用户（或代表组织用户进行处理）。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图创建的帐户拥有唯一标识符。 禁用或删除具有非唯一标识符的内置帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-1"></a>NIST 800-53 控制措施 IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>标识和身份验证（组织用户）| 对特权帐户的网络访问

**IA-2 (1)** 信息系统为网络访问特权帐户实施多重身份验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施对网络访问特权帐户的多重身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-2"></a>NIST 800-53 控制措施 IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>标识和身份验证（组织用户）| 网络访问非特权帐户

**IA-2 (2)** 信息系统为网络访问非特权帐户实施多重身份验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施对网络访问非特权帐户的多重身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-3"></a>NIST 800-53 控制措施 IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>标识和身份验证（组织用户）| 本地访问特权帐户

**IA-2 (3)** 信息系统为本地访问特权帐户实施多重身份验证。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户在 Azure 数据中心没有对任何系统资源的本地访问权限。 |
| **提供商 (Microsoft Azure)** | 除非需要物理访问，否则 Microsoft Azure 不允许本地访问。 本地管理员访问必须仅用于解决以下实例中的问题：该实例中，成员服务器遇到网络问题并且域身份验证不可用。 <br /> Azure 通过物理访问环境所需的访问控制机制实施本地访问的多重身份验证。 Azure 数据中心的空间（其中包括系统边界内的所有 Azure 基础结构系统）受各种物理安全机制限制，包括企业智能卡徽章访问和生物识别设备的要求。 这两种形式的身份验证都是入口点处对 Azure 数据中心托管的物理访问需要的。 |


 ### <a name="nist-800-53-control-ia-2-4"></a>NIST 800-53 控制措施 IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>标识和身份验证（组织用户）| 本地访问非特权帐户

**IA-2 (4)** 信息系统为本地访问非特权帐户实施多重身份验证。

**责任：**`Azure Only`

|||
|---|---|
| **客户** | 客户在 Azure 数据中心没有对任何系统资源的本地访问权限。 |
| **提供商 (Microsoft Azure)** | Microsoft Azure 会将 Microsoft Azure 政府员工使用的所有 Microsoft Azure 政府帐户视为特权帐户。 为所有使用智能卡和 PIN 的 Microsoft Azure 政府员工帐户实施多重身份验证，包括本地访问。 |


 ### <a name="nist-800-53-control-ia-2-5"></a>NIST 800-53 控制措施 IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>标识和身份验证（组织用户）| 组身份验证

**IA-2 (5)** 组织要求使用组身份验证时，使用个人验证器对个人进行身份验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的资源上未启用共享/组帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-8"></a>NIST 800-53 控制措施 IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>标识和身份验证（组织用户）| 网络访问特权帐户 - 防止重播

**IA-2 (8)** 信息系统为网络访问特权帐户实施防止重播身份验证机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 通过 Azure Active Directory、Active Directory 和 Windows 操作系统的内置 Kerberos 功能，防止此蓝图部署的资源访问受到重播攻击。 在 Kerberos 身份验证中，客户端发送的验证器包含其他数据，如加密的 IP 列表、客户端时间戳和票证生存期。 如果重播数据包，则会检查时间戳。 如果时间戳早于、或与上一验证器的时间相同，则会拒绝数据包。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-9"></a>NIST 800-53 控制措施 IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>标识和身份验证（组织用户）| 网络访问非特权帐户 - 防止重播

**IA-2 (9)** 信息系统为网络访问非特权帐户实施防止重播身份验证机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 通过 Azure Active Directory、Active Directory 和 Windows 操作系统的内置 Kerberos 功能，防止此蓝图部署的资源访问受到重播攻击。 在 Kerberos 身份验证中，客户端发送的验证器包含其他数据，如加密的 IP 列表、客户端时间戳和票证生存期。 如果重播数据包，则会检查时间戳。 如果时间戳早于、或与上一验证器的时间相同，则会拒绝数据包。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-11"></a>NIST 800-53 控制措施 IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>标识和身份验证（组织用户）| 远程访问 - 单独的设备

**IA-2 (11)** 信息系统对远程访问特权和非特权帐户实施多重身份验证，使其中一个因素由独立于获取访问权限的系统的设备提供且设备满足[赋值：机制要求的组织定义的强度]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该客户负责实施对远程访问客户部署的资源的多种身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-2-12"></a>NIST 800-53 控制措施 IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>标识和身份验证（组织用户）| Piv 凭据的接受

**IA-2 (12)** 信息系统接受并对个人标识验证 (PIV) 凭据进行电子验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责接受和验证个人标识验证 (PIV) 凭据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-3"></a>NIST 800-53 控制措施 IA-3

#### <a name="device-identification-and-authentication"></a>设备标识和身份验证

**IA 3** 信息系统在建立[选择（一个或多个）：本地；远程；网络]连接前，唯一标识和身份验证[赋值：组织定义的特定和/或设备类型]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责实施设备标识和身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-4a"></a>NIST 800-53 控制措施 IA-4.a

#### <a name="identifier-management"></a>标识符管理

**IA 4.a** 组织通过从[赋值：组织定义的员工或角色]接收授权并将其分配到个人、组、角色或设备标识符来管理信息系统标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户资源的标识符（即个人、组、角色和设备）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-4b"></a>NIST 800-53 控制措施 IA-4.b

#### <a name="identifier-management"></a>标识符管理

**IA 4.b** 组织通过选择标识个人、组、角色或设备的标识符来管理信息系统标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图会在部署期间提示个人帐户的客户指定标识符。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-4c"></a>NIST 800-53 控制措施 IA-4.c

#### <a name="identifier-management"></a>标识符管理

**IA-4.c** 组织通过将标识符分配给预期的个人、组、角色或设备来管理信息系统标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理客户资源的标识符（即个人、组、角色和设备）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-4d"></a>NIST 800-53 控制措施 IA-4.d

#### <a name="identifier-management"></a>标识符管理

**IA 4.d** 组织通过阻止重复使用[赋值：组织定义的时间段]的标识符来管理信息系统标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 为 Active Directory 和本地 Windows 操作系统帐户分配唯一安全标识符 (SID)。 为 Azure Active Directory 帐户分配全局唯一对象 ID。 这些唯一 ID 不能重复使用。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-4e"></a>NIST 800-53 控制措施 IA-4.e

#### <a name="identifier-management"></a>标识符管理

“IA-4.e” 组织通过禁用[赋值：组织定义的非活动状态时间段]后的标识符来管理信息系统标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实现计划的任务，让 Active Directory 在不活动 35 天后自动禁用帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-4-4"></a>NIST 800-53 控制措施 IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>标识符管理 |识别用户状态

**IA-4 (4)** 组织通过将每个人唯一标识为[赋值：组织定义的特征标识个人状态]来管理个人标识符。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Active Directory 和 Active Directory 支持使用应用到标识符的命名约定来表示合同工、供应商和其他用户类型。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 控制措施 IA-5.a

#### <a name="authenticator-management"></a>验证器管理

**IA 5.a** 组织通过验证（作为初始验证器分发的一部分）接收验证器的个人、组、角色或设备的标识来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5b"></a>NIST 800-53 控制措施 IA-5.b

#### <a name="authenticator-management"></a>验证器管理

**IA 5.b** 组织通过创建组织定义的验证器的初始验证器内容来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图创建的帐户的所有初始验证器内容符合部署期间由客户指定的已通过验证的 IA-5 (1) 中所述要求。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5c"></a>NIST 800-53 控制措施 IA-5.c

#### <a name="authenticator-management"></a>验证器管理

**IA 5.c** 组织通过确保验证器具有其所需的充足机制强度来管理管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图使用的验证器符合 FedRAMP 要求的强度。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5d"></a>NIST 800-53 控制措施 IA-5.d

#### <a name="authenticator-management"></a>验证器管理

**IA 5.d** 组织通过创建和实施用于初始验证器分发、验证器丢失/被盗或损坏和验证器撤销的管理流程来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5e"></a>NIST 800-53 控制措施 IA-5.e

#### <a name="authenticator-management"></a>验证器管理

**IA-5.e** 通过在信息系统安装之前更改验证器的默认内容，该组织可管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图组件的所有验证器都已更改默认设置。 此解决方案部署期间，验证器是由客户指定的。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5f"></a>NIST 800-53 控制措施 IA-5.f

#### <a name="authenticator-management"></a>验证器管理

**IA 5.f** 组织通过创建验证器的最短和最长生存期限制和重复使用条件来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责管理验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5g"></a>NIST 800-53 控制措施 IA-5.g

#### <a name="authenticator-management"></a>验证器管理

**IA 5.g** 组织通过更改/刷新验证器[赋值：组织定义的时间段（按验证器类型）]来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 创建和配置组策略以实施密码生存期限制（60 天）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5h"></a>NIST 800-53 控制措施 IA-5.h

#### <a name="authenticator-management"></a>验证器管理

**IA 5.h** 组织通过防止验证器内容未授权泄露和修改来管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实施密钥保管库以防止验证器内容未授权泄露和修改。 以下验证器存储在密钥保管库中：部署帐户的 Azure 密码、虚拟机管理员密码、SQL Server 服务帐户密码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5i"></a>NIST 800-53 控制措施 IA-5.i

#### <a name="authenticator-management"></a>验证器管理

**IA 5.i** 组织通过要求设备实施特定的安全措施以保护验证器，从而管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图实施密钥保管库以防止验证器内容未授权泄露和修改。 以下验证器存储在密钥保管库中：部署帐户的 Azure 密码、虚拟机管理员密码、SQL Server 服务帐户密码。 密钥保管库通过使用受硬件安全模块 (HSMs) 保护的密钥，来加密密钥和机密（例如身份验证器密钥、存储帐户密钥、数据加密密钥和密码）。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-5j"></a>NIST 800-53 控制措施 IA-5.j

#### <a name="authenticator-management"></a>验证器管理

**IA 5.j** 通过在帐户成员关系更改时更改组/角色帐户的验证器，组织可以管理信息系统验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 由此蓝图部署的资源上未启用共享/组帐户。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1a"></a>NIST 800-53 控制措施 IA-5 (1).a

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA 5 (1).a** 用于基于密码的身份验证的信息系统强制执行[赋值：区分大小写、字符数量、大小写字母、数字和特殊字符的混合的组织定义要求（包括每种类型的最低要求）]的最低密码复杂性。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 创建和配置组策略以强制执行虚拟机本地账户和 AD 帐户的密码复杂性要求。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1b"></a>NIST 800-53 控制措施 IA-5 (1).b

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA-5 (1).b** 用于基于密码的身份验证的信息系统在创建新密码时至少强制执行以下更改字符数：[赋值：组织定义的数字]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源中使用基于密码的身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1c"></a>NIST 800-53 控制措施 IA-5 (1).c

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA 5 (1).c** 用于基于密码的身份验证的信息系统仅存储和传输加密保护的密码。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Directory 用于确保存储和传输时，所有密码都受到加密保护。 由 Active Directory 存储和本地存储在已部署的 Windows 虚拟机上的密码自动哈希为内置安全功能的一部分。 远程桌面身份验证会话通过 TLS 受到保护，以确保传输时验证器受到保护。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1d"></a>NIST 800-53 控制措施 IA-5 (1).d

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA 5 (1).d** 用于基于密码的身份验证的信息系统强制执行[赋值：组织定义的生存期最小值、生存期最大值]的密码生存期最小值和最大值限制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 创建和配置组策略以强制执行对密码的限制，即强制执行本地帐户和 AD 帐户的最小值（1 天）和最大值（60 天）的生存期限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1e"></a>NIST 800-53 控制措施 IA-5 (1).e

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA-5 (1).e** 用于基于密码的身份验证的信息系统禁止[赋值：组织定义的数字]生成的密码重复使用。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 该蓝图部署其中联接所有已部署的虚拟机的域控制器。 创建并配置组策略以强制执行对本地帐户和 AD 帐户的重复使用条件（24 个密码）的限制。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-1f"></a>NIST 800-53 控制措施 IA-5 (1).f

#### <a name="authenticator-management--password-based-authentication"></a>验证器管理 |基于密码的身份验证

**IA-5 (1).f** 用于基于密码的身份验证的信息系统允许使用临时密码进行系统登录，永久性密码立即更改。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | Azure Active Directory 用于管理对信息系统的控制访问。 最初创建帐户或生成临时密码时，使用 Azure Active Directory 要求用户在下次登录时更改密码。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-2a"></a>NIST 800-53 控制措施 IA-5 (2).a

#### <a name="authenticator-management--pki-based-authentication"></a>验证器管理 |基于 PKI 的身份验证

**IA 5 (2).a** 用于基于 PKI 的身份验证的信息系统通过构建和验证已接受信任书签的证书路径（包括检查证书状态信息）来验证证书。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源中使用基于 PKI 的身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-2b"></a>NIST 800-53 控制措施 IA-5 (2).b

#### <a name="authenticator-management--pki-based-authentication"></a>验证器管理 |基于 PKI 的身份验证

**IA 5 (2).b** 用于基于 PKI 的身份验证的信息系统强制执行对对应私钥的授权访问。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源中使用基于 PKI 的身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-2c"></a>NIST 800-53 控制措施 IA-5 (2).c

#### <a name="authenticator-management--pki-based-authentication"></a>验证器管理 |基于 PKI 的身份验证

**IA 5 (2).c** 用于基于 PKI 的身份验证的信息系统将身份验证标识映射到个人或组的帐户。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源中使用基于 PKI 的身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-2d"></a>NIST 800-53 控制措施 IA-5 (2).d

#### <a name="authenticator-management--pki-based-authentication"></a>验证器管理 |基于 PKI 的身份验证

**IA 5 (2).d** 用于基于 PKI 的身份验证的信息系统实施吊销数据的本地缓存以支持路径发现和验证，以防无法通过网络访问吊销信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责在客户部署的资源中使用基于 PKI 的身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-3"></a>NIST 800-53 控制措施 IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>验证器管理 | 自行注册或受信任的第三方注册

**IA 5 (3)** 组织要求在具有[赋值：组织定义的人员或角色]授权的[赋值：组织定义的注册机构]前，执行接收[赋值：组织定义的类型和/或特定的验证器]的注册过程。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责注册验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-4"></a>NIST 800-53 控制措施 IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>验证器管理 | 自动支持密码强度确定

**IA 5 (4)** 组织使用自动化工具确定密码验证器是否有足够强度来满足[赋值：组织定义的要求]。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 使用此蓝图部署的用户帐户包括 AD 和本地用户帐户。 这两种方法都提供强制符合已创建密码要求的机制，以便创建初始密码和更改密码。 Azure Active Directory 是自动工具，用于确定密码验证器是否足够强大，以便满足 IA-5 (1) 中设定的密码长度、复杂性、循环和生存期限制。 Azure Active Directory 可确保在创建时密码验证器强度满足这些标准。 检查用于部署此解决方案的客户指定的密码是否符合密码强度要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-6"></a>NIST 800-53 控制措施 IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>验证器管理 | 验证器的保护

**IA 5 (6)** 组织保护与使用验证器允许访问的信息的安全类别相符的验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责保护验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-7"></a>NIST 800-53 控制措施 IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>验证器管理 | 无嵌入未加密的静态验证器

**IA 5 (7)** 组织确保未加密的静态验证器没有嵌入到应用程序或访问脚本中，也没有存储到功能键上。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 没有使用通过此蓝图部署的嵌入到应用程序、访问脚本或功能键中的未加密静态验证器。 每次使用之前，任何使用验证器的脚本或应用程序都会调用 Azure Key Vault 容器。 审核对 Azure Key Vault 容器的访问，如果使用服务帐户访问系统而未相应地调用 Azure Key Vault 容器，则允许检测此禁止的冲突。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-8"></a>NIST 800-53 控制措施 IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>验证器管理 | 多个信息系统帐户

**IA 5 (8)** 组织实施[赋值：组织定义的安全措施]来管理由于个人在多个信息系统上拥有帐户导致的泄漏风险。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户可能依赖企业级安全措施来管理因个人在多个系统上拥有帐户而导致的风险。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-11"></a>NIST 800-53 控制措施 IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>验证器管理 | 基于硬件令牌的身份验证

**IA 5 (11)** 用于基于硬件令牌的身份验证的信息系统使用满足[赋值：组织定义的令牌质量要求]的机制。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责使用此机制来满足基于硬件令牌的身份验证质量要求。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-5-13"></a>NIST 800-53 控制措施 IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>验证器管理 | 缓存验证器的有效期限

**IA 5 (13)** 信息系统禁止在[赋值：组织定义的时间段]之后使用缓存验证器。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 通过此蓝图部署的资源没有配置为允许使用缓存验证器。 对部署虚拟机的身份验证要求在身份验证时输入验证器。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-6"></a>NIST 800-53 控制措施 IA-6

#### <a name="authenticator-feedback"></a>验证器反馈

**IA-6** 信息系统在身份验证过程中模糊了身份验证信息的反馈，以防止未获得授权的个人利用/使用该信息。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 对蓝图部署的资源的访问通过远程桌面，且依赖于 Windows 身份验证。 Windows 身份验证会话的默认行为会在身份验证会话期间输入密码时进行掩码。  |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-7"></a>NIST 800-53 控制措施 IA-7

#### <a name="cryptographic-module-authentication"></a>加密模块身份验证

**IA 7** 信息系统实施对加密模块进行身份验证的机制，以满足此类身份验证适用的联邦法律、行政命令、指令、政策、法规、标准和指南的要求。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 此蓝图采用 Windows 身份验证、远程桌面和 BitLocker。 这些组件可以配置为依赖 FIPS 140 验证的加密模块。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ## <a name="nist-800-53-control-ia-8"></a>NIST 800-53 控制措施 IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>标识和身份验证（非组织用户）

**IA-8** 信息系统唯一标识和身份验证非组织用户（或代表非组织用户进行处理）。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责对访问客户部署资源的非组织用户进行标识和身份验证。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-8-1"></a>NIST 800-53 控制措施 IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>标识和身份验证（非组织用户）| 从其他机构接受 Piv 凭据

**IA-8 (1)** 信息系统接受并对来自其他联邦机构的个人标识验证 (PIV) 凭据进行电子验证。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责接受和验证其他联邦机构颁发的个人标识验证 (PIV) 凭据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-8-2"></a>NIST 800-53 控制措施 IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>标识和身份验证（非组织用户）| 第三方凭据的接受

**IA-8 (2)** 信息系统仅接受 FICAM 批准的第三方凭据。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责仅接受经联邦标识、凭据和访问管理 (FICAM) 信任框架解决方案计划批准的第三方凭据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-8-3"></a>NIST 800-53 控制措施 IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>标识和身份验证（非组织用户）| 使用经 Ficam 批准的产品

**IA-8 (3)** 组织仅使用[赋值：组织定义的信息系统]中的经 FICAM 批准的信息系统组件，接受第三方凭据。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责仅使用经联邦标识、凭据和访问管理 (FICAM) 信任框架解决方案计划批准的资源来接受第三方凭据。 |
| **提供商 (Microsoft Azure)** | 不适用 |


 ### <a name="nist-800-53-control-ia-8-4"></a>NIST 800-53 控制措施 IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>标识和身份验证（非组织用户）| 使用 Ficam 颁发的配置文件

**IA-8 (4)** 信息系统符合 FICAM 颁发的配置文件。

**责任：**`Customer Only`

|||
|---|---|
| **客户** | 客户负责确保与经联邦标识、凭据和访问管理 (FICAM) 信任框架解决方案计划颁发的配置文件相符合。 |
| **提供商 (Microsoft Azure)** | 不适用 |
