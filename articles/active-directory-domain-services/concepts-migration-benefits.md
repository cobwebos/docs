---
title: Azure AD 域服务中经典部署迁移的优点 | Microsoft Docs
description: 详细了解将 Azure Active Directory 域服务的经典部署迁移到资源管理器部署模型的优点
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 93dcc1202c08be905cf08513f38e79a8a7674e01
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650121"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>在 Azure Active Directory 域服务中从经典部署模型迁移到资源管理器部署模型的优点

通过 Azure Active Directory 域服务 (Azure AD DS)，你可以将使用经典部署模型的现有托管域迁移到资源管理器部署模型。 使用资源管理器部署模型的 Azure AD DS 托管域提供其他功能，例如细化密码策略、审核日志和帐户锁定保护。

本文概述了迁移的优点。 首先，请参阅[将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器][howto-migrate]。

> [!NOTE]
> 在 2017 年，Azure AD 域服务变得可以在 Azure 资源管理器网络中托管。 自那时起，我们就能够使用 Azure 资源管理器的新式功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，因此 Azure AD DS 经典虚拟网络部署将于 2023 年 3 月 1 日停用。
>
> 有关详细信息，请查看[正式的弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>迁移优点

迁移过程采用从使用经典部署模型转变为使用资源管理器部署模型的现有托管域。 将托管域从经典部署模型迁移到资源管理器部署模型时，无需将计算机重新加入该托管域，也无需删除该托管域后再重头开始创建一个托管域。 迁移过程结束时，VM 会继续加入该托管域。

迁移后，Azure AD DS 可提供许多功能，这些功能仅适用于使用资源管理器部署模型的域，如下所示：

* [细化密码策略支持][password-policy]。
* 加快 Azure AD 和 Azure AD 域服务之间的同步速度。
* 可[从 Azure AD 同步的两个新属性][attributes] - manager 和 employeeID 。
* [升级 SKU][skus] 时，可访问功能更强大的域控制器。
* AD 帐户锁定保护。
* [关于托管域的警报的电子邮件通知][email-alerts]。
* [使用 Azure 工作簿和 Azure Monitor 查看审核日志和登录活动][workbooks]。
* 在支持的区域中， [Azure 可用性区域][availability-zones]。
* 与其他 Azure 产品（如 [Azure 文件][azure-files]、 [高清 Insights][hd-insights]和 [Windows 虚拟桌面][wvd]）的集成。
* 支持人员可以访问更多遥测并可以帮助更有效地解决问题。
* 使用 [Azure 托管磁盘][managed-disks]对托管域控制器上的数据进行静态加密。

使用资源管理器部署模型的托管域有助于随时了解最新功能。 新功能不适用于使用经典部署模型的托管域。

## <a name="next-steps"></a>后续步骤

首先，请参阅[将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器][howto-migrate]。

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
