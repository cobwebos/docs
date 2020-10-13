---
title: 部署到现有虚拟网络
description: 介绍如何允许托管应用程序的用户选择现有虚拟网络。 虚拟网络可以在托管应用程序之外。
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84260680"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>通过 Azure 托管应用程序使用现有虚拟网络

本文介绍如何定义与使用者的订阅中的现有虚拟网络集成的 Azure 托管应用程序。 托管应用程序让使用者决定是创建新的虚拟网络还是使用现有的虚拟网络。 现有虚拟网络可以位于托管资源组之外。

## <a name="main-template"></a>主模板

首先，让我们看一下 mainTemplate.json 文件。 下面显示了用于部署虚拟机及其关联资源的整个模板。 稍后，你将更仔细地检查模板中与使用现有虚拟网络相关的部分。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

请注意，虚拟网络是[有条件部署的](../templates/conditional-resource-deployment.md)。 使用者传入一个参数值，该值指示是创建新的虚拟网络还是使用现有的虚拟网络。 如果使用者选择新的虚拟网络，则将部署资源。 否则，在部署过程中将跳过资源。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

虚拟网络 ID 的变量具有两个属性。 一个属性在部署新虚拟网络时返回资源 ID。 另一个属性在使用现有虚拟网络时返回资源 ID。 现有虚拟网络的资源 ID 包含虚拟网络所在资源组的名称。

子网 ID 根据虚拟网络 ID 的值进行构造。 它使用的值与使用者的选择匹配。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

网络接口将设置为子网 ID 变量。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>UI 定义

现在，让我们看一下 createUiDefinition.json 文件。 整个文件如下所示：

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

此文件包含一个虚拟网络元素。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

该元素让使用者选择新虚拟网络或现有虚拟网络。

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="新虚拟网络或现有虚拟网络":::

在输出中包含一个值，该值指示使用者是选择了新虚拟网络还是现有虚拟网络。 还有一个托管标识值。

> [!NOTE]
> 托管标识的输出值必须命名为“managedIdentity”。

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>后续步骤

若要详细了解如何创建 UI 定义文件，请参阅[适合 Azure 托管应用程序的创建体验的 CreateUiDefinition.json](create-uidefinition-overview.md)。
