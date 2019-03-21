---
title: 本地 Azure AD 密码保护常见问题解答
description: 本地 Azure AD 密码保护常见问题解答
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91ab8e8757c4a5313fde5f4d883e45648c9143b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901349"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>预览版：本地 Azure AD 密码保护 - 常见问题解答

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="general-questions"></a>一般问题

**问：在如何选择安全密码方面，你们为用户提供了哪些指导？**

在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/en-us/research/publication/password-guidance)

**问：非公有云是否支持本地 Azure AD 密码保护？**

不支持 - 只有公有云才支持本地 Azure AD 密码保护。 对于何时可在非公有云中使用，我们没有具体的日程表。

**问：如何将 Azure AD 密码保护权益应用到我的一部分本地用户？**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**问：密码更改和密码设置 （或重置） 之间的区别是什么？**

证明他们已知道旧密码后，在用户选择新密码时，密码更改。 例如，这是当用户登录到 Windows，然后提示用户选择新密码时，会发生什么情况。

当管理员帐户的密码将使用替换新密码，例如使用 Active Directory 用户和计算机管理工具时，密码设置 （有时称为密码重置）。 此操作需要高级别的特权 （通常域管理员），并且通常执行该操作的人员没有知道旧密码。 技术支持方案通常执行此操作，例如当为忘记其密码的用户提供帮助。 您还会看到事件在第一次使用密码创建一个全新的用户帐户时设置的密码。

密码验证策略行为而不考虑是否完成密码更改一个或一组相同。 Azure AD 密码保护 DC 代理服务会记录不同的事件来通知你是否密码更改或设置操作已完成。  请参阅[Azure AD 密码保护监视和日志记录](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor)。

**问：是否支持与其他基于密码筛选器的产品一同安装 Azure AD 密码保护？**

是的。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**问：在不使用 Azure 的情况下，如何在 Active Directory 环境中部署和配置 Azure AD 密码保护？**

不支持。 Azure AD 密码保护是旨在扩展到本地 Active Directory 环境中的一项 Azure 功能。

**问：如何在 Active Directory 级别修改策略的内容？**

不支持。 只能使用 Azure AD 管理门户管理策略。 另请参阅前面的问题。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**问：该功能需要占用域 sysvol 共享中的多少磁盘空间？**

准确的空间用量根据多种因素而异，例如，Microsoft 全局受禁密码列表和租户自定义密码列表中的受禁令牌数目和长度，以及加密开销。 这些列表的内容将来可能会不断扩充。 考虑到这一点，合理的预期是该功能至少需要占用域 sysvol 共享中的 5 MB 空间。

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不是。 由于代理服务器是无状态的，具体使用哪种代理服务器并不重要。

**问：是否可与其他服务（例如 Azure AD Connect）一起部署 Azure AD 密码保护代理服务？**

是的。 Azure AD 密码保护代理服务和 Azure AD Connect 永远不会直接相互冲突。

**问：按什么顺序应 DC 代理和代理服务器必须安装并注册？**

支持的代理的代理安装、 DC 代理安装、 林注册和代理注册任何排序。

**问：部署此功能是否会导致域控制器上出现性能瓶颈？**

在现有的正常 Active Directory 部署中，Azure AD 密码保护 DC 代理服务应该不会显著影响域控制器的性能。

对于大部分 Active Directory 部署而言，密码更改操作在任意给定的域控制器上只占总体工作负荷的一小部分。 例如，假设某个 Active Directory 域包含 10000 个用户帐户，某个 MaxPasswordAge 策略设置为 30 天。 一般情况下，此域每天会发生 10000/30 = 大约 333 次密码更改操作，即使是在单个域控制器中，此操作次数也是微不足道的。 考虑一种更糟糕的潜在情况：假设在单个 DC 上执行的大约 333 次密码更改是在一小时内完成的。 例如，当许多员工在星期一上午上班时，就可能会发生这种此情况。 即使出现这种情况，每 60 分钟的密码更改次数也只有大约 333 次，即每分钟 6 次，同样，这并不是一个很高的负载。

但是，如果当前域控制器已在性能限制级别运行（例如，达到了 CPU、磁盘空间、磁盘 I/O 等的上限），则我们建议添加更多的域控制器或扩展可用磁盘空间，然后再部署此功能。 另请参阅前面有关 sysvol 磁盘空间用量的问题。

**问：我只想在域中的少量 DC 上测试 Azure AD 密码保护。是否可以强制用户密码更改使用这些特定的 DC？**

不是。 当用户更改其密码时，Windows 客户端 OS 会控制要使用的域控制器。 域控制器是根据 Active Directory 站点和子网分配、特定于环境的网络配置等因素选择的。Azure AD 密码保护不会控制这些因素，也不能对选择用来更改用户密码的域控制器施加影响。

在一定程度上实现此目标的方法之一是在给定 Active Directory 站点中的所有域控制器上部署 Azure AD 密码保护。 这种方法能够合理覆盖分配到该站点的 Windows 客户端，因此，也会合理覆盖登录到这些客户端并更改其密码的用户。

**问：如果只在主域控制器 (PDC) 上安装 Azure AD 密码保护 DC 代理服务，该域中的其他所有域控制器是否也会受到保护？**

不是。 如果在给定的非 PDC 域控制器上更改用户密码时，则明文密码永远不会发送到 PDC（这种想法是常见的错误认知）。 一旦在给定的 DC 上接受新密码，该 DC 将使用该密码来为该密码创建各种特定于身份验证协议的哈希，然后在目录中保存这些哈希。 不会保存明文密码。 然后，更新的哈希将复制到 PDC。 在某些情况下，用户密码可以直接在 PDC 上更改，同样，这取决于网络拓扑和 Active Directory 站点设计等多种因素。 （请参阅前面的问题。）

总而言之，在 PDC 上部署 Azure AD 密码保护 DC 代理服务需要对整个跨中的功能实现 100% 的安全覆盖。 仅在 PDC 上部署该功能不能为域中的其他任何 DC 提供 Azure AD 密码保护安全优势。

**问：System Center Operations Manager 管理包是否适用于 Azure AD 密码保护？**

不是。

## <a name="additional-content"></a>其他内容

以下链接不是核心 Azure AD 密码保护文档的一部分，但可能有关该功能的其他信息的有用来源。

[Email Phishing Protection Guide – Part 15:Implement the Microsoft Azure AD Password Protection Service (for On-Premises too!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)（电子邮件网络钓鱼保护指南 – 第 15 部分：实施 Microsoft Azure AD 密码保护服务（也适用于本地部署！））

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)（Azure AD 密码保护和智能锁定现已推出公共预览版！）

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft Premier\Unified 支持培训

如果你想要详细了解 Azure AD 密码保护并将其部署在自己的环境中，可以利用面向已签署 Premier 或 Unified 合同的客户的 Microsoft 前瞻服务。 该服务名为“Azure Active Directory：密码保护”。 请联系技术客户经理获取详细信息。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
