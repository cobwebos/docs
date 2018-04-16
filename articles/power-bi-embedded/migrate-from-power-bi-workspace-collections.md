---
title: 如何将 Power BI 工作区集合内容迁移到 Power BI Embedded | Microsoft Docs
description: 了解如何从 Power BI 工作区集合迁移到 Power BI Embedded 并利用改进在应用中进行嵌入。
services: power-bi-embedded
documentationcenter: ''
author: guyinacube
manager: erikre
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>如何将 Power BI 工作区集合内容迁移到 Power BI Embedded

了解如何从 Power BI 工作区集合迁移到 Power BI Embedded。 本文提供有关从 Azure Power BI 工作区集合迁移到 Power BI Embedded 的指导。 我们还会了解对应用程序更改的期望。

在 Power BI Premium 版本公开上市之后，Power BI 工作区集合资源可继续使用有限时间。 企业协议涵盖的客户可以在其现有协议过期之后访问其现有工作区集合。 通过直接或 CSP 渠道获取 Power BI 工作区集合的客户可在 Power BI Premium 公开上市之后享有一年时间的访问权限。

> [!IMPORTANT]
> 虽然迁移依赖于 Power BI 服务，不过在使用**嵌入令牌**时，应用程序的用户不依赖于 Power BI。 他们无需注册 Power BI 即可查看应用程序中的嵌入内容。 可以使用此嵌入方法为客户嵌入 Power BI。

![Power BI Embedded 流](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>准备迁移

对于从 Power BI 工作区集合服务迁移转移到 Power BI Embedded，需要进行一些准备工作。 需要有一个租户以及具有 Power BI Pro 许可证的用户可用。

1. 确保可以访问 Azure Active Directory (Azure AD) 租户。

    要使用哪个租户？

    * 使用现有企业 Power BI 租户？
    * 对应用程序使用单独租户？
    * 对每个客户使用单独租户？

    如果决定为应用程序或每个客户创建新租户，请参阅以下内容之一：

    * [创建 Azure Active Directory 租户](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [如何获取 Azure Active Directory 租户](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)。

2. 在此新租户中创建充当应用程序“主”帐户的用户。 该帐户需要注册 Power BI，并需要分配有 Power BI Pro 许可证。

## <a name="accounts-within-azure-ad"></a>Azure AD 中的帐户

租户中需要存在以下帐户。

> [!NOTE]
> 这些帐户需要具有 Power BI Pro 许可证才能使用应用工作区。

1. 租户管理员用户。

    建议嵌入应用工作区中将租户管理员列为成员。

2. 用于创建内容的分析员的帐户。

    这些用户应根据需要分配给应用工作区。

3. 应用程序主用户帐户或服务帐户。

    应用程序后端会存储此帐户的凭据。 使用主帐户获取与 Power BI REST API 一起使用的 Azure AD 令牌。 此帐户用于为应用程序生成嵌入令牌。 主帐户需要是为进行嵌入而创建的应用工作区的管理员。

    **此帐户只是组织中用于嵌入用途的常规用户帐户。**

## <a name="app-registration-and-permissions"></a>应用注册和权限

若要进行 REST API 调用，请向 Azure AD 注册应用程序。 会在 Microsoft Azure 门户以及 Power BI 应用注册页中应用添加配置。 有关详细信息，请参阅[注册 Azure AD 应用以嵌入 Power BI 内容](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)。

建议使用应用程序**主**帐户来注册应用程序。

## <a name="create-app-workspaces-required"></a>创建应用工作区（必需）

如果应用程序为多个客户提供服务，则可以利用应用工作区提供更好的隔离。 仪表板和报表会在客户之间进行隔离。 随后可以对每个应用工作区使用一个 Power BI 帐户以进一步在客户之间隔离应用程序体验，不过可以仅使用一个帐户以保持简单性。

> [!IMPORTANT]
> 无法使用个人工作区（“我的工作区”）来利用对客户的嵌入。

需要具有 Pro 许可证的用户才能在 Power BI 中创建应用工作区。 创建应用工作区的 Power BI 用户在默认情况下是该工作区的管理员。 **应用程序主帐户需要是工作区的管理员。**

## <a name="content-migration"></a>内容迁移

将内容从工作区集合迁移到 Power BI Embedded 可以与当前解决方案并行进行，无需任何停机时间。

可使用“迁移工具”将内容从 Power BI 工作区集合复制到 Power BI Embedded。 尤其是你具有许多报表时。 有关详细信息，请参阅 [Power BI Embedded 迁移工具](migrate-tool.md)。

内容迁移主要依靠两个 API。

1. 下载 PBIX - 此 API 可以下载在 2016 年 10 月之后上传到 Power BI 的 PBIX 文件。
2. 导入 PBIX - 此 API 将任何 PBIX 上传到 Power BI。

有关一些相关代码片段，请参阅[用于从 Power BI Embedded 迁移内容的代码片段](migrate-code-snippets.md)。

### <a name="report-types"></a>报表类型

有几种类型的报表，每种需要不同的迁移流。

#### <a name="cached-dataset-and-report"></a>缓存数据集和报表

缓存数据集指导入了数据的 PBIX 文件，而不是实时连接或 DirectQuery 连接。

**流**

1. 从 Power BI 工作区集合工作区调用下载 PBIX API。
2. 保存 PBIX。
3. 为 Power BI Embedded 工作区调用导入 PBIX。

#### <a name="directquery-dataset-and-report"></a>DirectQuery 数据集和报表

**流**

1. 调用 GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources 并保存收到的连接字符串。
2. 从 Power BI 工作区集合工作区调用下载 PBIX API。
3. 保存 PBIX。
4. 为 Power BI Embedded 工作区调用导入 PBIX。
5. 通过调用 POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections 更新连接字符串
6. 通过调用 GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources 获取 GW ID 和数据源 ID
7. 通过调用 PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id} 更新用户的凭据

#### <a name="old-dataset-and-reports"></a>旧数据集和报表

2016 年 10 月之前上传的报表不支持下载 PBIX 功能。

**流**

1. 从开发环境（内部源代码管理）获取 PBIX。
2. 为 Power BI Embedded 工作区调用导入 PBIX。

#### <a name="push-dataset-and-report"></a>推送数据集和报表

下载 PBIX 不支持推送 API 数据集。 推送 API 数据集数据无法从 Power BI 工作区集合移植到 Power BI Embedded。

**流**

1. 使用数据集 Json 调用“创建数据集”API 可为 Power BI Embedded 工作区创建数据集。
2. 为创建的数据集重新生成报表*。

可以通过尝试以下方式，使用一些解决方法将推送 API 报表从 Power BI 工作区集合迁移到 Power BI Embedded：

1. 将某个虚拟 PBIX 上传到 Power BI 工作区集合工作区。
2. 克隆推送 API 报告并将它绑定到步骤 1 中的虚拟 PBIX。
3. 通过虚拟 PBIX 下载推送 API 报表。
4. 将虚拟 PBIX 上传到 Power BI Embedded 工作区。
5. 在 Power BI Embedded 工作区中创建推送数据集。
6. 将报表重新绑定到推送 API 数据集。

## <a name="create-and-upload-new-reports"></a>创建并上传新报表

除了从 Power BI 工作区集合迁移的内容之外，可以使用 Power BI Desktop 创建报表和数据集，然后将这些报表发布到应用工作区。 发布报表的最终用户需要具有 Power BI Pro 许可证才能发布到应用工作区。

## <a name="rebuild-your-application"></a>重新生成应用程序

1. 修改应用程序以使用 Power BI REST API 和 powerbi.com 中的报表位置。

2. 使用应用程序的主帐户重新生成身份验证/授权身份验证。 可以使用[嵌入令牌](https://msdn.microsoft.com/library/mt784614.aspx)允许此用户代表其他用户。

3. 将报表从 Power BI Embedded 嵌入到应用程序中。 有关详细信息，请参阅[如何嵌入 Power BI 仪表板、报表和磁贴](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

## <a name="map-your-users-to-a-power-bi-user"></a>将用户映射到 Power BI 用户

在应用程序中，将在应用程序中管理的用户映射到主 Power BI 凭据以用于应用程序。 此 Power BI 主帐户的凭据存储在应用程序中，用于创建嵌入令牌。

## <a name="what-to-do-when-you-are-ready-for-production"></a>准备好进行生产时要执行的操作

准备好移动到生产环境时，需要执行以下操作：

- 如果使用单独租户进行开发，则需要确保应用工作区以及仪表板和报表在生产环境中可用。 确保在 Azure AD 中为生产租户创建了应用程序，并分配了正确的应用权限，如步骤 1 所示。

- 购买符合需求的容量。 可以使用[嵌入式分析容量规划白皮书](https://aka.ms/pbiewhitepaper)来帮助了解可能需要什么。 准备好进行购买时，可以在 Azure 门户中购买 Power BI Embedded 资源。

- 编辑应用工作区，并在“高级”下将它分配给容量。

    ![在 powerbi.com 中将应用工作区中分配给容量](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- 将更新的应用程序部署到生产环境，然后开始从 Power BI Embedded 嵌入报表。

## <a name="after-migration"></a>迁移之后

需要在 Power BI 工作区集合中进行一些清理。

- 在 Power BI 工作区集合的 Azure 服务中删除部署的解决方案的所有工作区。
- 删除 Azure 中存在的任何工作区集合。

## <a name="next-steps"></a>后续步骤

祝贺。 应用程序现在迁移到 Power BI Embedded。 有关如何嵌入 Power BI 仪表板、报表和数据集的信息，请参阅[如何嵌入 Power BI 仪表板、报表和磁贴](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有更多问题？ [尝试在 Power BI 社区中提问](http://community.powerbi.com/)