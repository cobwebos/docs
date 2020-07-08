---
title: Azure AD 域服务中的经典部署迁移的优点 |Microsoft Docs
description: 详细了解将 Azure Active Directory 域服务的经典部署迁移到资源管理器部署模型的好处
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 837ca3afaf014c41fded09fa8342eed1c13e5dcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734922"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Azure Active Directory 域服务中从经典部署模型迁移到资源管理器部署模型的优点

Azure Active Directory 域服务（Azure AD DS）允许你将使用经典部署模型的现有托管域迁移到资源管理器部署模型。 使用资源管理器部署模型 Azure AD DS 托管域提供其他功能，例如细化密码策略、审核日志和帐户锁定保护。

本文概述了迁移的好处。 若要开始，请参阅将[Azure AD 域服务从经典虚拟网络模型迁移到资源管理器][howto-migrate]。

> [!NOTE]
> 在2017中，Azure AD 域服务变为可在 Azure 资源管理器网络中托管。 自那时起，我们能够使用 Azure 资源管理器的新式功能构建更安全的服务。 由于 Azure 资源管理器部署完全取代了经典部署，Azure AD DS 经典虚拟网络部署将于2023年3月1日停用。
>
> 有关详细信息，请参阅[官方弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>迁移权益

迁移过程采用使用经典部署模型的现有托管域，并移动到使用资源管理器部署模型。 将托管域从经典部署模型迁移到资源管理器部署模型时，无需将计算机重新加入托管域或删除托管域，并从头开始创建一个。 在迁移过程结束时，Vm 将继续加入到托管域。

迁移后，Azure AD DS 提供了许多功能，这些功能仅适用于使用资源管理器部署模型的域，如下所示：

* [细化密码策略支持][password-policy]。
* Azure AD 和 Azure AD 域服务之间的同步速度更快。
* 两个新[属性，它们从 Azure AD][attributes]  -  *manager*和*雇员 id*同步。
* [升级 SKU][skus]时，可以访问更高容量的域控制器。
* AD 帐户锁定保护。
* [托管域上警报的电子邮件通知][email-alerts]。
* [使用 Azure 工作簿和 azure monitor 查看审核日志和登录活动][workbooks]。
* 在支持的区域中， [Azure 可用性区域][availability-zones]。
* 与其他 Azure 产品（如[Azure 文件][azure-files]、[高清 Insights][hd-insights]和[Windows 虚拟桌面][wvd]）的集成。
* 支持人员可以访问更多遥测数据，并有助于更有效地解决问题。
* 使用[Azure 托管磁盘][managed-disks]对托管域控制器上的数据进行静态加密。

使用资源管理器部署模型的托管域可帮助你随时了解最新的新功能。 新功能不可用于使用经典部署模型的托管域。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅将[Azure AD 域服务从经典虚拟网络模型迁移到资源管理器][howto-migrate]。

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
[managed-disks]: ../virtual-machines/windows/managed-disks-overview.md
