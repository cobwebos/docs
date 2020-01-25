---
title: Azure AD 中的企业应用的用户预配管理
description: Azure Active Directory를 사용하여 엔터프라이즈 앱에 대한 사용자 계정 프로비전을 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e776ce8a455f2a5b453e23ebb996de9ddb5175a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711962"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리

本文介绍管理自动用户帐户预配和取消预配（适用于支持它的应用程序）的常规步骤。 *사용자 계정 프로비전*은 애플리케이션의 로컬 사용자 프로필 저장소에 사용자 계정 레코드를 생성, 업데이트 및/또는 비활성화하는 작업입니다. 大多数云和 SaaS 应用程序将用户角色和权限存储在用户自己的本地用户配置文件存储区中，并且用户本地存储中存在此类用户记录是单一登录和访问工作*所必需*的。 若要了解有关自动用户帐户预配的详细信息，请参阅[通过 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](user-provisioning.md)。

> [!IMPORTANT]
> Azure Active Directory （Azure AD）有一个库，其中包含数千个预先集成的应用程序，这些应用程序通过 Azure AD 启用了自动预配。 首先应在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中找到应用程序特定的预配安装教程。 你可能会找到有关配置应用程序和 Azure AD 以创建预配连接的分步指导。

## <a name="finding-your-apps-in-the-portal"></a>포털에서 앱 찾기

使用 Azure Active Directory 门户查看和管理在目录中为单一登录配置的所有应用程序。 엔터프라이즈 앱은 조직 내에서 배포 및 사용되는 앱입니다. 请按照以下步骤来查看和管理企业应用程序：

1. 打开[Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用程序以加载其资源窗格，在其中可以查看报表和管理应用设置。
1. 选择 "**设置**" 以管理所选应用的用户帐户预配设置。

   ![用于管理用户帐户预配设置的 "设置" 屏幕](media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>프로비전 모드

"**设置**" 窗格以**模式**菜单开头，其中显示了企业应用程序支持的预配模式，并允许你对其进行配置。 사용 가능한 옵션은 다음과 같습니다.

* **自动**-如果 Azure AD 支持基于 API 自动预配或取消预配此应用程序的用户帐户，则会显示此选项。 选择此模式以显示一个可帮助管理员：

  * 配置 Azure AD 以连接到应用程序的用户管理 API
  * 创建用于定义用户帐户数据如何在 Azure AD 和应用之间流动的帐户映射和工作流
  * 管理 Azure AD 预配服务

* **手动**-如果 Azure AD 不支持向此应用程序自动预配用户帐户，则会显示此选项。 在这种情况下，存储在应用程序中的用户帐户记录必须使用外部进程进行管理，具体取决于该应用程序提供的用户管理和设置功能（可以包括 SAML 实时预配）。

## <a name="configuring-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

选择 "**自动**" 选项来指定管理员凭据、映射、启动和停止以及同步的设置。

### <a name="admin-credentials"></a>관리자 자격 증명

展开 "**管理员凭据**" 以输入 Azure AD 连接到应用程序的用户管理 API 所需的凭据。 필요한 입력은 애플리케이션에 따라 다릅니다. 특정 애플리케이션에 대한 자격 증명 형식 및 요구 사항에 대해 알아보려면 [특정 애플리케이션에 대한 구성 자습서](user-provisioning.md)를 참조하세요.

选择 "**测试连接**"，通过 Azure AD 尝试使用提供的凭据连接到应用的预配应用来测试凭据。

### <a name="mappings"></a>매핑

展开 "**映射**" 可查看和编辑设置或更新用户帐户时在 Azure AD 和目标应用程序之间流动的用户属性。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的映射集。 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. 在表中选择一个映射，以便在右侧打开映射编辑器，您可以在其中查看和自定义该编辑器。

![显示 "属性映射" 屏幕](media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

지원되는 사용자 지정은 다음과 같습니다.

* Azure AD 사용자 개체를 SaaS 앱의 사용자 개체로 매핑하는 것과 같이 특정 개체에 대한 매핑을 사용하거나 사용하지 않도록 설정합니다.
* Azure AD 사용자 개체에서 앱의 사용자 개체로 흐르는 특성을 편집합니다. 특성 매핑에 대한 자세한 내용은 [특성 매핑 유형 이해하기](customize-application-attributes.md#understanding-attribute-mapping-types)를 참조하세요.
* 筛选 Azure AD 在目标应用程序上运行的设置操作。 可以限制运行的操作，而不是让 Azure AD 完全同步对象。

  例如，仅选择 "**更新**" 和 "Azure AD 仅更新应用程序中的现有用户帐户，但不创建新用户帐户。 仅选择 "**创建**"，Azure 仅创建新用户帐户，但不更新现有用户帐户。 此功能使管理员可以创建用于创建帐户和更新工作流的不同映射。

* 添加新的属性映射。 选择 "**属性映射**" 窗格底部的 "**添加新映射**"。 填写**编辑属性**窗体，然后选择 **"确定"** 以将新的映射添加到列表。

### <a name="settings"></a>설정

可以**在 "设置" 屏幕的**"**设置**" 区域中，为所选的应用程序启动和停止 Azure AD 预配服务。 你还可以选择清除预配缓存并重新启动该服务。

프로비전을 애플리케이션에 대해 처음으로 사용하는 경우 **프로비전 상태**를 **켜기**로 변경하여 서비스를 활성화합니다. 此更改会导致 Azure AD 预配服务运行初始周期。 它会读取 "**用户和组**" 部分中分配的用户，查询目标应用程序，然后运行在 Azure AD**映射**"部分中定义的设置操作。 在此过程中，预配服务将存储有关它所管理的用户帐户的缓存数据，因此，取消预配操作不会影响目标应用程序中从不在分配范围内的非托管帐户。 初始循环后，预配服务会按40分钟的间隔自动同步用户和组对象。

将**预配状态**更改为 "**关闭**" 以暂停预配服务。 在此状态下，Azure 不会创建、更新或删除应用程序中的任何用户或组对象。 将状态改回 "**开**"，然后服务从中断的位置继续。

**清除当前状态，重新启动同步**会触发初始周期。 然后，该服务将再次评估源系统中的所有用户，并确定它们是否处于预配的作用域中。 当应用程序当前处于隔离中或者需要更改属性映射时，这会很有用。 请注意，由于需要计算对象的数量，初始周期的完成时间比典型增量周期长。 可在[此处](application-provisioning-when-will-provisioning-finish-specific-user.md)详细了解初始和增量循环的性能。 
