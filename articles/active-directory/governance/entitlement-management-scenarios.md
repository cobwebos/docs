---
title: 在 Azure AD 授权管理 （预览版）-Azure Active Directory 中的常见方案
description: 了解应遵循针对 Azure Active Directory 权利管理 （预览版） 中的常见方案的高级步骤。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d54d51a16f4ed94f24f9afa005c63b22778cf9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190350"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>在 Azure AD 授权管理 （预览版） 中的常见方案

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有几种方法，您可以为你的组织中配置权利管理。 但是，如果您刚开始，则有助于您了解有关管理员、 审批者和请求者的常见方案。

## <a name="administrators"></a>管理员

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>我刚刚接触权利管理，并且我想入门的帮助

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | [请按照教程，以创建第一个访问包](entitlement-management-access-package-first.md) | [![Azure 门户的图标](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要允许我的目录来请求访问组、 应用程序或 SharePoint 站点中的用户

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [在目录中创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package) | ![创建访问包](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [添加资源角色，以访问包](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>组</li><li>应用程序</li><li>SharePoint 站点</li></ul> | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [添加策略](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>在目录中的用户</li><li>需要审批</li><li>过期设置</li></ul> | ![添加策略](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>我想要允许用户从我的业务合作伙伴目录 （我的目录中尚不包括用户） 组、 应用程序或 SharePoint 站点的请求访问

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [在目录中创建新的访问包](entitlement-management-access-package-create.md#start-new-access-package) | ![创建访问包](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [添加资源角色，以访问包](entitlement-management-access-package-edit.md#add-resource-roles) | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [添加用于外部用户的策略](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>不在目录中的用户</li><li>需要审批</li><li>过期设置</li></ul> | ![添加为外部用户的策略](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [发送我访问门户链接，以请求为您的业务合作伙伴访问包](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>业务合作伙伴可以与他们的用户共享链接</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>我想要更改的组、 应用程序或访问包中的 SharePoint 站点

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** 打开访问包 | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [添加或删除资源角色](entitlement-management-access-package-edit.md#add-resource-roles) | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>我想要查看谁具有组、 应用程序，或 SharePoint 站点的分配

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** 打开访问包 | ![添加资源角色](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [查看分配](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>哪些用户有权访问包的视图</li><li>查看哪些用户的访问权限已过期</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>我想要查看组、 应用程序或用户有权访问的 SharePoint 站点

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | [查看用户分配的报告](entitlement-management-reports.md)<ul><li>当所请求的视图和谁批准</li></ul> |  |

## <a name="approvers"></a>审批者

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>我想要批准请求，从而访问组、 应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [我访问门户中打开请求](entitlement-management-request-approve.md#open-request) | [![我访问门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [批准访问请求](entitlement-management-request-approve.md#approve-or-deny-request) | ![批准访问](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>请求者

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>我想要查看组、 应用程序或提供给我的 SharePoint 站点和请求的访问权限

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [登录到我访问门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我访问门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 查找访问包 |  |
> | **3.** [请求访问权限](entitlement-management-request-access.md#request-an-access-package) | ![请求访问权限](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>我是外部用户，我想要请求的访问权限组、 应用程序或 SharePoint 站点具有直接链接

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [查找你收到了我访问门户链接](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [登录到我访问门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我访问门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [请求访问权限](entitlement-management-request-access.md#request-an-access-package) | ![请求访问权限外部用户](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>我想要查看我已有权访问的组、 应用程序或 SharePoint 站点

> [!div class="mx-tableFixed"]
> | Steps | 示例 |
> | --- | --- |
> | **1.** [登录到我访问门户](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![我访问门户图标](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** 查看活动的访问权限的包 |  |

## <a name="next-steps"></a>后续步骤

- [教程：创建第一个访问包](entitlement-management-access-package-first.md)
- [委派任务](entitlement-management-delegate.md)
