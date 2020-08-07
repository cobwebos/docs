---
title: Azure API for FHIR 的其他设置
description: 可为 Azure API for FHIR 指定的其他设置的概述
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 5de1d99442f307fc7850114915851f994258b537
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845895"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Azure API for FHIR 的其他设置

在本操作指南中，我们将了解可在 Azure API for FHIR 中指定的其他设置。 一些附加页面提供了更深入的详细信息。

## <a name="configure-database-settings"></a>配置数据库设置

Azure API for FHIR 使用数据库存储其数据。 底层数据库的性能取决于预配服务期间选择的请求单位 (RU) 数，以及预配服务后数据库设置中的 RU 数。

必须预配吞吐量才能确保始终为数据库提供足够的系统资源。 应用程序所需的 RU 数取决于执行的操作。 操作既包括简单的读写，也包括更复杂的查询。

有关如何更改默认设置的详细信息，请参阅[配置数据库设置](configure-database.md)。

## <a name="access-control"></a>访问控制

适用于 FHIR 的 Azure API 将仅允许授权用户访问 FHIR API。 可以通过两种不同的机制来配置授权的用户。 配置访问控制的主要方法和推荐方法是使用 azure [RBAC) 的 azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/)，该访问控制可通过**访问控制 (IAM) **边栏选项卡进行访问 (。 仅当你想要使用与你的订阅关联的 Azure Active Directory 租户来保护数据平面访问时，Azure RBAC 才有效。 如果要使用不同的租户，Azure API for FHIR 提供了本地 FHIR 数据平面访问控制机制。 使用本地 RBAC 机制时，配置选项不是很丰富。 有关详细信息，请选择下列选项之一：

* [适用于 FHIR 数据平面的 AZURE RBAC](configure-azure-rbac.md)。 当你使用与你的订阅关联的 Azure Active Directory 租户时，这是首选选项。
* [本地 FHIR 数据平面访问控制](configure-local-rbac.md)。 仅当需要使用外部 Azure Active Directory 租户进行数据平面访问控制时，才使用此选项。 

## <a name="enable-diagnostic-logging"></a>启用诊断日志记录
你可能想要在安装过程中启用诊断日志记录，以便能够监视你的服务并提供准确的报告来实现符合性。 有关如何设置诊断日志记录的详细信息，请参阅有关如何设置诊断日志记录的操作[方法](enable-diagnostic-logging.md)以及一些示例查询。 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>使用自定义标头将数据添加到审核日志
在用于 FHIR 的 Azure API 中，你可能需要在来自调用系统的日志中包含附加信息。 为此，您可以使用自定义标头。

可以使用自定义标头来捕获多种类型的信息。 例如：

* 标识或授权信息
* 调用方的源点
* 发起组织
* 客户端系统详细信息（电子病历、患者门户）

若要将此数据添加到审核日志，请参阅[使用自定义 HTTP 标头向审核日志添加数据](use-custom-headers.md)操作方法。

## <a name="next-steps"></a>后续步骤

在本操作方法指南中，为 FHIR 的 Azure API 设置其他设置。

接下来，请查看一系列教程来创建读取 FHIR 数据的 web 应用程序。

>[!div class="nextstepaction"]
>[部署 JavaScript 应用程序](tutorial-web-app-fhir-server.md)