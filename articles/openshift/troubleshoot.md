---
title: 排查 Azure Red Hat OpenShift |Microsoft Docs
description: 排除故障并解决 Azure Red Hat OpenShift 的常见问题
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 7f2bdf643f12671bec3d0c087d8775844099fe9a
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306247"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift 的故障排除

本文详细介绍创建或管理 Microsoft Azure Red Hat OpenShift 群集时遇到的一些常见问题。

## <a name="retrying-the-creation-of-a-failed-cluster"></a>重试失败的群集的创建

如果创建 Azure Red Hat OpenShift 群集使用`az`CLI 命令失败，正在重试创建将继续失败。
使用`az openshift delete`若要删除失败的群集，然后创建一个全新的群集。

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>隐藏的 Azure Red Hat OpenShift 群集资源组

目前， `Microsoft.ContainerService/openShiftManagedClusters` Azure CLI 会自动创建的资源 (`az openshift create`命令) 被隐藏在 Azure 门户中。 在中**资源组**视图中，检查**显示隐藏的类型**若要查看资源组。

![在门户中的隐藏的类型复选框的屏幕截图](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>创建群集的结果中找到任何已注册的资源提供程序的错误

如果在错误中创建群集结果`No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview`，然后是预览版的一部分，现在需要[购买 Azure 虚拟机保留实例](https://aka.ms/openshift/buy)使用已正式发布的产品。 保留可减少你费用由预付费的完全托管的 Azure 服务。 请参阅[*什么是 Azure 预订*](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)若要详细了解保留和如何他们节省资金。

## <a name="next-steps"></a>后续步骤

- 请尝试[Red Hat OpenShift 帮助中心](https://help.openshift.com/)进行故障排除的 on OpenShift 详细。

- 查找答案[有关 Azure Red Hat OpenShift 常见问题](openshift-faq.md)。
