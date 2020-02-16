---
title: 使用 Microsoft Cloud App Security 实现应用可见性和控制
description: 了解如何识别应用风险级别、实时阻止违规和透露，以及使用应用连接器通过提供程序 API 来实现可见性和监管。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069703"
---
# <a name="cloud-app-visibility-and-control"></a>云应用可见性和控制

若要充分利用云应用和服务，IT 团队必须在支持访问与保持对关键数据的控制和保护之间找到适当的平衡。 Microsoft Cloud App Security 针对数据的移动提供丰富的可见性和控制功能，并提供先进的分析工具来识别和应对所有 Microsoft 和第三方云服务中的网络威胁。

## <a name="discover-and-manage-shadow-it-in-your-network"></a>发现和管理网络中的影子 IT

让 IT 管理员说出他们认为员工使用了多少个云应用时，他们说出的平均数字是 30 到 40 个，但在现实中，组织中的员工使用的不同应用数平均超过 1,000 个。 影子 IT 可帮助你了解和识别正在使用的应用，以及面临的风险级别。 80% 的员工使用的应用未经批准（未经过任何人评审），可能不符合安全与合规策略。 此外，由于员工能够从企业网络外部访问资源和应用，因此，只是在防火墙中配置规则和策略不再足够。

使用 Microsoft Cloud App Discovery（一项 Azure Active Directory Premium P1 功能）可以发现正在使用的应用、探索这些应用的风险、配置策略来识别新的有风险应用，并取消批准这些应用，以使用代理或防火墙设备在本地将其阻止。

- 发现和识别影子 IT
- 评估和分析
- 管理应用
- 高级影子 IT 发现报告
- 控制已批准的应用
 
### <a name="learn-more"></a>了解详细信息

- [发现和管理网络中的影子 IT](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [使用 Cloud App Security 发现的应用](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>用户会话可见性和控制 

在当今的工作区中，知道云环境中幕后发生的情况往往并不足够。 需要实时阻止违规和信息透露，避免员工有意或无意中使数据和组织面临风险。 Microsoft Cloud App Security 与 Azure Active Directory (Azure AD) 相结合，可以在一体式的集成体验中通过条件访问应用控制提供这些功能。 

会话控制使用反向代理体系结构，并以独特的方式与 Azure AD 条件访问相集成。 使用 Azure AD 条件访问可以根据某些条件在组织的应用中强制实施访问控制。 条件定义了要向谁（用户或用户组）、什么（哪些云应用）和哪里（哪些位置和网络）应用条件访问策略。 确定条件后，可将用户路由到 Cloud App Security，在其中可以实时保护数据。  

通过这种控制，可以：  
- 控制文件下载
- 监视 B2B 方案  
- 控制对文件的访问  
- 下载时保护文档  
 
### <a name="learn-more"></a>了解详细信息

- [在 Cloud App Security 中使用会话控制保护应用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>高级应用可见性和控制 

应用连接器使用应用提供商的 API 通过 Microsoft Cloud App Security 对连接到的应用实现更高的可见性和控制力度。 Cloud App Security 利用云提供商提供的 API。 每个服务具有自身的框架和 API 限制，例如带宽限制、API 限制、动态时移 API 窗口，等等。 Cloud App Security 产品团队已通过这些服务优化 API 的使用并提供最佳性能。 考虑到服务对其 API 施加的不同限制，Cloud App Security 引擎将使用允许的最大容量。 某些操作（例如，扫描租户中的所有文件）需要大量的 API 调用，因此它们的执行阶段分散在更长的时段。 某些策略预期需要运行几个小时甚至几天。 
 
### <a name="learn-more"></a>了解详细信息  

- [在 Cloud App Security 中连接应用](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>后续步骤

- [发现和管理网络中的影子 IT](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [使用 Cloud App Security 发现的应用](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [在 Cloud App Security 中使用会话控制保护应用](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [在 Cloud App Security 中连接应用](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
