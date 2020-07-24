---
title: 创建 UI 定义引用函数
description: 说明在为引用其他对象的 Azure 门户构造 UI 定义时要使用的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095977"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 引用函数

从 CreateUiDefinition 文件的属性或上下文引用输出时要使用的函数。

## <a name="basics"></a>basics

返回在[基本](create-uidefinition-overview.md#basics)步骤中定义的元素的输出值。 传入元素的名称作为此函数的参数。

若要获取其他步骤中的元素的输出值，请使用[步骤（）](#steps)函数。

下面的示例返回 Basics 步骤中名为 `clusterName` 的元素的输出：

```json
"[basics('clusterName')]"
```

返回的值因检索到的元素类型而异。

## <a name="location"></a>location

返回 Basics 步骤或当前上下文中选择的位置。

下面的示例返回一个值，如下所示 `"westus"` ：

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

返回有关在基本步骤或当前上下文中选择的资源组的详细信息。

如下示例中：

```json
"[resourceGroup()]"
```

返回以下属性：

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

可以获取具有点表示法的任何特定值。

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steps

返回指定步骤中的元素。 传入步骤的名称作为此函数的参数。 从返回的元素中，可以获取特定属性值。

若要获取基本步骤中的元素的输出值，请使用[基本（）](#basics)函数。

下面的示例返回名为的步骤 `vmParameters` 。 在该步骤中，是一个名为的元素 `adminUsername` 。

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>订阅

返回在基本步骤或当前上下文中选择的订阅的属性。

如下示例中：

```json
"[subscription()]"
```

返回以下属性：

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>后续步骤

* 有关开发门户界面的简介，请参阅[Azure 托管应用程序的创建体验中的CreateUiDefinition.js](create-uidefinition-overview.md)。
