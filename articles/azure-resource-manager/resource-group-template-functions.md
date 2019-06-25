---
title: Resource Manager 模板函数 | Microsoft Docs
description: 介绍在 Azure 资源管理器模板中检索值、处理字符串和数字以及检索部署信息时所用的函数。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: 201fea91aba2a14720a7c28df5128f94e9e327fd
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205432"
---
# <a name="azure-resource-manager-template-functions"></a>Azure 资源管理器模板函数
本文介绍可以在 Azure 资源管理器模板中使用的所有函数。 若要了解如何在模板中使用函数，请参阅[模板语法](resource-group-authoring-templates.md#syntax)。

若要创建自己的函数，请参阅[用户定义函数](resource-group-authoring-templates.md#functions)。

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

* [array](resource-group-template-functions-array.md#array)
* [coalesce](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [contains](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [empty](resource-group-template-functions-array.md#empty)
* [first](resource-group-template-functions-array.md#first)
* [intersection](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [last](resource-group-template-functions-array.md#last)
* [length](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [range](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [take](resource-group-template-functions-array.md#take)
* [union](resource-group-template-functions-array.md#union)

<a id="equals" aria-hidden="true" />
<a id="less" aria-hidden="true" />
<a id="lessorequals" aria-hidden="true" />
<a id="greater" aria-hidden="true" />
<a id="greaterorequals" aria-hidden="true" />

## <a name="comparison-functions"></a>比较函数
Resource Manager 提供了多个用于在模板中进行比较的函数。

* [equals](resource-group-template-functions-comparison.md#equals)
* [less](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [greater](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" aria-hidden="true" />
<a id="parameters" aria-hidden="true" />
<a id="variables" aria-hidden="true" />

## <a name="deployment-value-functions"></a>部署值函数
Resource Manager 提供以下函数，用于从与部署相关的模板和值部分获取值：

* [deployment](resource-group-template-functions-deployment.md#deployment)
* [参数](resource-group-template-functions-deployment.md#parameters)
* [variables](resource-group-template-functions-deployment.md#variables)

<a id="and" aria-hidden="true" />
<a id="bool" aria-hidden="true" />
<a id="if" aria-hidden="true" />
<a id="not" aria-hidden="true" />
<a id="or" aria-hidden="true" />

## <a name="logical-functions"></a>逻辑函数
资源管理器提供以下用于逻辑条件的函数：

* [and](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

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

## <a name="numeric-functions"></a>数值函数
Resource Manager 提供以下用于处理整数的函数：

* [add](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" aria-hidden="true" />
<a id="list" aria-hidden="true" />
<a id="providers" aria-hidden="true" />
<a id="reference" aria-hidden="true" />
<a id="resourcegroup" aria-hidden="true" />
<a id="resourceid" aria-hidden="true" />
<a id="subscription" aria-hidden="true" />

## <a name="resource-functions"></a>资源函数
Resource Manager 提供以下用于获取资源值的函数：

* [listAccountSas](resource-group-template-functions-resource.md#list)
* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [list*](resource-group-template-functions-resource.md#list)
* [providers](resource-group-template-functions-resource.md#providers)
* [reference](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [subscription](resource-group-template-functions-resource.md#subscription)

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

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [contains](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [empty](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [first](resource-group-template-functions-string.md#first)
* [format](resource-group-template-functions-string.md#format)
* [guid](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [last](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [length](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [replace](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [字符串](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [take](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [uri](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>后续步骤
* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)
* 若要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)
