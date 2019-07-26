---
title: Azure AD 授权管理 (预览版) 中的常见方案-Azure Active Directory
description: 了解 Azure Active Directory 授权管理 (预览版) 中常见方案的高级步骤。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8e7709abcc97baac9bf657b9fff2110cb2e72c1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489036"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Azure AD 授权管理 (预览版) 中的常见方案

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以通过多种方式为组织配置权限管理。 但是, 如果您刚开始工作, 则了解管理员、审批者和请求者的常见方案将很有帮助。

## <a name="administrators"></a>管理员

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>我是对权利管理的新手, 我想了解入门信息

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | [遵循教程创建第一个访问包](entitlement-management-access-package-first.md) | [![Azure 门户图标](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我希望允许目录中的用户请求访问组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [在目录中创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package) | ![创建访问包](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [将资源角色添加到 access 包](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>个组</li><li>应用程序</li><li>SharePoint 站点</li></ul> | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [添加策略](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>适用于目录中的用户</li><li>需要审批</li><li>过期设置</li></ul> | ![添加策略](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我希望允许来自我的企业合作伙伴目录 (包括我的目录中的用户) 的用户请求访问组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [在目录中创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package) | ![创建访问包](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [将资源角色添加到 access 包](entitlement-management-access-package-edit.md#add-resource-roles) | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [为外部用户添加策略](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>对于不在你的目录中的用户</li><li>需要审批</li><li>过期设置</li></ul> | ![为外部用户添加策略](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [发送 "我的访问门户" 链接以向业务合作伙伴请求访问包](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>业务合作伙伴可以与其用户共享链接</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>我想更改访问包中的组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** 打开访问包 | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [添加或删除资源角色](entitlement-management-access-package-edit.md#add-resource-roles) | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>我想要查看谁具有对组、应用程序或 SharePoint 站点的分配

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** 打开访问包 | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [查看分配](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>查看哪些用户有权访问访问包</li><li>查看用户的访问权限已过期</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>我想要查看用户有权访问的组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | [查看用户分配报表](entitlement-management-reports.md)<ul><li>查看他们的请求时间和批准者</li></ul> |  |

## <a name="approvers"></a>审批者

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>我想批准访问组、应用程序或 SharePoint 站点的请求

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [在我的访问门户中打开请求](entitlement-management-request-approve.md#open-request) | [![我的 Access 门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [批准访问请求](entitlement-management-request-approve.md#approve-or-deny-request) | ![批准访问](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>请求者

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>我想要查看可用的组、应用程序或 SharePoint 站点并请求访问权限

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [登录到 "我的 Access" 门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我的 Access 门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 查找访问包 |  |
> | **3.** [请求访问权限](entitlement-management-request-access.md#request-an-access-package) | ![请求访问](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>我是外部用户, 我想通过直接链接请求访问组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [查找收到的 "我的访问门户" 链接](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [登录到 "我的 Access" 门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我的 Access 门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [请求访问权限](entitlement-management-request-access.md#request-an-access-package) | ![请求访问外部用户](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>我想要查看已有权访问的组、应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | 步骤 | 示例 |
> | --- | --- |
> | **1.** [登录到 "我的 Access" 门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我的 Access 门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 查看活动访问包 |  |

## <a name="next-steps"></a>后续步骤

- [教程：创建首个访问包](entitlement-management-access-package-first.md)
- [委托任务](entitlement-management-delegate.md)
