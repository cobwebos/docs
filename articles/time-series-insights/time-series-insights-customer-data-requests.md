---
title: Azure 时序见解中的客户数据请求功能
description: 客户数据请求功能摘要。
author: ashannon7
ms.author: anshan
manager: cshankar
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.openlocfilehash: 4696cdaf96a73c54334f553a0affe459e3476946
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629729"
---
# <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要

Azure 时序见解是一项托管云服务，具有存储、分析和可视化组件，可以轻松地同时引入、存储、浏览和分析数十亿个事件。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

若要查看、导出和删除可能受数据主体请求影响的个人数据，Azure 时序见解租户管理员可使用 Azure 门户或 REST API 进行操作。 使用 Azure 门户为数据主体请求提供服务，可以更简单地执行大多数用户的首选操作。

## <a name="identifying-customer-data"></a>标识客户数据

Azure 时序见解将个人数据视为与时序见解的管理员和用户相关联的数据。 时序见解存储具有环境访问权限的用户的 Azure Active Directory 对象 ID。 Azure 门户显示用户电子邮件地址，但这些电子邮件地址不存储在时序见解内，可使用 Azure Active Directory 中的 Azure Active Directory 对象 ID 动态查找。

## <a name="deleting-customer-data"></a>删除客户数据

租户管理员可使用 Azure 门户删除客户数据。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

但是，在通过门户删除客户数据之前，应该从 Azure 门户内的时序见解环境中删除用户的访问策略。 有关详细信息，请参阅[使用 Azure 门户授予对时序见解环境的数据访问权限](time-series-insights-data-access.md)。

也可使用 REST API 对访问策略执行删除操作。 有关详细信息，请参阅 [Access Policies - Delete](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/delete)（访问策略 - 删除）。

时序见解与 Azure 门户中的“策略”边栏选项卡集成。 可通过时序见解和“策略”边栏选项卡查看、导出和删除存储在服务中的用户数据。 在 Azure 门户的“策略”边栏选项卡中执行的任何删除操作都会导致删除时序见解中的用户数据。 例如，如果用户保存了一条个人查询，则该查询将从时序见解资源管理器中永久删除。 如果用户保存了一条共享查询，则查询仍然存在，但将永久删除用户信息。 以下注释包含有关如何完成这些任务的说明。

## <a name="exporting-customer-data"></a>导出客户数据

与删除数据类似，租户管理员可从 Azure 门户中的“策略”边栏选项卡查看和导出存储在时序见解中的数据。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

如果你是租户管理员，则可在 Azure 门户的时序见解环境中查看数据访问策略。 有关详细信息，请参阅[使用 Azure 门户授予对时序见解环境的数据访问权限](time-series-insights-data-access.md)。

也可使用提供的 REST API 中的“按环境列出”操作对访问策略执行导出操作。 有关详细信息，请参阅 [Access Policies - List By Environment](https://docs.microsoft.com/rest/api/time-series-insights-management/accesspolicies/listbyenvironment)（访问策略 - 按环境列出）。

## <a name="to-delete-data-stored-within-time-series-insights"></a>删除时序见解内存储的数据

个人数据可能会进入时序见解存储，这种情况与用户和管理员数据有所不同。 如果认为存储在时序见解中的数据是个人数据，则可使用以下步骤导出和删除该数据：

**查看和导出数据** 

若要查看和导出存储在时序见解中的数据，需要搜索该数据。 可使用时序见解资源管理器或时序见解查询 API 来查看和导出数据。 若要使用时序见解资源管理器查看和导出数据，请先进行搜索，找到有问题的用户数据。 搜索后，右键单击图表并选择“浏览事件”。 事件网格显示并提供将数据导出为 CSV 和 JSON 的选项。

有关详细信息，请参阅 [Azure 时序见解资源管理器](time-series-insights-explorer.md)。

**删除数据**

目前，时序见解不支持数据的粒度删除。 但是，通过配置保留策略，时序见解能够删除存储在时序见解中的客户数据。 可将整个时序见解环境的保持期调整为任意天数以支持删除需求。

有关详细信息，请参阅[配置时序见解中的保留期](time-series-insights-how-to-configure-retention.md)。