---
title: 排查 Azure Red Hat OpenShift
description: 排查和解决 Azure Red Hat OpenShift 的常见问题
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 55360ef295ff80b700b059d053203458f9f384db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469076"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的故障排除

本文详细介绍了创建或管理 Microsoft Azure Red Hat OpenShift 群集时遇到的一些常见问题。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>正在重试创建失败的群集

如果使用 CLI 命令创建 Azure Red Hat OpenShift 群集 `az` 失败，则重试创建操作将继续失败。
使用 `az openshift delete` 删除失败的群集，然后创建一个全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隐藏 Azure Red Hat OpenShift 群集资源组

目前， `Microsoft.ContainerService/openShiftManagedClusters`) 中 Azure CLI (命令自动创建的资源 `az openshift create` 在 Azure 门户中是隐藏的。 在 **资源组** 视图中，选中 " **显示隐藏的类型** " 以查看资源组。

![门户中隐藏的类型复选框的屏幕截图](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>创建群集导致找不到任何已注册的资源提供程序

如果创建群集时出现错误，则表明 `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` 你是预览版的一部分，现在需要 [购买 Azure 虚拟机保留实例](https://aka.ms/openshift/buy) ，才能使用正式发布的产品。 预订通过预先支付完全托管的 Azure 服务来减少支出。 请参阅 [*什么是 Azure 保留内容*](../cost-management-billing/reservations/save-compute-costs-reservations.md) ，了解有关预订的详细信息以及这些预订如何节省资金。

## <a name="next-steps"></a>后续步骤

- 有关 OpenShift 故障排除的详细信息，请尝试 [Red Hat OpenShift 帮助中心](https://help.openshift.com/) 。

- 查找 [有关 Azure Red Hat OpenShift](openshift-faq.md)的常见问题的解答。