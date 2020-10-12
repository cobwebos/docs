---
title: 了解 Azure 应用程序配置键值存储
description: 了解 Azure 应用配置中的键-值存储，将配置数据存储为键值。 键-值为应用程序设置的表示形式。
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: b1998532c3d9e4272d91280d57d9ea2f6e7a262c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88586386"
---
# <a name="keys-and-values"></a>键和值

Azure 应用配置将配置数据存储为键值。 键值是开发人员使用的“应用程序设置”的一种简单灵活的表示形式。

## <a name="keys"></a>键

键用作键值的标识符，用于存储和检索相应的值。 使用字符分隔符（如 `/` 或 `:`）将键组织到分层命名空间中是一种常见做法。 可使用最适合应用程序的约定。 应用配置将密钥视为一个整体。 不会解析键以弄清楚其名字是如何构成的，也不会强制执行任何规则。

下面是基于组件服务按层次结构组织的键名称的示例：

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

在应用程序框架内使用配置数据可以规定键值的特定命名方案。 例如，Java 的 Spring Cloud 框架定义了 `Environment` 资源，这些资源为 Spring 应用程序提供设置。  这些资源通过包括应用程序名称和配置文件在内的变量进行了参数化 。 Spring Cloud 相关配置数据的键通常以这两个元素开头，由一个分隔符分开。

存储在应用配置中的密钥是区分大小写的、基于 unicode 的字符串。 “app1”和“App1”键在应用程序配置存储区中是有所区分的****。 在应用程序中使用配置设置时，请记住这一点，因为有些框架处理配置键时不区分大小写。 不建议使用大小写来区分键。

可以在键名称中使用任何 unicode 字符，除了 `%`。 键名称不能为 `.` 或 `..`。 键值的组合大小限制为 10 KB。 此限制包括键中的所有字符、其值以及所有相关的可选属性。 在此限制范围内，可以为密钥设置许多层次结构级别。

### <a name="design-key-namespaces"></a>设计键命名空间

命名用于配置数据的密钥有两种通用方法：平面或分层。 从应用程序使用情况的角度来看，这些方法非常相似，但分层命名提供了许多优点：

* 更易于阅读。 分层键名称功能中的分隔符在句子中充当空格。 它们还在单词之间提供自然的分隔。
* 更易于管理。 密钥名称层次结构表示配置数据的逻辑组。
* 更易于使用。 更简单的方法是编写一个查询，该查询在层次结构中模式匹配密钥并仅检索部分配置数据。 此外，许多较新的编程框架对分层配置数据具有本机支持，因此应用程序可以使用特定的配置集。

可以通过多种方式按层次结构组织应用配置中的密钥。 将此类键视为 [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)。 每个分层键是由一个或多个组件组成的资源“路径”，由分隔符联接在一起**。 根据应用程序、编程语言或框架的需要选择要用作分隔符的字符。 在应用配置中为不同的键使用多个分隔符。

### <a name="label-keys"></a>标签键

应用配置中的键值可以选择具有“标签”属性。 标签用于使用相同的键区分键值。 带有“A”标签和“B”标签的“app1”键会在应用程序配置存储区中形成两个单独的键  。 默认情况下，键值没有标签。 若要显式引用不带标签的键值，请使用 `\0`（编码为 `%00` 的 URL）。

标签提供了一种方便的方式来创建键的变体。 标签的常见用途是为同一个键指定多个环境：

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>键值版本

使用标签作为创建键值的多个版本的方法。 例如，可以在标签中输入应用程序版本号或 Git 提交 ID，以标识与特定软件版本关联的键值。

> [!NOTE]
> 如果要查找“更改”的版本，应用程序配置会自动保留过去特定时间段内发生的所有键值更改。 有关详细信息，请参阅[时间点快照](./concept-point-time-snapshot.md)。

### <a name="query-key-values"></a>查询键值

每个键值通过其键以及可以为 `\0` 的标签进行唯一标识。 可以通过指定模式来查询应用程序配置存储区中的键值。 应用程序配置存储区会返回与模式匹配的所有键值，包括对应的值和属性。 在 REST API 调用应用配置时使用以下键模式：

| 键 | 说明 |
|---|---|
| 省略 `key` 或 `key=*` | 匹配所有密钥 |
| `key=abc` | 完全匹配键名称 abc**** |
| `key=abc*` | 匹配以 abc 开头的密钥名称**** |
| `key=abc,xyz` | 匹配密钥名称 abc 或 xyz 。 限制为 5 个 CSV |

还可以包含以下标签模式：

| Label | 说明 |
|---|---|
| 省略 `label` 或 `label=*` | 匹配任何标签，包括 `\0` |
| `label=%00` | 匹配 `\0` 标签 |
| `label=1.0.0` | 完全匹配标签 1.0.0**** |
| `label=1.0.*` | 匹配以 1.0. 开头的标签**** |
| `label=%00,1.0.0` | 匹配标签 `\0` 或 **1.0.0**，限制为五个 CSV |

> [!NOTE]
> `*`、`,` 和 `\` 是查询中的保留字符。 如果在键名称或标签中使用了保留字符，需要在查询中使用 `\{Reserved Character}` 来转义它。

## <a name="values"></a>值

分配给键的值也是 unicode 字符串。 可以将所有 unicode 字符用于值。

### <a name="use-content-type"></a>使用 Content-Type
应用程序配置中的每个键值都有一个内容类型属性。 可以选择使用此属性来存储关于键值中值类型的信息，其有助于应用程序正确处理该信息。 可为 content-type 使用任何格式。 应用程序配置为内置数据类型（如功能标志、Key Vault 引用和 JSON 键值）使用[媒体类型]( https://www.iana.org/assignments/media-types/media-types.xhtml)（也称为 MIME 类型）。

## <a name="next-steps"></a>后续步骤

* [时间点快照](./concept-point-time-snapshot.md)
* [特色管理](./concept-feature-management.md)
* [事件处理](./concept-app-configuration-event.md)
