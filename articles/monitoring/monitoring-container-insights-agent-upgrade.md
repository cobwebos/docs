---
title: 如何升级用于容器的 Azure Monitor（预览版）代理 | Microsoft Docs
description: 本文介绍了如何升级用于容器的 Azure Monitor 使用的 Log Analytics 代理。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: fb45002d284bc28dfb7093f69cfac7aae0681e8d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628296"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>如何升级用于容器的 Azure Monitor（预览版）代理
用于容器的 Azure Monitor 使用适用于 Linux 的 Log Analytics 代理的容器化版本。 当该代理的新版本发布时，在 Azure Kubernetes 服务 (AKS) 上承载的托管 Kubernetes 群集上，该代理不会自动升级。

本文介绍了升级该代理的过程。

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>在受监视的 Kubernetes 群集上升级代理
升级代理的过程包括两个明确的步骤。 第一个步骤是使用 Azure CLI 禁止通过用于容器的 Azure Monitor 进行监视。  请遵循[禁用监视](monitoring-container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli)一文中介绍的步骤。 可以使用 Azure CLI 从群集的节点中删除代理，不会影响解决方案以及工作区中存储的相应数据。 

>[!NOTE]
>执行此维护活动时，群集中的节点不会转发所收集的数据，并且性能视图不会显示从删除代理到安装新版本这段时间内的数据。 
>

若要安装代理的新版本，请使用 Azure CLI 遵循[加入监视](monitoring-container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli)一文中介绍的步骤来完成此过程。  

启用监视后，可能需要约 15 分钟才能查看群集的更新后运行状况指标。 

## <a name="next-steps"></a>后续步骤
如果升级代理时遇到问题，请查看[故障排除指南](monitoring-container-insights-troubleshoot.md)来获得支持。