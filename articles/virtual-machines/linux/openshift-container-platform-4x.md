---
title: 在 Azure 中部署 OpenShift 容器平台 4.x |Microsoft Docs
description: 在 Azure 中部署 OpenShift 容器 Platform 4.x。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 9f807823b1d0d8079c43b2ba0e074a1c8a91b458
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392678"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器平台4。x

现在，Azure 中的 OpenShift 容器平台（OCP）4.2 部署是通过安装程序预配的基础结构（IPI）模型来支持的。  尝试 OpenShift 4 的登录页为[try.openshift.com](https://try.openshift.com/)。 若要在 Azure 中安装 OCP 4.2，请访问[Red Hat OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)页。  需要使用 Red Hat 凭据才能访问此站点。


## <a name="notes"></a>说明 

 - 在 Azure 中安装和运行 OCP 4.x 需要 Azure Active Directory （AAD）服务主体（SP）
     - 必须为 SP 授予 Azure Active Directory 关系图的**OwnedBy**的 API 权限
     - AAD 租户管理员必须授予管理员同意才能使此 API 权限生效
     - SP 必须向订阅授予**参与者**和**用户访问管理员**角色
 - 用于 OCP 4.x 的安装模型不同于3.x 版，并且没有可用于在 Azure 中部署 OCP 4.x 的 Azure 资源管理器模板
 - 如果在安装过程中遇到问题，请联系相应的公司（Microsoft 或 Red Hat）

| 问题说明 | 联系点 |
|-------------------|---------------|
| Azure 特定问题（AAD、SP、Azure 订阅等）                              | Microsoft |
| 特定于 OpenShift 的问题（安装故障/错误、Red Hat 订阅等） |  Red Hat  |




## <a name="next-steps"></a>后续步骤

- [OpenShift 容器平台入门](https://docs.openshift.com)
