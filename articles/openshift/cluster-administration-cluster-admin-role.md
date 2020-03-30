---
title: Azure 红帽开放移位群集管理员角色 |微软文档
description: Azure 红帽开放移位群集管理员角色的分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139090"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure 红帽开放转移客户管理员角色

您是 Azure 红帽开放Shift 群集的群集管理员。 您的帐户具有对所有用户创建项目的权限和访问权限。

当您的帐户将客户管理员群集授权角色绑定到该角色时，它可以自动管理项目。

> [!Note] 
> 客户-管理员群集群集角色与群集管理员群集角色不同。

例如，可以执行与一组谓词 （`create`） 关联的操作，以对一组资源名称 （`templates`） 进行操作。 要查看这些角色及其谓词和资源集的详细信息，请运行以下命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

动词名称不一定全部直接映射到`oc`命令。 它们更一般地等同于您可以执行的 CLI 操作类型。 

例如，`list`使用谓词意味着可以显示资源名称 （ 的所有`oc get`对象的列表）。 该`get`谓词表示，如果知道特定对象的名称 （），`oc describe`则可以显示其详细信息。

## <a name="configure-the-customer-administrator-role"></a>配置客户管理员角色

只能在群集创建期间通过提供标志`--customer-admin-group-id`来配置客户管理员群集群集角色。 此字段当前无法在 Azure 门户中配置。 要了解如何配置 Azure 活动目录和管理员组，请参阅[Azure 红帽 OpenShift 的 Azure 活动目录集成](howto-aad-app-configuration.md)。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>确认客户管理员角色的成员身份

要确认您在客户管理组中的成员资格，请尝试 OpenShift CLI`oc get nodes`命令`oc projects`或 。 `oc get nodes`如果具有客户管理员群集角色，将显示节点列表;如果只有客户管理员-项目角色，则显示权限错误。 `oc projects`将显示群集中的所有项目，而不是只显示您正在处理的项目。

要进一步浏览群集中的角色和权限，可以使用 该[`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings)命令。

## <a name="next-steps"></a>后续步骤

配置客户管理员群集角色：
> [!div class="nextstepaction"]
> [Azure 红帽开放移位的 Azure 活动目录集成](howto-aad-app-configuration.md)
