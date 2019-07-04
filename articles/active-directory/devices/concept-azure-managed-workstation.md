---
title: 了解安全的 Azure 托管的工作站的 Azure Active Directory
description: 了解有关安全的 Azure 托管的工作站，并了解为什么它们很重要。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02a6ddef294c4872f2d7e50e8940ecbb4b4b7bc4
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491588"
---
# <a name="understand-secure-azure-managed-workstations"></a>了解安全的 Azure 托管的工作站

安全且隔离的工作站是极为重要的安全敏感的角色，如管理员、 开发人员和关键服务操作员。 如果客户端工作站的安全性受到威胁，很多安全控制和保证可以失败或无效。

本文档介绍所需的构建安全的工作站，通常称为特权的访问工作站 (PAW)。 该文章还包含有关设置初始安全控制的详细的说明。 本指南介绍了如何基于云的技术可以管理服务。 它依赖于 Windows 10RS5、 Microsoft Defender 高级威胁防护 (ATP)、 Azure Active Directory 和 Intune 中引入的安全功能。

> [!NOTE]
> 此文章介绍了安全的工作站和其重要性的概念。 如果您已熟悉这一概念，并且想要跳过部署，请访问[部署安全工作站](https://docs.microsoft.com/azure/active-directory/devices/howto-azure-managed-workstation)。

## <a name="why-secure-workstation-access-is-important"></a>安全的工作站访问为何重要

快速采用云服务和能够从任何位置创建了新的利用该方法。 通过利用其中管理员工作的设备上的薄弱的安全控件，攻击者可以访问特权资源。

特权滥用和供应链攻击是攻击者使用违反组织的前五个方法。 它们也是第二个最常检测到在 2018 年根据报告的事件策略[Verizon 威胁报告](https://enterprise.verizon.com/resources/reports/dbir/)，并[安全研究报告](https://aka.ms/sir)。

大多数攻击者执行以下步骤：

1. 若要找到一种方法中，通常特定于行业的侦测。
1. 分析，以收集信息并确定最佳的方式来渗透的工作站，被视为较低的值。
1. 暂留以寻找一种方法来移动[横向](https://en.wikipedia.org/wiki/Network_Lateral_Movement)。
1. 数据泄露的机密和敏感。

期间的侦测，攻击者经常渗入看起来低风险或遭到轻视的设备。 若要找到横向移动的机会和查找管理用户和设备，他们会使用这些易受攻击的设备。 他们就可以访问特权的用户角色后，则攻击者确定高价值数据并成功盗取该数据。

![典型的破坏模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文档介绍了解决方案，可帮助防止此类横向攻击计算设备。 管理和不太有价值的工作效率设备，有权访问敏感的云资源的设备可以渗透之前中断链中的服务，该解决方案将隔离开来。 该解决方案使用本机 Microsoft 365 企业版堆栈的一部分的 Azure 服务：

* Intune 设备管理和安全的应用程序和 Url 列表
* Autopilot 设备安装程序、 部署和刷新
* Azure AD 进行用户管理、 条件性访问和多重身份验证
* （当前版本） 适用于 Windows 10 设备运行状况证明和用户体验
* Defender ATP 的云管理的终结点保护、 检测和响应
* 用于管理授权和实时 (JIT) 的 azure AD PIM 的特权资源的访问权限

## <a name="who-benefits-from-a-secure-workstation"></a>用户得益于工作站的安全？

所有用户和运算符中都获益时使用的是安全的工作站。 如果攻击者危及 PC 或设备可以模拟所有缓存的帐户。 当登录到设备，它们可能会使用凭据和令牌。 这种风险到用于特权角色，包括管理权限的安全设备使得很重要。 使用特权帐户的设备是横向移动和特权提升攻击的目标。 这些帐户可用于各种资源包括：

* 在本地或基于云的系统的管理员
* 关键系统的开发人员工作站
* 以高暴露风险的社交媒体帐户管理员
* 高度敏感的工作站，如终端 SWIFT 付款
* 工作站处理商业机密

为了降低风险，应实现提升的安全控制特权的工作站，请使用这些帐户。 有关详细信息，请参阅[Azure Active Directory 功能部署指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)， [Office 365 路线图](https://aka.ms/o365secroadmap)，并[保护特权访问路线图](https://aka.ms/sparoadmap))。

## <a name="why-use-dedicated-workstations"></a>为什么要使用专用的工作站？

可以将安全添加到现有的设备时，是更好的做法开始使用安全的基础。 若要将你的组织中维护一个高安全级别的最佳位置，开始与你知道是安全设备，并实现一组已知的安全控件。

越来越多的通过电子邮件和 web 浏览的攻击媒介会越来越难以确保设备可以是受信任。 本指南假定专用的工作站都独立于标准的工作效率、 浏览和电子邮件。 删除的工作效率、 浏览 web，以及从设备的电子邮件可以对工作效率产生负面影响。 但是，这种保护措施是通常情况下，作业任务不明确要求它并且安全事件的风险较高的可接受的。

> [!NOTE]
> Web 浏览此处引用的常规访问权限可以是高风险活动的任意网站。 此类浏览是使用 web 浏览器访问少量的已知的服务，如 Azure、 Office 365、 其他云提供程序和 SaaS 应用程序的管理网站组完全不同。

包含策略通过增加的数量和类型从获取敏感资产的访问权限阻止攻击者的控件的增强的安全性。 在本文中所述的模型使用分层的特权设计，并将管理权限限制到特定设备。

## <a name="supply-chain-management"></a>供应链管理

至关重要的受保护的工作站是信任的使用名为根的受信任的工作站的其中一个供应链解决方案。 对于此解决方案，使用信任的根[Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)技术。 若要保护工作站，Autopilot 可以利用 Microsoft OEM 优化 Windows 10 设备。 这些设备制造商提供的有已知的良好状态。 而不是重置映像可能不安全的设备，Autopilot 可以将 Windows 设备转换为"业务就绪"状态。 它将应用设置和策略、 安装应用程序，并甚至更改的 Windows 10 版本。 例如，Autopilot 可能会更改设备的 Windows 安装从 Windows 10 专业版到 Windows 10 企业版，以便它可以使用高级的功能。

![保护工作站级别](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>设备角色和配置文件

本指南引用多个安全配置文件，并可帮助你的角色创建的用户、 开发人员和 IT 人员更安全的解决方案。 这些配置文件权衡可用性和多都可以受益于增强或安全工作站的用户的风险。 此处提供的配置基于行业公认标准的设置。 本指南演示如何来强化 Windows 10 并减少与设备或用户泄漏风险。 它会通过使用策略和技术来帮助管理的安全功能和风险。
![保护工作站级别](./media/concept-azure-managed-workstation/seccon-levels.png)

* **低安全**– 托管的标准工作站大多数家庭和小型企业用于提供很好的起点。 在 Azure AD 中注册和使用 Intune 管理这些设备。 此配置文件允许用户运行任何应用程序和浏览的任何网站。 反恶意软件解决方案喜欢[Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security)应启用。

* **增强的安全性**– 此入门级的受保护的解决方案是适用于家庭用户、 小型企业用户和常规开发人员。

   增强的工作站是基于策略的方法，来提高较低的安全配置文件的安全性。 它提供了处理客户数据，同时也使用电子邮件和 web 浏览等高效工具的安全方法。 您可以使用审核策略和 Intune 来监视用户行为和配置文件使用情况的增强型的工作站。 部署的增强型的工作站配置文件的 Windows10 (1809) 脚本，并利用高级恶意软件保护使用的[高级威胁防护 (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)。

* **高安全性**– 若要减少攻击面的工作站的最有效方法是删除上下工作站的功能。 删除本地管理权限的步骤，可以提高安全性，但如果未正确实现，它可能会影响工作效率。 高安全配置文件生成一个相当大的变化的增强的安全配置文件： 删除的本地管理员。此配置文件专用于高配置文件： 高级管理人员、 工资和用户的敏感数据、 服务和流程的审批者。

   高安全性用户要求同时仍然能够执行活动，例如电子邮件和 web 浏览中使用简单的体验更容易控制的环境。 用户期望功能，例如 cookie、 收藏夹以及其他快捷方式工作。 但是，这些用户可能不需要修改或调试其设备的功能。 它们还不需要安装驱动程序。 使用高安全性-Windows10 (1809) 脚本来部署的高安全配置文件。

* **专用化**– 攻击者以开发人员和 IT 管理员，因为它们可以改变系统的攻击者感兴趣。 专用的工作站上的高安全性工作站的策略扩展，通过管理本地应用程序并将网站限制。 它还会限制高风险的工作效率功能，例如 ActiveX、 Java、 浏览器插件和其他 Windows 控件。 部署此配置文件与 DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline 脚本。

* **保护**– 的攻击者威胁管理帐户可能会导致数据被盗，数据更改类型或服务中断的重要业务损失。 在此强化状态下，工作站可以实现所有的安全控制和限制的本地应用程序管理的直接控制的策略。 受保护的工作站具有任何工作效率工具，因此更难以破坏的设备。 它将阻止网页仿冒攻击的最常见向量： 电子邮件和社交媒体。  可以使用安全工作站的 Windows10 (1809) SecurityBaseline 脚本部署受保护的工作站。

   ![受保护的工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   工作站的安全提供了具有明确的应用程序控件和应用程序防护的强化工作站的管理员。 工作站使用凭据保护、 device guard 和攻击防护来保护主机的恶意行为。 所有本地磁盘还使用 BitLocker 加密。

* **独立**– 这种情况下自定义的脱机表示极端极端情况下的结束。 这种情况下提供了没有安装脚本。 您可能需要管理需要不受支持或未安装修补程序的旧操作系统的关键业务函数。 例如，高价值生产订单行或生命-支持系统。 因为安全很关键，云服务都不可用，你可以管理和更新这些计算机手动或通过独立的 Active Directory 林体系结构如增强的安全管理环境 (ESAE)。 在这些情况下，请考虑删除除基本 Intune 和 ATP 运行状况检查的所有访问。

  * [Intune 网络通信要求](https://docs.microsoft.com/intune/network-bandwidth-use)
  * [ATP 网络通信要求](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>后续步骤

[部署 Azure 托管的安全工作站](howto-azure-managed-workstation.md)。
