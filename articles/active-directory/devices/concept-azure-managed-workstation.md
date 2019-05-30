---
title: 为什么安全工作站是重要的 Azure Active Directory
description: 了解为什么组织应创建安全的 Azure 托管的工作站
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
ms.openlocfilehash: b7e9ca9fa26e9744eb0a9bfafe692a096825b0b5
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357055"
---
# <a name="building-secure-workstations"></a>构建安全的工作站

受保护的独立的工作站是极为重要的安全敏感的角色，如管理员、 开发人员和运算符的关键服务。 许多其他安全控制和保证操作会失败或不不起任何作用，如果基础客户端工作站安全已遭到破坏。

本文档介绍如何生成详细的分步说明，包括如何设置启动安全控制的安全客户端工作站。 此类型的工作站有时被称为特权的访问工作站 (PAW)，它使用，并基于此引用。 但是的指南为基于云的技术来管理服务，看起来和引入了安全功能引入了从 Windows 10RS5、 Microsoft Defender ATP、 Azure Active Directory 和 Intune。

## <a name="why-securing-workstation-access-is-important"></a>保护工作站访问为何重要

快速采用云服务和能够从任何位置创建了利用此漏洞的新方法。 攻击者所利用薄弱的安全控件台设备的管理员工作，并能够访问特权资源。

如中所述[Verizon 威胁报告](https://enterprise.verizon.com/resources/reports/dbir/)，并[安全研究报告](https://aka.ms/sir)特权误用，和供应链攻击是用来破坏的组织的前五个机制和第二个最常检测到策略报告在 2018 年的事件。

大多数攻击者按照下面的路径：

* 侦测，通常特定于行业，若要找到一种方法中的使用启动
* 分析收集的信息来标识获取访问权限 （渗透） 的感知低价值工作站的最佳方法
* 持久性和查看方式移动[横向](https://en.wikipedia.org/wiki/Network_Lateral_Movement)
* 盗取机密和敏感数据

攻击者经常渗入看起来低风险或设备遭到轻视的侦测。 然后使用这些易受攻击的设备来找到横向移动的机会，查找管理用户和设备以及确定在高价值的数据，到成功盗取信息后他们获得这些权限的用户角色。

![典型的破坏模式](./media/concept-azure-managed-workstation/typical-timeline.png)

本文档提供了解决方案来帮助隔离管理和服务来帮助保护免受横向移动，从而保护你的计算设备或攻击不太有价值的工作效率设备。 设计可帮助减少成功通过中断之前使用用于管理或访问敏感的云资源的设备的渗透链执行违规行为的能力。 所述的解决方案将利用本机 Azure 服务属于 Microsoft 365 企业版堆栈包括：

* Intune 设备管理，包括应用程序和 URL 列入白名单
* 有关设备安装和部署和刷新的 autopilot 
* Azure AD 进行用户管理、 条件性访问和多重身份验证
* （当前版本） 适用于 Windows 10 设备运行状况证明和用户体验
* Microsoft Defender 高级威胁防护 (ATP) 的终结点保护、 检测和响应通过云管理
* 用于管理授权，包括实时 (JIT) 的 azure AD PIM 的特权资源的访问权限

## <a name="who-benefit-from-using-a-secure-workstation"></a>用户从使用安全工作站中受益

所有用户和运算符使用工作站的安全都受益。 如果攻击者入侵 PC 或设备可以执行多项操作，包括模拟所有缓存的帐户，并使用凭据，并选择他们登录时使用该设备上的标记。 这种风险，可以保护任何特权角色包括管理权限很重要，因为设备使用特权的帐户的位置是横向移动和特权提升攻击目标所使用的设备。 这些帐户可用于各种资源包括：

* 在本地和基于云的系统的管理员
* 关键系统的开发人员工作站
* 具有高暴露程度的社交媒体帐户管理员
* 如 SWIFT 付款终端高度敏感的工作站
* 处理商业机密的工作站

Microsoft 建议实施特权工作站使用这些帐户以降低风险的提升的安全控制。 可以中找到更多指导[Azure Active Directory 功能部署指南](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2)， [Office 365 路线图](https://aka.ms/o365secroadmap)，并[保护特权访问路线图](https://aka.ms/sparoadmap))。

## <a name="why-dedicated-workstations"></a>为什么专用工作站

可以将安全添加到现有的设备时，是更好的做法开始使用安全的基础。 从已知良好的设备和一组已知的安全控件将你的组织中的最佳位置，若要维护的增加安全的级别。 与允许的临时电子邮件和 web 浏览的攻击媒介的数量的不断增长，很越来越难确保设备可以是受信任。 假设专用工作站下的本指南适用标准的工作效率，浏览，分离，并完成电子邮件任务。 删除的工作效率、 浏览 web，以及从设备的电子邮件可以产生负面影响工作效率，但这种保护措施是通常可以接受的情况下，作业任务不明确要求它并且安全事件的风险较高。

> [!NOTE]
> Web 浏览此处是指对任意网站，这是高风险使用 web 浏览器访问少量的服务，如 Azure、 Office 365、 其他云提供程序和 SaaS 的已知管理网站的截然不同的常规访问应用程序。

包含策略通过增加的数量和类型的攻击者必须克服才能访问敏感资产的控件提供增强的安全保证。 此处开发模型提供了包含使用分层的特权模型的特定设备的管理权限。

## <a name="supply-chain-management"></a>供应链管理

至关重要的受保护的工作站是供应链解决方案则使用的工作站是受信任，根的信任。 此解决方案所要解决使用信任的根[Microsoft Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)技术。 对于受保护的工作站 Microsoft Autopilot 提供了能够充分利用 Microsoft OEM 优化 Windows 10 设备的制造商提供已知的良好状态。 而不是重置映像可能不受信任的设备，Microsoft Autopilot 可以转换为 Windows 设备的"业务就绪"状态，应用设置和策略，安装应用，以及甚至更改的 Windows 10 版本 （例如，从正在使用Windows 10 专业版到 Windows 10 企业版，以支持高级的功能)。

![保护工作站级别](./media/concept-azure-managed-workstation/supplychain.png)

## <a name="device-roles-and-profiles"></a>设备角色和配置文件

在指南中，将解决多个安全配置文件和角色以实现更安全的解决方案为用户、 开发人员和 IT 操作人员。 这些配置文件具有已对齐以支持常见的用户在组织中，都可以受益于增强，或安全工作站上，同时平衡易用性和风险。 本指南将提供基于行业接受标准的设置的配置。 本指南用于说明在强化 Windows 10 和减少与使用策略和技术来帮助管理的安全功能和风险的设备或用户泄漏风险的方法。
![保护工作站级别](./media/concept-azure-managed-workstation/seccon-levels.png)

* **低安全**– 托管的标准工作站大多数家庭和小型企业用于提供很好的起点。 这些设备是已注册 Azure AD 和 Intune 托管。 该配置文件允许用户运行任何应用程序和浏览的任何网站。 反恶意软件解决方案喜欢[Microsoft Defender](https://www.microsoft.com/windows/comprehensive-security)应启用。
* **增强的安全性**– 是面向家庭用户、 小型企业用户，以及常规的开发人员很好的入门级保护解决方案。
   * 增强型工作站提供了基于策略的方法以增强低安全配置文件的安全性。 此配置文件允许使用客户数据，并且能够使用的工作效率工具，如检查电子邮件和 web 浏览的安全方法。 增强型工作站可以用于通过启用审核策略，并记录到 Intune 审核用户行为和配置文件使用的工作站。 在此配置文件，工作站将启用安全控制和策略所述在内容中，并可部署在增强型工作站的 Windows10 (1809) 脚本。 部署还会利用的高级恶意软件保护使用[高级威胁防护 (ATP)](https://docs.microsoft.com/office365/securitycompliance/office-365-atp)
* **高安全性**– 若要减少攻击面的工作站的最有效方法是删除上下工作站的功能。 删除本地管理权限是可以增强安全性，一个步骤，如果无法正确地实现可能会影响工作效率。 高安全配置文件为基础的增强的安全配置文件的一个相当大的更改，删除的本地管理员。此配置文件旨在帮助可能是高级管理人员如一个高配置文件用户或用户可能具有的与敏感数据，例如工资单、 或批准的服务和进程的用户。
   * 高安全性用户配置文件要求的更高版本的控制的环境同时仍然能够执行其工作效率的活动，例如邮件和 web 浏览时维护一个简单的使用体验。 用户期望功能，例如 cookie、 收藏夹以及其他可用于操作的快捷方式。 但是这些用户可能不需要修改，或调试其设备的功能，并且不需要安装驱动程序。 使用高安全性-Windows10 (1809) 脚本来部署的高安全配置文件。
* **专用化**– 开发人员和 IT 管理员是攻击者的目标，如这些角色可以更改与攻击者感兴趣的系统。 专用的工作站采用高安全性工作站和 ActiveX，如通过管理的本地应用程序、 限制 internet web 站点和限制较高的工作效率增强功能的安全风险的进一步 emphases 中部署的工作量Java、 浏览器插件的和 Windows 设备上的多个其他已知的高风险控件。 在此配置文件，工作站将启用安全控制和策略所述在内容中，并可部署在 DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline 脚本。
* **保护**– 的攻击者可以破坏的管理帐户通常会导致数据被盗，数据更改类型或服务中断的重要业务损失。 在此强化状态下，工作站将使所有安全控制和限制的本地应用程序管理的直接控制的策略，并删除的工作效率工具。 因此，会影响设备进行难度作为电子邮件，社交媒体会阻止其反映网页仿冒攻击可能会成功的最常见方式。  可以使用安全工作站的 Windows10 (1809) SecurityBaseline 脚本部署受保护的工作站。

   ![受保护的工作站](./media/concept-azure-managed-workstation/secure-workstation.png)

   工作站的安全提供管理员具有明确的应用程序控件和应用程序防护的强化的工作站。 工作站将使用凭据、 设备和攻击防护保护免受恶意行为的主机。 此外所有本地磁盘加密与 Bitlocker 加密。

* **独立**– 此自定义的脱机情况下表示极端结束极端情况下 （这种情况下提供了任何安装脚本）。 组织可能需要管理的独立的业务关键函数如高价值生产订单行或生命周期支持系统需要不受支持/未修补的旧操作系统。 因为安全很关键，云服务都不可用，组织可能会手动管理/更新这些计算机或使用独立的 Active Directory 林体系结构 (如增强的安全管理环境 (ESAE)) 来管理它们。 在这些情况下，删除所有访问除基本 Intune 和 ATP 运行状况检查应考虑。
   * [Intune 网络通信要求](https://docs.microsoft.com/intune/network-bandwidth-use)
   * [ATP 网络通信要求](https://docs.microsoft.com/azure-advanced-threat-protection/configure-proxy)

## <a name="next-steps"></a>后续步骤

[部署 Azure 托管工作站的安全](howto-azure-managed-workstation.md)
