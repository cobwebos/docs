---
title: Azure Red Hat OpenShift 群集管理员角色 |Microsoft Docs
description: Azure Red Hat OpenShift 群集管理员角色分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709957"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客户管理员角色

作为 OpenShift 群集的 ARO （Azure Red Hat OpenShift）群集管理员，你的帐户已增加了对所有用户创建项目的权限和访问权限。

当你的帐户有与之绑定的 osa-客户管理员授权角色时，它可以自动管理项目。

> [!Note] 
> osa-customer-admin clusterrole 与群集管理 clusterrole 不同


例如，可以执行与一组谓词（`create`）关联的操作，以便对一组资源名称（`templates`）进行操作。 若要查看这些角色及其谓词和资源集的详细信息，请运行以下命令：

`$ oc describe clusterrole/osa-customer-admin`

谓词名称并不一定直接映射到 oc 命令，而是更常见地等同于可执行的 CLI 操作类型。 例如，如果使用 @no__t 0 谓词，则表示你可以显示给定资源名称（@no__t 为1）的所有对象的列表，而 `get` 谓词意味着你可以显示特定对象的详细信息（如果你知道该对象的名称（`oc describe`）。

## <a name="how-to-configure-customer-administrator-role"></a>如何配置客户管理员角色

仅可通过向 `--customer-admin-group-id` 提供标志，在群集创建期间配置客户管理员角色。 如何配置 Azure Active Directory 和管理员组遵循操作方法指南：[Azure Active Directory Azure Red Hat OpenShift 集成](howto-aad-app-configuration.md)

## <a name="next-steps"></a>后续步骤

如何配置 osa-客户管理员角色：
> [!div class="nextstepaction"]
> [Azure Active Directory Azure Red Hat OpenShift 集成](howto-aad-app-configuration.md)
