---
title: 了解安全、Azure 管理的工作站 - Azure 活动目录
description: 了解安全、Azure 管理的工作站，并了解它们的重要性。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a3a8cf14a591dd3037175e4eed5b5bd8d3096c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672660"
---
# <a name="understand-secure-azure-managed-workstations"></a>了解安全、Azure 管理的工作站

安全、隔离的工作站对于管理员、开发人员和关键服务操作员等敏感角色的安全至关重要。 如果客户端工作站安全性受到威胁，许多安全控制和保证可能会失败或无效。

本文档介绍了构建安全工作站（通常称为特权访问工作站 （PAW） 所需的内容。 本文还包含设置初始安全控件的详细说明。 本指南介绍了基于云的技术如何管理服务。 它依赖于 Windows 10RS5、Microsoft 防御者高级威胁保护 （ATP）、Azure 活动目录和 Microsoft Intune 中引入的安全功能。

> [!NOTE]
> 本文介绍了安全工作站的概念及其重要性。 如果您已经熟悉了这个概念，并希望跳到部署，请访问[部署安全工作站](howto-azure-managed-workstation.md)。

## <a name="why-secure-workstation-access-is-important"></a>为什么安全工作站访问很重要

云服务的快速采用和随时随地工作的能力创造了一种新的利用方法。 通过在管理员工作的设备上利用薄弱的安全控制，攻击者可以访问特权资源。

特权滥用和供应链攻击是攻击者用来破坏组织的五大方法之一。 根据[Verizon威胁报告](https://enterprise.verizon.com/resources/reports/dbir/)[和安全情报报告](https://aka.ms/sir)，在2018年报道的事件中，它们也是第二大最常被检测到的战术。

大多数攻击者都遵循以下步骤：

1. 侦察，以找到一种方法，往往特定于一个行业。
1. 分析以收集信息并确定渗透被视为低价值工作站的最佳方式。
1. 坚持寻找[横向移动的方法](https://en.wikipedia.org/wiki/Network_Lateral_Movement)。
1. 泄露机密和敏感数据。

在侦察过程中，攻击者经常潜入风险低或被低估的设备。 他们使用这些易受攻击的设备来寻找横向移动的机会，并查找管理用户和设备。 在获取特权用户角色后，攻击者将识别高价值数据并成功泄露该数据。

![典型的折衷模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文档介绍了一种解决方案，可帮助保护您的计算设备免受此类横向攻击。 该解决方案将管理和服务与价值较低的生产力设备隔离开来，在能够渗透访问敏感云资源的设备之前，该链条就打破了这一链条。 该解决方案使用作为 Microsoft 365 企业版栈一部分的本机 Azure 服务：

* 设备管理功能以及应用程序和 URL 的安全列表
* 用于设备设置、部署和刷新的自动驾驶仪
* 用于用户管理、条件访问和多重身份验证的 Azure AD
* 适用于设备运行状况证明和用户体验的 Windows 10（当前版本）
* 维护 ATP，用于云管理的端点保护、检测和响应
* Azure AD PIM，用于管理授权和对资源的实时 （JIT） 特权访问
* 日志分析和用于监视和警报的哨兵

## <a name="who-benefits-from-a-secure-workstation"></a>谁从安全工作站中获益？

所有用户和操作员在使用安全工作站时都受益。 危害 PC 或设备的攻击者可以模拟所有缓存的帐户。 登录到设备时，它们也可能使用凭据和令牌。 这种风险使得保护用于特权角色（包括管理权限）的设备非常重要。 具有特权帐户的设备是横向移动和特权升级攻击的目标。 这些帐户可用于各种资产，例如：

* 本地或基于云的系统的管理员
* 关键系统的开发人员工作站
* 高曝光率的社交媒体帐户管理员
* 高度敏感的工作站，如 SWIFT 支付终端
* 处理商业机密的工作站

为了降低风险，您应该对使用这些帐户的特权工作站实施提升的安全控制。 有关详细信息，请参阅 Azure[活动目录功能部署指南](../fundamentals/active-directory-deployment-checklist-p2.md)[、Office 365 路线图](https://aka.ms/o365secroadmap)[和保护特权访问路线图](https://aka.ms/sparoadmap)）。

## <a name="why-use-dedicated-workstations"></a>为什么要使用专用工作站？

虽然可以将安全性添加到现有设备，但最好从安全基础开始。 要使您的组织处于保持高安全级别的最佳位置，请从您知道安全的设备开始，并实施一组已知的安全控制。

通过电子邮件和 Web 浏览的攻击媒介数量不断增加，因此越来越难以确定设备可以受信任。 本指南假定专用工作站与标准生产力、浏览和电子邮件隔离开来。 从设备中删除生产力、Web 浏览和电子邮件可能会对工作效率产生负面影响。 但是，对于作业任务没有明确要求且安全事件风险很高的情况，此保护通常是可以接受的。

> [!NOTE]
> 此处的 Web 浏览是指对任意网站的一般访问，这些网站可能是高风险活动。 这种浏览与使用 Web 浏览器访问少量已知管理网站以访问 Azure、Office 365、其他云提供商和 SaaS 应用程序截然不同。

遏制策略通过增加阻止攻击者获取敏感资产的控制数量和类型来增强安全性。 本文中描述的模型使用分层权限设计，并将管理权限限制为特定设备。

## <a name="supply-chain-management"></a>供应链管理

安全工作站的基本要素是供应链解决方案，即使用称为"信任根"的可信工作站。 在选择信任硬件根目录时必须考虑的技术应包括现代笔记本电脑中包含的以下技术： 

* [受信任的平台模块 （TPM） 2.0](/windows-hardware/design/device-experiences/oem-tpm)
* [BitLocker 驱动器加密](/windows-hardware/design/device-experiences/oem-bitlocker)
* [UEFI 安全启动](/windows-hardware/design/device-experiences/oem-secure-boot)
* [通过 Windows 更新分发的驱动程序和固件](/windows-hardware/drivers/dashboard/understanding-windows-update-automatic-and-optional-rules-for-driver-distribution)
* [虚拟化和启用 HVCI](/windows-hardware/design/device-experiences/oem-vbs)
* [驱动程序和应用 HVCI 就绪](/windows-hardware/test/hlk/testref/driver-compatibility-with-device-guard)
* [Windows Hello](/windows-hardware/design/device-experiences/windows-hello-biometric-requirements)
* [DMA I/O 保护](/windows/security/information-protection/kernel-dma-protection-for-thunderbolt)
* [系统防护装置](/windows/security/threat-protection/windows-defender-system-guard/system-guard-how-hardware-based-root-of-trust-helps-protect-windows)
* [新式待机](/windows-hardware/design/device-experiences/modern-standby)

对于此解决方案，将使用[Microsoft 自动驾驶仪](/windows/deployment/windows-autopilot/windows-autopilot)技术部署信任根，硬件满足现代技术要求。 为了保护工作站，自动驾驶仪允许您利用 Microsoft OEM 优化的 Windows 10 设备。 这些设备处于制造商已知的良好状态。 自动驾驶仪可以将 Windows 设备转换为"业务就绪"状态，而不是重新映像潜在的不安全设备。 它应用设置和策略，安装应用，甚至更改 Windows 10 的版本。 例如，自动驾驶仪可能会将设备的 Windows 安装从 Windows 10 专业版更改为 Windows 10 企业版，以便它可以使用高级功能。

![安全工作站级别](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>设备角色和配置文件

本指南引用了几个安全配置文件和角色，这些配置文件和角色可帮助您为用户、开发人员和 IT 人员创建更安全的解决方案。 这些配置文件平衡了普通用户的可用性和风险，这些用户可以从增强型或安全工作站中受益。 此处提供的设置配置基于行业接受的标准。 本指南演示如何强化 Windows 10 并减少与设备或用户危害相关的风险。 为了利用现代硬件技术和信任设备的根源，我们将使用[设备运行状况证明](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Support-Tip-Using-Device-Health-Attestation-Settings-as-Part-of/ba-p/282643)，这是从**高安全性**配置文件开始启用的。 此功能可确保攻击者在设备的早期启动期间不能持久。 它通过使用策略和技术来帮助管理安全功能和风险。
![安全工作站级别](./media/concept-azure-managed-workstation/seccon-levels.png)

* **基本安全性**– 托管的标准工作站为大多数家庭和小型企业使用提供了良好的起点。 这些设备在 Azure AD 中注册，并在 Intune 中进行管理。 此配置文件允许用户运行任何应用程序并浏览任何网站。 应启用像 Microsoft [Defender](https://www.microsoft.com/windows/comprehensive-security)这样的反恶意软件解决方案。

* **增强的安全性**– 此入门级受保护解决方案适用于家庭用户、小型企业用户和一般开发人员。

   增强的工作站是提高低安全性安全性的基于策略的方法。 它提供了处理客户数据的安全方法，同时还使用电子邮件和 Web 浏览等生产力工具。 您可以使用审核策略和 Intune 监视增强的工作站的用户行为和配置文件使用情况。 使用 Windows10 （1809） 脚本部署增强的工作站配置文件，并使用[高级威胁防护 （ATP）](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)利用高级恶意软件防护。

* **高安全性**– 减少工作站攻击面的最有效方法是消除自管理工作站的能力。 删除本地管理权限是提高安全性的一个步骤，但如果实施不正确，可能会影响工作效率。 高安全性配置文件建立在增强的安全配置文件之上，并进行了大量更改：删除本地管理员。此配置文件专为高知名度用户设计：高管、工资和敏感数据用户、服务和流程审批人。

   高安全性用户需要一个更具控制性的环境，同时仍然能够以易于使用的体验执行电子邮件和 Web 浏览等活动。 用户期望功能，如 Cookie、收藏夹和其他快捷方式才能正常工作。 但是，这些用户可能不需要能够修改或调试其设备。 它们也不需要安装驱动程序。 使用高安全性 - Windows10 （1809） 脚本部署高安全性配置文件。

* **专门**– 攻击者以开发人员和 IT 管理员为目标，因为他们可以更改攻击者感兴趣的系统。 专用工作站通过管理本地应用程序和限制网站来扩展高安全性工作站的策略。 它还限制高风险的生产力功能，如 ActiveX、Java、浏览器插件和其他 Windows 控件。 使用 DeviceConfiguration_NCSC - Windows10 （1803） 安全基线脚本部署此配置文件。

* **安全**– 危害管理帐户的攻击者可能会因数据窃取、数据更改或服务中断而造成重大业务损害。 在此强化状态下，工作站启用所有限制直接控制本地应用程序管理的安全控制和策略。 安全的工作站没有生产力工具，因此设备更难妥协。 它阻止网络钓鱼攻击的最常见媒介：电子邮件和社交媒体。 安全工作站可以随安全工作站 - Windows10 （1809） 安全基线脚本一起部署。

   ![安全工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   安全工作站为管理员提供经过强化的工作站，该工作站具有清晰的应用程序控制和应用程序防护功能。 工作站使用凭据保护、设备保护和漏洞利用防护来保护主机免受恶意行为的影响。 所有本地磁盘也使用 BitLocker 进行加密。

* **隔离**– 此自定义脱机方案表示频谱的极端端。 此案例未提供安装脚本。 您可能需要管理需要不受支持或未修补的旧版操作系统的业务关键型功能。 例如，高价值生产线或生命支持系统。 由于安全性至关重要且云服务不可用，因此您可以手动管理和更新这些计算机，或者使用隔离的 Active Directory 林体系结构（如增强安全管理环境 （ESAE）） 来管理和更新这些计算机。 在这些情况下，请考虑删除除基本 Intune 和 ATP 运行状况检查之外的所有访问。

   * [Intune 网络通信要求](/intune/network-bandwidth-use)
   * [ATP 网络通信要求](/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>后续步骤

[部署安全的 Azure 管理工作站](howto-azure-managed-workstation.md)。
