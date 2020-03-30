---
title: 管理 Azure 红帽开放移位中的资源 |微软文档
description: 在 Azure 红帽 OpenShift 群集中管理项目、模板和图像流
services: openshift
keywords: 红帽开档项目请求自编
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139107"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>在 Azure 红帽 OpenShift 群集中管理项目、模板和图像流 

在 OpenShift 容器平台中，项目用于对相关对象进行分组和隔离。 作为管理员，您可以授予开发人员对特定项目的权限，允许他们创建自己的项目，并授予他们各个项目的管理权限。

## <a name="self-provisioning-projects"></a>自调配项目

您可以使开发人员创建自己的项目。 API 终结点负责根据名为项目请求的模板预配项目。 当开发人员创建新项目时`oc new-project`，Web 控制台和命令使用此终结点。

提交项目请求时，API 将替换模板中的以下参数：

| 参数               | 描述                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 项目的名称。 必需。             |
| PROJECT_DISPLAYNAME     | 项目的显示名称。 可能为空。 |
| PROJECT_DESCRIPTION     | 项目的说明。 可能为空。  |
| PROJECT_ADMIN_USER      | 管理用户的用户名。       |
| PROJECT_REQUESTING_USER | 请求用户的用户名。           |

使用自预配器群集角色绑定授予开发人员对 API 的访问。 默认情况下，此功能可供所有经过身份验证的开发人员使用。

## <a name="modify-the-template-for-a-new-project"></a>修改新项目的模板 

1. 以具有`customer-admin`权限的用户身份登录。

2. 编辑默认项目请求模板。

   ```
   oc edit template project-request -n openshift
   ```

3. 通过添加以下注释，从 Azure 红帽 OpenShift （ARO） 更新流程中删除默认项目模板：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   项目请求模板不会由 ARO 更新过程更新。 这使客户能够自定义模板并在更新群集时保留这些自定义项。

## <a name="disable-the-self-provisioning-role"></a>禁用自我预配角色

您可以阻止经过身份验证的用户组自行预配新项目。

1. 以具有`customer-admin`权限的用户身份登录。

2. 编辑自预配器群集角色绑定。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 通过添加以下注释从 ARO 更新过程中删除角色： `openshift.io/reconcile-protect: "true"`。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 更改群集角色绑定以防止`system:authenticated:oauth`创建项目：

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>管理默认模板和图像流

在 Azure 红帽 OpenShift 中，可以禁用命名空间内`openshift`任何默认模板和图像流的更新。
要禁用 ALL`Templates`和`ImageStreams`命名`openshift`空间中的更新，

1. 以具有`customer-admin`权限的用户身份登录。

2. 编辑`openshift`命名空间：

   ```
   oc edit namespace openshift
   ```

3. 通过`openshift`添加以下注释从 ARO 更新过程中删除命名空间：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   可以通过向更新进程添加`openshift`注释`openshift.io/reconcile-protect: "true"`来从更新过程中删除命名空间中的任何单个对象。

## <a name="next-steps"></a>后续步骤

请尝试本教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)
