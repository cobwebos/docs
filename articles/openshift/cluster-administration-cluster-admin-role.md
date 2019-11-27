---
title: Azure Red Hat OpenShift 群集管理员角色 |Microsoft Docs
description: Azure Red Hat OpenShift 群集管理员角色的分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539276"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客户管理员角色

你是 Azure Red Hat OpenShift 群集的群集管理员。 你的帐户已增加权限，并可访问所有用户创建的项目。

当你的帐户有与之绑定的客户-管理员群集授权角色时，它可以自动管理项目。

> [!Note] 
> 客户管理员群集群集角色与群集管理员群集角色不同。


例如，可以执行与一组谓词（`create`）关联的操作，以便对一组资源名称（`templates`）进行操作。 若要查看这些角色及其谓词和资源集的详细信息，请运行以下命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

谓词名称并不一定直接映射到 `oc` 命令。 它们更常见地等同于你可以执行的 CLI 操作类型。 

例如，具有 `list` 谓词意味着可以显示资源名称（`oc get`）的所有对象的列表。 `get` 谓词表示，如果知道特定对象的名称（`oc describe`），则可以显示该对象的详细信息。

## <a name="configure-the-customer-administrator-role"></a>配置客户管理员角色

仅在群集创建过程中，可以通过提供标志 `--customer-admin-group-id`来配置客户管理群集的群集角色。 若要了解如何配置 Azure Active Directory 和 Administrators 组，请参阅[Azure Red Hat OpenShift 的 Azure Active Directory 集成](howto-aad-app-configuration.md)。

## <a name="next-steps"></a>后续步骤

配置客户-管理员群集的群集角色：
> [!div class="nextstepaction"]
> [Azure Active Directory Azure Red Hat OpenShift 集成](howto-aad-app-configuration.md)
