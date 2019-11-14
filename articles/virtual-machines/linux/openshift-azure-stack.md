---
title: 在 Azure Stack 中部署 OpenShift
description: 在 Azure Stack 中部署 OpenShift。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: d6c73b8cd33aa85793a2ce839410065e03b97be7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035534"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>在 Azure Stack 中部署 OpenShift 容器平台或 OKD。

可在 Azure Stack 中部署 OpenShift。 Azure 与 Azure Stack 之间存在一些重要的差异，因此，部署方法和功能会略有不同。

目前，Azure 云提供程序无法在 Azure Stack 中运行。 出于此原因，无法使用附加的磁盘作为 Azure Stack 中的永久存储。 相反，你可以配置其他存储选项，例如 NFS、iSCSI、GlusterFS 等。作为替代方法，你可以启用 CN，并使用 GlusterFS 来实现持久存储。 如果启用了 CNS，将部署额外的三个节点和附加的存储供 GlusterFS 使用。

可以使用多种方法之一在 Azure Stack 中部署 OpenShift 容器平台或 OKD：

- 可以手动部署所需的 Azure 基础结构组件，然后遵循 [OpenShift 容器平台文档](https://docs.openshift.com/container-platform)或 [OKD 文档](https://docs.okd.io)操作。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-container-platform/)，该模板可简化 OpenShift 容器平台群集的部署流程。
- 也可使用现有的[资源管理器模板](https://github.com/Microsoft/openshift-origin)，该模板可简化 OKD 群集的部署。

如果使用资源管理器模板，请选择适当的分支 (azurestack-release-3.x)。 适用于 Azure 的模板不起作用，因为 Azure 与 Azure Stack 的 API 版本不同。 RHEL 映像引用目前在 azuredeploy.json 文件中硬编码为变量，需要对其进行更改，以匹配映像。

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

所有选项均需要 Red Hat 订阅。 在部署期间，会将 Red Hat Enterprise Linux 实例注册到 Red Hat 订阅并附加到包含 OpenShift 容器平台权利的池 ID。
确保提供有效的 Red Hat 订阅管理员 (RHSM) 用户名、密码和池 ID。 或者，可以使用激活密钥、组织 ID 和池 ID。  可通过登录 https://access.redhat.com 验证此信息。

## <a name="azure-stack-prerequisites"></a>Azure Stack 先决条件

需将 RHEL 映像（OpenShift 容器平台）或 CentOS 映像 (OKD) 添加到 Azure Stack 环境才能部署 OpenShift 群集。 请联系 Azure Stack 管理员添加这些映像。 可在以下页面中找到说明：

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>使用 OpenShift 容器平台或 OKD 资源管理器模板进行部署

要使用资源管理器模板进行部署，需使用参数文件提供输入参数。 若要进一步自定义部署，请将 GitHub 存储库进行分支并更改相应的项。

一些常见的自定义选项包括但不限于：

- 守护 VM 大小（azuredeploy.json 中的变量）
- 命名约定（azuredeploy.json 中的变量）
- OpenShift 群集详细信息，通过主机文件修改 (deployOpenShift.sh)
- RHEL 映像引用（azuredeploy.json 中的变量）

对于使用 Azure CLI 进行部署的步骤，请遵循 [OpenShift 容器平台](./openshift-container-platform-3x.md)部分或 [OKD](./openshift-okd.md) 部分中的相应内容。

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-container-platform-3x-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-container-platform-3x-troubleshooting.md)