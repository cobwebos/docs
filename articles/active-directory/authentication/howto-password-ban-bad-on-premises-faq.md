---
title: 本地 Azure AD 密码保护常见问题解答
description: 查看本地活动目录域服务环境中的 Azure AD 密码保护常见问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: abef6e52e37cfa2faeb426bc59bb0de5a52a6658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671668"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 密码保护本地常见问题

本节提供有关 Azure AD 密码保护的许多常见问题的解答。

## <a name="general-questions"></a>一般问题

**问：应该如何为用户提供有关如何选择安全密码的指导？**

在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

**问：非公共云中是否支持本地 Azure AD 密码保护？**

不支持 - 只有公有云才支持本地 Azure AD 密码保护。 对于何时可在非公有云中使用，我们没有具体的日程表。

Azure AD 门户允许修改特定于本地的"Windows 服务器活动目录的密码保护"配置，即使在非公共云中也是如此;此类更改将保持不变，但否则将永远不会生效。 使用非公共云凭据时，不支持本地代理或林的注册，任何此类注册尝试都将始终失败。

**问：如何将 Azure AD 密码保护权益应用于本地用户的子集？**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**问：密码更改和密码集（或重置）之间的区别是什么？**

密码更改是用户在证明他们知道旧密码后选择新密码。 例如，密码更改是当用户登录到 Windows 然后提示选择新密码时发生的情况。

密码集（有时称为密码重置）是管理员用新密码替换帐户上的密码时，例如使用 Active Directory 用户和计算机管理工具。 此操作需要高级别的权限（通常是域管理员），执行该操作的人员通常不了解旧密码。 帮助台方案通常执行密码集，例如帮助忘记密码的用户时。 首次使用密码创建全新用户帐户时，您还将看到密码设置事件。

无论密码更改或设置是否完成，密码验证策略都执行相同的操作。 Azure AD 密码保护 DC 代理服务会记录不同的事件，以通知您是否完成了密码更改或设置操作。  请参阅[Azure AD 密码保护监视和日志记录](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)。

**问：为什么在尝试使用 Active Directory 用户和计算机管理卡入设置弱密码时记录重复的密码拒绝事件？**

活动目录用户和计算机管理管理卡入将首先尝试使用 Kerberos 协议设置新密码。 失败后，管理单元将再次尝试使用旧式 （SAM RPC） 协议设置密码（使用的特定协议并不重要）。 如果 Azure AD 密码保护认为新密码很弱，则此密码登录行为将导致记录两组密码重置拒绝事件。

**问：为什么使用空用户名记录 Azure AD 密码保护密码验证事件？**

Active Directory 支持测试密码以查看是否通过域当前密码复杂性要求（例如使用[NetValidatePasswordPolicy](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api） 的功能。 以这种方式验证密码时，测试还包括通过基于密码筛选器 dll 的产品（如 Azure AD 密码保护）进行验证，但传递给给定密码筛选器 dll 的用户名将为空。 在这种情况下，Azure AD 密码保护仍将使用当前有效的密码策略验证密码，并将发出事件日志消息以捕获结果，但事件日志消息将具有空用户名字段。

**问：是否支持与其他基于密码筛选器的产品并排安装 Azure AD 密码保护？**

是的。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**问：如何在不使用 Azure 的情况下在活动目录环境中部署和配置 Azure AD 密码保护？**

不支持。 Azure AD 密码保护是旨在扩展到本地 Active Directory 环境中的一项 Azure 功能。

**问：如何在活动目录级别修改策略的内容？**

不支持。 只能使用 Azure AD 门户管理策略。 另请参阅前面的问题。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

如果您的域尚未使用 DFSR，则必须在安装 Azure AD 密码保护之前将其迁移以使用 DFSR。 有关详细信息，请参阅以下链接：

[SYSVOL 复制迁移指南：FRS 到 DFS 复制](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS 进行系统复制的域控制器上，但该软件在此环境中无法正常工作。 其他负面的副作用包括单个文件无法复制，sysvol 还原过程似乎成功，但默默地无法复制所有文件。 应尽快迁移域以使用 DFSR，这既有利于 DFSR 的固有优势，也有助于取消阻止 Azure AD 密码保护的部署。 在仍在使用 FRS 的域中运行时，软件的未来版本将自动禁用。

**问：该功能在域 sysvol 共享上需要多少磁盘空间？**

准确的空间用量根据多种因素而异，例如，Microsoft 全局受禁密码列表和租户自定义密码列表中的受禁令牌数目和长度，以及加密开销。 这些列表的内容将来可能会不断扩充。 考虑到这一点，合理的预期是该功能至少需要占用域 sysvol 共享中的 5 MB 空间。

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不是。 由于代理服务器是无状态的，具体使用哪种代理服务器并不重要。

**问：是否可以与其他服务（如 Azure AD 连接）并行部署 Azure AD 密码保护代理服务？**

是的。 Azure AD 密码保护代理服务和 Azure AD Connect 永远不会直接相互冲突。

遗憾的是，Azure AD 密码保护代理软件安装的 Microsoft Azure AD Connect 代理更新程序服务的版本与[Azure 活动目录应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)软件安装的服务版本之间发现不兼容。 这种不兼容可能导致代理更新程序服务无法与 Azure 联系以获取软件更新。 不建议在同一台计算机上安装 Azure AD 密码保护代理和 Azure 活动目录应用程序代理。

**问：DC 代理和代理的安装和注册顺序如何？**

支持代理代理安装、DC 代理安装、林注册和代理注册的任何排序。

**问：我是否应该担心部署此功能对域控制器的性能影响？**

在现有的正常 Active Directory 部署中，Azure AD 密码保护 DC 代理服务应该不会显著影响域控制器的性能。

对于大部分 Active Directory 部署而言，密码更改操作在任意给定的域控制器上只占总体工作负荷的一小部分。 例如，假设某个 Active Directory 域包含 10000 个用户帐户，某个 MaxPasswordAge 策略设置为 30 天。 一般情况下，此域每天会发生 10000/30 = 大约 333 次密码更改操作，即使是在单个域控制器中，此操作次数也是微不足道的。 考虑一种更糟糕的潜在情况：假设在单个 DC 上执行的大约 333 次密码更改是在一小时内完成的。 例如，当许多员工在星期一上午上班时，就可能会发生这种此情况。 即使出现这种情况，每 60 分钟的密码更改次数也只有大约 333 次，即每分钟 6 次，同样，这并不是一个很高的负载。

但是，如果当前域控制器已在性能限制级别运行（例如，达到了 CPU、磁盘空间、磁盘 I/O 等的上限），则我们建议添加更多的域控制器或扩展可用磁盘空间，然后再部署此功能。 另请参阅前面有关 sysvol 磁盘空间用量的问题。

**问：我想在我的域中仅测试几个 DC 上的 Azure AD 密码保护。是否可以强制更改用户密码以使用这些特定的 DC？**

不是。 当用户更改其密码时，Windows 客户端 OS 会控制要使用的域控制器。 域控制器是根据活动目录站点和子网分配、特定于环境的网络配置等因素选择的。Azure AD 密码保护不控制这些因素，并且不会影响选择哪个域控制器来更改用户的密码。

在一定程度上实现此目标的方法之一是在给定 Active Directory 站点中的所有域控制器上部署 Azure AD 密码保护。 这种方法能够合理覆盖分配到该站点的 Windows 客户端，因此，也会合理覆盖登录到这些客户端并更改其密码的用户。

**问：如果我在主域控制器 （PDC） 上安装 Azure AD 密码保护 DC 代理服务，域中的所有其他域控制器是否也会受到保护？**

不是。 如果在给定的非 PDC 域控制器上更改用户密码时，则明文密码永远不会发送到 PDC（这种想法是常见的错误认知）。 一旦在给定的 DC 上接受新密码，该 DC 将使用该密码来为该密码创建各种特定于身份验证协议的哈希，然后在目录中保存这些哈希。 不会保存明文密码。 然后，更新的哈希将复制到 PDC。 在某些情况下，用户密码可以直接在 PDC 上更改，同样，这取决于网络拓扑和 Active Directory 站点设计等多种因素。 （请参阅前面的问题。）

总而言之，在 PDC 上部署 Azure AD 密码保护 DC 代理服务需要对整个跨中的功能实现 100% 的安全覆盖。 仅在 PDC 上部署该功能不能为域中的其他任何 DC 提供 Azure AD 密码保护安全优势。

**问：为什么即使在代理安装在我的本地活动目录环境中后，自定义智能锁定也不起作用？**

仅 Azure AD 中支持自定义智能锁定。 对 Azure AD 门户中的自定义智能锁定设置的更改对本地活动目录环境没有影响，即使安装了代理也是如此。

**问：系统中心操作管理器管理包是否可用于 Azure AD 密码保护？**

不是。

**问：为什么我已将策略配置为处于审核模式，Azure AD 仍拒绝弱密码？**

审核模式仅在本地活动目录环境中受支持。 Azure AD 在计算密码时始终处于"强制"模式。

**问：当 Azure AD 密码保护拒绝密码时，我的用户会看到传统的 Windows 错误消息。是否可以自定义此错误消息，以便用户知道到底发生了什么？**

不是。 当域控制器拒绝密码时，用户看到的错误消息由客户端计算机控制，而不是由域控制器控制。 无论默认的 Active Directory 密码策略或基于密码筛选器的解决方案（如 Azure AD 密码保护）拒绝密码，都会发生此行为。

## <a name="additional-content"></a>更多内容

以下链接不是核心 Azure AD 密码保护文档的一部分，但可能有关该功能的其他信息的有用来源。

[Azure AD 密码保护现在通常可用！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[电子邮件网络钓鱼保护指南 – 第 15 部分：实现 Microsoft Azure AD 密码保护服务（也用于本地！](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)（Azure AD 密码保护和智能锁定现已推出公共预览版！）

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft Premier\Unified 支持培训

如果你想要详细了解 Azure AD 密码保护并将其部署在自己的环境中，可以利用面向已签署 Premier 或 Unified 合同的客户的 Microsoft 前瞻服务。 该服务称为 Azure 活动目录：密码保护。 请联系技术客户经理获取详细信息。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
