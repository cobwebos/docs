---
title: 模板函数
description: 介绍在 Azure 资源管理器模板中检索值、处理字符串和数字以及检索部署信息时所用的函数。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: fbd82f89ed9a97a3f376a9ed6eaa8ae3760759ff
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982372"
---
# <a name="arm-template-functions"></a>ARM 模板功能

本文介绍可在 Azure 资源管理器 （ARM） 模板中使用的所有功能。 若要了解如何在模板中使用函数，请参阅[模板语法](template-expressions.md)。

若要创建自己的函数，请参阅[用户定义函数](template-syntax.md#functions)。

部署到资源组、订阅、管理组或租户时，大多数函数的工作方式相同。 一些函数不能在所有作用域中使用。 下面列出了它们。

<a id="array" aria-hidden="true" />
<a id="coalesce" aria-hidden="true" />
<a id="concatarray" aria-hidden="true" />
<a id="contains" aria-hidden="true" />
<a id="createarray" aria-hidden="true" />
<a id="empty" aria-hidden="true" />
<a id="first" aria-hidden="true" />
<a id="intersection" aria-hidden="true" />
<a id="json" aria-hidden="true" />
<a id="last" aria-hidden="true" />
<a id="length" aria-hidden="true" />
<a id="min" aria-hidden="true" />
<a id="max" aria-hidden="true" />
<a id="range" aria-hidden="true" />
<a id="skip" aria-hidden="true" />
<a id="take" aria-hidden="true" />
<a id="union" aria-hidden="true" />

## <a name="array-and-object-functions"></a>数组和对象函数

Resource Manager 提供以下用于处理数组和对象的函数。

* [阵 列](template-functions-array.md#array)
* [合并](template-functions-array.md#coalesce)
* [Concat](template-functions-array.md#concat)
* [包含](template-functions-array.md#contains)
* [创建Array](template-functions-array.md#createarray)
* [空](template-functions-array.md#empty)
* [第一](template-functions-array.md#first)
* [交集](template-functions-array.md#intersection)
* [Json](template-functions-array.md#json)
* [最后](template-functions-array.md#last)
* [length](template-functions-array.md#length)
* [分钟](template-functions-array.md#min)
* [麦克斯](template-functions-array.md#max)
* [范围](template-functions-array.md#range)
* [跳](template-functions-array.md#skip)
* [采取](template-functions-array.md#take)
* [联盟](template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>比较函数

Resource Manager 提供了多个用于在模板中进行比较的函数。

* [equals](template-functions-comparison.md#equals)
* [less](template-functions-comparison.md#less)
* [lessOrEquals](template-functions-comparison.md#lessorequals)
* [greater](template-functions-comparison.md#greater)
* [greaterOrEquals](template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="date-functions"></a>日期函数

资源管理器提供以下函数，用于处理日期。

* [日期时间添加](template-functions-date.md#datetimeadd)
* [utcNow](template-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>部署值函数

Resource Manager 提供以下函数，用于从与部署相关的模板和值部分获取值：

* [部署](template-functions-deployment.md#deployment)
* [环境](template-functions-deployment.md#environment)
* [参数](template-functions-deployment.md#parameters)
* [变量](template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>逻辑函数

资源管理器提供以下用于逻辑条件的函数：

* [和](template-functions-logical.md#and)
* [bool](template-functions-logical.md#bool)
* [if](template-functions-logical.md#if)
* [不](template-functions-logical.md#not)
* [或](template-functions-logical.md#or)

<a id="add" aria-hidden="true" />
<a id="copyindex" aria-hidden="true" />
<a id="div" aria-hidden="true" />
<a id="float" aria-hidden="true" />
<a id="int" aria-hidden="true" />
<a id="minint" aria-hidden="true" />
<a id="maxint" aria-hidden="true" />
<a id="mod" aria-hidden="true" />
<a id="mul" aria-hidden="true" />
<a id="sub" aria-hidden="true" />

## <a name="numeric-functions"></a>数字函数

Resource Manager 提供以下用于处理整数的函数：

* [添加](template-functions-numeric.md#add)
* [复制索引](template-functions-numeric.md#copyindex)
* [div](template-functions-numeric.md#div)
* [浮动](template-functions-numeric.md#float)
* [Int](template-functions-numeric.md#int)
* [分钟](template-functions-numeric.md#min)
* [麦克斯](template-functions-numeric.md#max)
* [国防部](template-functions-numeric.md#mod)
* [mul](template-functions-numeric.md#mul)
* [子](template-functions-numeric.md#sub)

<a id="extensionResourceId" aria-hidden="true" />
<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />
<a id="subscriptionResourceId" aria-hidden="true" />
<a id="tenantResourceId" aria-hidden="true" />

## <a name="resource-functions"></a>资源函数

Resource Manager 提供以下用于获取资源值的函数：

* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [listAccountSas](template-functions-resource.md#list)
* [listKeys](template-functions-resource.md#listkeys)
* [listSecrets](template-functions-resource.md#list)
* [列表*](template-functions-resource.md#list)
* [供应商](template-functions-resource.md#providers)
* [参考](template-functions-resource.md#reference)
* [资源组](template-functions-resource.md#resourcegroup)- 只能在部署到资源组时使用。
* [resourceId](template-functions-resource.md#resourceid) - 可在任何作用域使用，但有效参数会根据范围而变化。
* [订阅](template-functions-resource.md#subscription)- 只能在部署到资源组或订阅时使用。
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)

<a id="base64" aria-hidden="true" />
<a id="base64tojson" aria-hidden="true" />
<a id="base64tostring" aria-hidden="true" />
<a id="concat" aria-hidden="true" />
<a id="containsstring" aria-hidden="true" />
<a id="datauri" aria-hidden="true" />
<a id="datauritostring" aria-hidden="true" />
<a id="emptystring" aria-hidden="true" />
<a id="endswith" aria-hidden="true" />
<a id="firststring" aria-hidden="true" />
<a id="guid" aria-hidden="true" />
<a id="indexof" aria-hidden="true" />
<a id="laststring" aria-hidden="true" />
<a id="lastindexof" aria-hidden="true" />
<a id="lengthstring" aria-hidden="true" />
<a id="padleft" aria-hidden="true" />
<a id="replace" aria-hidden="true" />
<a id="skipstring" aria-hidden="true" />
<a id="split" aria-hidden="true" />
<a id="startswith" aria-hidden="true" />
<a id="string" aria-hidden="true" />
<a id="substring" aria-hidden="true" />
<a id="takestring" aria-hidden="true" />
<a id="tolower" aria-hidden="true" />
<a id="toupper" aria-hidden="true" />
<a id="trim" aria-hidden="true" />
<a id="uniquestring" aria-hidden="true" />
<a id="uri" aria-hidden="true" />
<a id="uricomponent" aria-hidden="true" />
<a id="uricomponenttostring" aria-hidden="true" />

## <a name="string-functions"></a>字符串函数

Resource Manager 提供以下用于处理字符串的函数：

* [base64](template-functions-string.md#base64)
* [base64ToJson](template-functions-string.md#base64tojson)
* [base64ToString](template-functions-string.md#base64tostring)
* [Concat](template-functions-string.md#concat)
* [包含](template-functions-string.md#contains)
* [dataUri](template-functions-string.md#datauri)
* [dataUriToString](template-functions-string.md#datauritostring)
* [空](template-functions-string.md#empty)
* [结束与](template-functions-string.md#endswith)
* [第一](template-functions-string.md#first)
* [格式](template-functions-string.md#format)
* [Guid](template-functions-string.md#guid)
* [索引](template-functions-string.md#indexof)
* [最后](template-functions-string.md#last)
* [lastIndexOf](template-functions-string.md#lastindexof)
* [length](template-functions-string.md#length)
* [新吉德](template-functions-string.md#newguid)
* [垫左](template-functions-string.md#padleft)
* [取代](template-functions-string.md#replace)
* [跳](template-functions-string.md#skip)
* [分裂](template-functions-string.md#split)
* [开始与](template-functions-string.md#startswith)
* [string](template-functions-string.md#string)
* [子](template-functions-string.md#substring)
* [采取](template-functions-string.md#take)
* [到下](template-functions-string.md#tolower)
* [到上](template-functions-string.md#toupper)
* [修剪](template-functions-string.md#trim)
* [uniqueString](template-functions-string.md#uniquestring)
* [Uri](template-functions-string.md#uri)
* [uriComponent](template-functions-string.md#uricomponent)
* [uriComponentToString](template-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>后续步骤

* 有关 ARM 模板中部分的说明，请参阅创作[ARM 模板](template-syntax.md)
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](linked-templates.md)
* 要迭代创建资源类型时指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。
* 要查看如何部署已创建的模板，请参阅[使用 ARM 模板部署应用程序](deploy-powershell.md)
