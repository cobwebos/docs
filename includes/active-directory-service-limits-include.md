---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 01/22/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 2e06a6c8dd7eb58769f504db9f96e0303c3e9f4c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748772"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| 범주 | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 20개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와의 페더레이션용으로 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
|리소스 |<ul><li>默认情况下，每个免费版 Azure Active Directory 的用户最多可以在单个目录中创建 50000 Azure AD 资源。 如果至少有一个已验证的域，Azure AD 中的默认目录服务配额将扩展为 300000 Azure AD 资源。 </li><li>非管理员用户最多可以创建250个 Azure AD 资源。 可用来还原的活动资源和已删除资源均计入此配额。 仅已删除的已删除 Azure AD 资源可用于还原。 删除 Azure AD 的资源，这些资源在30天的一个季度内，不能再还原到此配额的计数。 如果你的开发人员可能会在其常规职责的过程中反复超过此配额，则可以[创建并分配一个自定义角色，该角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)有权创建无限数量的应用注册。</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>在*所有*类型和*所有*应用程序中，只能将100扩展值写入任何单个 Azure AD 资源。</li><li>User, Group, TenantDetail, Device, Application 및 ServicePrincipal 엔터티만 String 형식 또는 Binary 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
|애플리케이션 매니페스트 |最多可在应用程序清单中添加1200个条目。 |
| 그룹 |<ul><li>用户最多可在 Azure AD 组织中创建250组。</li><li>Azure AD 组织最多可以有5000个动态组。<li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>任意数量的 Azure AD 资源可以是单个组的成员。</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버 수는 50,000으로 제한됩니다.</li><li>并非在所有方案中都支持 Azure AD 中的嵌套组</li></ul><br/> 目前，以下是嵌套组支持的方案。<ul><li> 一个组可以添加为另一个组的成员，并且可以实现组嵌套。</li><li> 组成员身份声明（当应用配置为接收令牌中的组成员身份声明时，已登录用户是其成员的嵌套组）</li><li>条件性访问（在将条件访问策略限定为组时）</li><li>限制对自助密码重置的访问</li><li>限制哪些用户可以执行 Azure AD 联接和设备注册</li></ul><br/>以下方案不支持嵌套组：<ul><li> 应用角色分配（支持向应用分配组，但嵌套在直接分配组内的组将没有访问权限），可用于访问和预配</li><li>基于组的许可（将许可证自动分配给组的所有成员）</li><li>Office 365 组。</li></ul> |
| 애플리케이션 프록시 | <ul><li>每个应用代理应用程序最多每秒500个事务</li><li>Azure AD 组织最多每秒750个事务</li></ul><br/>事务定义为单一的 http 请求和对唯一资源的响应。 如果受到限制，客户端将收到429响应（请求过多）。 |
| 액세스 패널 |<ul><li>사용자당 액세스 패널에서 볼 수 있는 애플리케이션의 수는 제한이 없습니다. 이는 Azure AD Premium 또는 Enterprise Mobility Suite용 라이선스가 할당된 사용자에게 적용됩니다.</li><li>각 사용자는 액세스 패널에서 최대 10개의 앱 타일을 볼 수 있습니다. 此限制适用于为 Azure AD Free 许可证计划分配许可证的用户。 앱 타일의 예로는 Box, Salesforce 또는 Dropbox가 있습니다. 관리자 계정에는 이 제한이 적용되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 장치 | Azure AD 资源可以是不超过30个管理单元的成员。 |
| 관리자 역할 및 권한 | <ul><li>无法将组添加为[所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>不能将组分配给[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>无法在 Azure AD 组织范围内切换用户读取其他用户的目录信息，从而禁止所有非管理员用户访问所有目录信息（不推荐）。 有关默认权限的详细信息，请参阅[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>在管理员角色成员身份添加和吊销生效之前，可能最多需要15分钟或注销/登录。</li></ul> |
