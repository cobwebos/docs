---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: marsma
ms.openlocfilehash: fc70fb163ae1f6198f66743a739a0d9720f764f1
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912388"
---
## <a name="ropc-flow-notes"></a>ROPC 流注释
在 Azure Active Directory B2C （Azure AD B2C）中，支持以下选项：

- **本机客户端**：当代码在用户端设备上运行时，将在身份验证期间进行用户交互。 该设备可以是在本机操作系统（如 Android 和 iOS）中运行的移动应用程序。
- **公共客户端流**：只有通过应用程序收集的用户凭据才会在 API 调用中发送。 不会发送应用程序的凭据。
- **添加新声明**：可更改 ID 令牌内容以添加新的声明。

不支持以下流：

- **服务器到服务器**：标识保护系统在交互过程中需要从调用方（本地客户端）收集的可靠 IP 地址。 在服务器端 API 调用中，仅使用服务器的 IP 地址。 如果超过了失败身份验证的动态阈值，则标识保护系统可能将重复的 IP 地址识别为攻击者。
- **机密客户端流**：应用程序客户端 ID 已验证，但应用程序机密未验证。

使用 ROPC 流时，请注意以下事项：

- 当需要用户交互的身份验证流发生任何中断时，ROPC 不起作用。 例如，当密码已过期或需要更改时，需要多重身份验证，或者在登录期间需要收集更多的信息（例如，用户同意）。
- ROPC 仅支持本地帐户。 用户无法用联合标识提供者（如 Microsoft、Google +、Twitter、AD FS 或 Facebook）登录。
- 会话管理（包括 "使我保持登录" （KMSI））不适用。
