
---
title: "在 Azure Active Directory 中启用企业状态漫游 | Microsoft Docs"
description: "有关 Windows 设备中的企业状态漫游设置的常见问题。 企业状态漫游可跨 Windows 设备为用户提供统一体验，并减少配置新设备所需的时间。"
services: active-directory
keywords: "企业状态漫游, Windows 云, 如何启用企业状态漫游"
documentationcenter: 
author: femila
manager: swadhwa
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>在 Azure Active Directory 中启用企业状态漫游
企业状态漫游可供任何拥有高级 Azure Active Directory (Azure AD) 订阅的组织使用。 有关如何获取 Azure AD 订阅的更多详细信息，请参阅 [Azure AD 产品页](https://azure.microsoft.com/services/active-directory)。

启用企业状态漫游时，将自动授予你的组织对 Azure Rights Management 的免费有限使用订阅的许可证。 此免费订阅仅限于加密和解密由企业状态漫游服务同步的企业设置和应用程序数据；必须具有付费订阅才能使用 Azure Rights Management 的完整功能。

获取 Premium Azure AD 订阅后，请按照以下步骤启用企业状态漫游：

1. 登录到 Azure 经典门户。
2. 在左侧，选择“ACTIVE DIRECTORY”，然后选择要启用企业状态漫游的目录。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. 转到顶部的“配置”选项卡。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. 向下滚动页面，选择“用户可以同步设置和企业应用数据”，然后单击“保存”。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

若要使用企业状态漫游服务漫游 Windows 10 设备的设置，设备必须使用 Azure AD 标识对该设备进行身份验证。 对于已加入到 Azure AD 的设备，用户的主登录名为 Azure AD 标识，因此不需要其他配置。 对于使用传统本地 Active Directory 的设备，IT 管理员必须[将已加入域的设备连接到 Azure AD for Windows 10 体验](active-directory-azureadjoin-devices-group-policy.md)。

## <a name="sync-data-storage"></a>同步数据存储
企业状态漫游数据托管在一个或多个 [Azure 区域](https://azure.microsoft.com/regions/)中，这些区域与 Azure Active Directory 实例中设置的国家/地区值最为匹配。 企业状态漫游数据基于三个主要地理区域进行分区：北美、EMEA 和 APAC。 租户的企业状态漫游数据位于本地所在的地理区域，不会跨区域复制。  例如，将其国家/地区值设置为 EMEA 国家（如“法国”或“赞比亚”）的客户，其数据会托管在欧洲的一个或多个 Azure 区域。  将 Azure AD 中的国家/地区值设置为“美国”或“加拿大”等北美国家/地区的客户，其数据会托管在美国的一个或多个 Azure 区域。  将 Azure AD 中的国家/地区价值设置为“澳大利亚”或“新西兰”等 APAC 国家之一的客户，其数据会托管在亚洲的一个或多个 Azure 区域。  南美国家和南极洲的数据将会托管在美国的一个或多个 Azure 区域。  国家/地区值在 Azure AD 目录创建过程中设置，无法进行后续修改。 

如需有关数据存储位置的更多详细信息，请通过 [Azure 支持](https://azure.microsoft.com/support/options/)提交票证。

## <a name="manage-enterprise-state-roaming"></a>管理企业状态漫游
Azure AD 全局管理员可以在 Azure 经典门户中启用和禁用企业状态漫游。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

全局管理员可以将设置同步限制到特定安全组。

全局管理员还可在 Active Directory 实例的“用户”列表中选择特定用户，单击“设备”标签，并选择“同步设置和企业应用数据的设备”视图，从而查看每名用户的设备同步状态报告。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>数据保留
通过企业状态漫游与 Azure 同步的数据将无限期保留，除非执行手动删除操作或相关数据被确定为过时。 

**显式删除**：当 Azure 管理员删除用户或目录，或管理员显式请求删除数据时，将删除数据。

* **用户删除**：在 Azure AD 中删除用户时，会将用户帐户漫游数据标记为待删除，并在 90 到 180 天内将其删除。 
* **目录删除**：在 Azure AD 中删除整个目录是一项即时操作。 会将与该目录关联的所有设置数据标记为待删除，并在 90 到 180 天内将其删除。 
* **按请求删除**：如果 Azure AD 管理员要手动删除特定用户的数据或设置数据，可以通过 [Azure 支持](https://azure.microsoft.com/support/)提交票证。 

**过时数据删除**：一年（“保留期”）内未访问的数据将被视为过时，可能会从 Azure 中删除。 保留期可能会发生变化，但不会少于 90 天。 过时数据可以是一组特定的 Windows/应用程序设置或用户的所有设置。 例如：

* 如果没有设备访问特定设置集合（例如从设备中删除了应用程序，或者对某用户的所有设备禁用了“主题”等设置组），则该集合将在保留期后变为过时，并可能被删除。 
* 如果用户已关闭其所有设备上的设置同步，则不会访问任何设置数据，同时该用户的所有设置数据将会过时，并可以在保留期后被删除。 
* 如果 Azure AD 目录管理员关闭整个目录的企业状态漫游，则该目录中的所有用户将停止同步设置，所有用户的所有设置数据都将过时，并可能在保留期后被删除。 

**恢复已删除数据**：无法配置数据保留策略。 永久删除数据后，即无法将其恢复。 但是，请注意，设置数据只会从 Azure 中删除，而不会从最终用户设备中删除。 如果任何设备稍后重新连接到企业状态漫游服务，将再次同步设置并将其存储在 Azure 中。

## <a name="related-topics"></a>相关主题
* [企业状态漫游概述](active-directory-windows-enterprise-state-roaming-overview.md)
* [设置和数据漫游的常见问题](active-directory-windows-enterprise-state-roaming-faqs.md)
* [设置同步的组策略和 MDM 设置](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 漫游设置参考](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [故障排除](active-directory-windows-enterprise-state-roaming-troubleshooting.md)



<!--HONumber=Jan17_HO1-->


