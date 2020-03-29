---
title: 排除 Azure 红帽打开移位故障
description: 使用 Azure 红帽开放Shift 解决和解决常见问题
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274931"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure 红帽打开移位的故障排除

本文详细介绍了创建或管理 Microsoft Azure 红帽 OpenShift 群集时遇到的一些常见问题。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重试创建失败的群集

如果使用`az`CLI 命令创建 Azure 红帽 OpenShift 群集失败，则重试创建将继续失败。
用于`az openshift delete`删除失败的群集，然后创建一个全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隐藏的 Azure 红帽开放移位群集资源组

目前，Azure `Microsoft.ContainerService/openShiftManagedClusters` CLI（`az openshift create`命令）自动创建的资源隐藏在 Azure 门户中。 在 **"资源组"** 视图中，选中 **"显示隐藏类型**以查看资源组"。

![门户中隐藏类型复选框的屏幕截图](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>创建群集会导致错误，未找到注册的资源提供程序

如果创建群集会导致错误，`No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`则您是预览的一部分，现在需要购买 Azure[虚拟机预留实例](https://aka.ms/openshift/buy)才能使用通常可用的产品。 预留通过为完全托管的 Azure 服务预付费用来减少支出。 请参阅[*什么是 Azure 预留*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)，了解有关预订及其如何为您节省资金的详细信息。

## <a name="next-steps"></a>后续步骤

- 有关 OpenShift 故障排除的更多问题，请尝试[红帽 OpenShift 帮助中心](https://help.openshift.com/)。

- 查找有关[Azure 红帽开放Shift](openshift-faq.md)的常见问题的解答。
