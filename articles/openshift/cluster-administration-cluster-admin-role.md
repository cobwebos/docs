---
title: Azure Red Hat OpenShift 群集管理员角色 |Microsoft Docs
description: Azure Red Hat OpenShift 群集管理员角色的分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936932"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客户管理员角色

你是 Azure Red Hat OpenShift 群集的群集管理员。 你的帐户已增加权限，并可访问所有用户创建的项目。

当你的帐户有与之绑定的 osa 客户-管理员授权角色时，它可以自动管理项目。

> [!Note] 
> "Osa-客户-管理" 群集角色与群集管理群集角色不同。


例如，可以执行与一组谓词（`create`）关联的操作，以便对一组资源名称（`templates`）进行操作。 若要查看这些角色及其谓词和资源集的详细信息，请运行以下命令：

`$ oc describe clusterrole/osa-customer-admin`

谓词名称并不一定直接映射到 `oc` 命令。 它们更常见地等同于你可以执行的 CLI 操作类型。 

例如，如果使用 @no__t 0 谓词，则表示可以显示资源名称（@no__t）的所有对象的列表。 如果你知道特定对象的名称（@no__t 为-1），则 @no__t 谓词表示可以显示该对象的详细信息。

## <a name="configure-the-customer-administrator-role"></a>配置客户管理员角色

只有在创建群集期间，才能通过提供 `--customer-admin-group-id` 标志来配置客户管理员角色。 若要了解如何配置 Azure Active Directory 和 Administrators 组，请参阅[Azure Red Hat OpenShift 的 Azure Active Directory 集成](howto-aad-app-configuration.md)。

## <a name="next-steps"></a>后续步骤

配置 "osa-客户管理员" 角色：
> [!div class="nextstepaction"]
> [Azure Active Directory Azure Red Hat OpenShift 集成](howto-aad-app-configuration.md)
