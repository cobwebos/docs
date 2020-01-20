---
title: Azure Red Hat OpenShift 疑难解答
description: 排查和解决 Azure Red Hat OpenShift 的常见问题
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: ee032cdf4a3f72b2cd2e7da0658effe75b6fb1fa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274931"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的故障排除

本文详细介绍了创建或管理 Microsoft Azure Red Hat OpenShift 群集时遇到的一些常见问题。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>正在重试创建失败的群集

如果使用 `az` CLI 命令创建 Azure Red Hat OpenShift 群集失败，则重试创建操作将继续失败。
使用 `az openshift delete` 删除失败的群集，然后创建一个全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隐藏 Azure Red Hat OpenShift 群集资源组

当前，Azure CLI （`az openshift create` 命令）自动创建的 `Microsoft.ContainerService/openShiftManagedClusters` 资源在 Azure 门户中处于隐藏状态。 在**资源组**视图中，选中 "**显示隐藏的类型**" 以查看资源组。

![门户中隐藏的类型复选框的屏幕截图](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>创建群集导致找不到任何已注册的资源提供程序

如果创建群集时出现 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`错误，则表明你是预览版的一部分，现在需要[购买 Azure 虚拟机保留实例](https://aka.ms/openshift/buy)，才能使用正式发布的产品。 预订通过预先支付完全托管的 Azure 服务来减少支出。 请参阅[*什么是 Azure 保留内容*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)，了解有关预订的详细信息以及这些预订如何节省资金。

## <a name="next-steps"></a>后续步骤

- 有关 OpenShift 故障排除的详细信息，请尝试[Red Hat OpenShift 帮助中心](https://help.openshift.com/)。

- 查找[有关 Azure Red Hat OpenShift](openshift-faq.md)的常见问题的解答。
