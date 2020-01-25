---
title: 向 SaaS 应用程序报告自动用户帐户预配
description: 자동 사용자 계정 프로비전 작업의 상태를 확인하는 방법과 개별 사용자의 프로비전 문제를 해결하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77865ad3fe23d1413b6b957d351d9d9d92d22fb7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711983"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>자습서: 자동 사용자 계정 프로비전에 대한 보고

Azure Active Directory （Azure AD）包含一个[用户帐户预配服务](user-provisioning.md)，该服务可帮助自动预配在 SaaS 应用和其他系统中预配用户帐户，以实现端到端的标识生命周期管理。 Azure AD는 [Azure AD 애플리케이션 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured)의 "추천 앱" 섹션에 있는 모든 애플리케이션 및 시스템에 대해 사전 통합된 사용자 프로비전 커넥터를 지원합니다.

이 문서에서는 프로비전 작업을 설정한 후 해당 상태를 확인하는 방법과 개별 사용자 및 그룹의 프로비전 문제를 해결하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

프로비전 커넥터는 지원되는 애플리케이션에 [제공된 설명서](../saas-apps/tutorial-list.md)에 따라 [Azure Portal](https://portal.azure.com)을 사용하여 설정하고 구성합니다. 일단 구성되고 실행된 후에는 다음 두 가지 방법 중 하나를 사용하여 프로비전 작업을 보고할 수 있습니다.

* **Azure 门户**-本文主要介绍如何从[Azure 门户](https://portal.azure.com)检索报表信息，该信息提供了预配摘要报告以及给定应用程序的详细预配审核日志。
* **감사 API** - Azure Active Directory는 자세한 프로비저닝 감사 로그를 프로그래밍 방식으로 검색할 수 있게 해주는 감사 API도 제공합니다. 이 API의 사용과 관련하여 [Azure Active Directory 감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 문서를 참조하세요. 이 문서에서는 API를 사용하는 방법을 구체적으로 다루지 않지만, 감사 로그에 기록되는 프로비전 이벤트 유형에 대해서는 자세히 설명합니다.

### <a name="definitions"></a>정의

이 문서에서 사용하는 용어는 다음과 같이 정의됩니다.

* **원본 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 원본이 되는 사용자의 리포지토리입니다. Azure Active Directory는 대부분의 사전 통합된 프로비전 커넥터를 위한 원본 시스템이지만, Workday 인바운드 동기화와 같이 일부 예외가 있습니다.
* **대상 시스템** - Azure AD 프로비전 서비스에서 동기화하기 위해 대상이 되는 사용자의 리포지토리입니다. 这通常是一个 SaaS 应用程序（例如： Salesforce、ServiceNow、G Suite、Dropbox for Business），但在某些情况下，可以是本地系统，例如 Active Directory （例如： Workday 入站同步到 Active Directory）。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>从 Azure 门户获取预配报告

若要获取给定应用程序的预配报表信息，请 先启动 [Azure 门户](https://portal.azure.com)Azure Active Directory &gt;企业应用&gt; 预配日志（预览）并在活动部分。 还可以浏览到配置了预配的企业应用程序。 예를 들어 사용자를 LinkedIn Elevate로 프로비전하는 경우 애플리케이션 세부 정보의 탐색 경로는 다음과 같습니다.

**Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션 &gt; LinkedIn Elevate**

在此处，你可以访问预配进度栏和预配日志，如下所述。

## <a name="provisioning-progress-bar"></a>设置进度栏

[预配进度栏](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)在给定应用程序的 "**预配**" 选项卡中可见。 它位于 "**设置**" 下的 "**当前状态**" 部分，并显示当前初始或增量周期的状态。 本部分还显示：

* 동기화되어 현재의 원본 시스템과 대상 시스템 간 프로비전에 해당하는 범위에 포함된 총 사용자 및/또는 그룹 수
* 동기화가 마지막으로 실행된 시간 - [初始循环](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)完成后，同步通常每20-40 分钟发生一次。
* [初始周期](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)是否已完成。
* 프로비저닝 프로세스의 격리 여부 및 격리 상태에 대한 이유(예: 잘못된 관리자 자격 증명으로 인해 대상 시스템과 통신하지 못하는 경우).

**当前状态**应该是管理员检查设置作业的操作运行状况的第一个地方。

 ![요약 보고서](media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>设置日志（预览）

预配服务执行的所有活动都记录在 Azure AD[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt;**企业应用**&gt;**预配日志（预览版）** " 来访问 Azure 门户中的设置日志。 你可以根据用户的名称或源系统或目标系统中的标识符来搜索设置数据。 有关详细信息，请参阅[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 기록되는 활동 이벤트 유형은 다음과 같습니다.

## <a name="troubleshooting"></a>문제 해결

预配摘要报告和预配日志起着重要作用，有助于管理员解决各种用户帐户设置问题。

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](application-provisioning-config-problem.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](what-is-single-sign-on.md)
