---
title: 在 Azure 中部署 OpenShift 容器平台 4.x
description: 在 Azure 中部署 OpenShift 容器平台 4.x。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759492"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器平台 4.x

现在，Azure 中通过安装程序配置基础结构 （IPI） 模型支持部署 OpenShift 容器平台 （OCP） 4.2。  尝试 OpenShift 4 的着陆页[try.openshift.com](https://try.openshift.com/)。 要在 Azure 中安装 OCP 4.2，请访问[红帽 OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)页面。  访问此站点需要红帽凭据。


## <a name="notes"></a>说明 

 - 在 Azure 中安装和运行 OCP 4.x 需要 Azure 活动目录 （AAD） 服务主体 （SP）
     - 必须授予 SP 应用程序的 API 权限 **。**
     - AAD 租户管理员必须授予管理员同意才能使此 API 生效
     - 必须授予订阅**的"参与者**"和 **"用户访问管理员"** 角色
 - OCP 4.x 的安装模型与 3.x 不同，并且没有可用于在 Azure 中部署 OCP 4.x 的 Azure 资源管理器模板
 - 如果在安装过程中遇到问题，请联系相应的公司（微软或红帽）

| 问题描述 | 联系点 |
|-------------------|---------------|
| Azure 特定问题（AAD、SP、Azure 订阅等）                              | Microsoft |
| OpenShift 特定问题（安装失败/错误、红帽订阅等） |  Red Hat  |




## <a name="next-steps"></a>后续步骤

- [使用 OpenShift 容器平台入门](https://docs.openshift.com)
