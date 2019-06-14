---
title: 创建 Web 服务终结点
titleSuffix: Azure Machine Learning Studio
description: 在 Azure 机器学习工作室中创建 Web 服务终结点。 Web 服务中的每个终结点都是独立处理、限制和托管的。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: ac434a696f6e77e5ce61b430232166e7727eda38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751172"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>为部署的 Azure 机器学习工作室 Web 服务创建终结点

> [!NOTE]
> 本主题介绍适用于经典机器学习 Web 服务的技术  。

部署 Web 服务之后，将为该服务创建默认终结点。 该默认终结点可以使用其 API 密钥调用。 可以从 Web 服务门户添加更多具有自身密钥的终结点。
Web 服务中的每个终结点都是独立处理、限制和托管的。 每个终结点唯一 URL 和身份验证密钥，可以将其分发给客户。

## <a name="add-endpoints-to-a-web-service"></a>将终结点添加到 Web 服务

可以使用 Azure 机器学习 Web 服务门户将终结点添加到 Web 服务。 创建终结点后，可以通过同步 API、Batch API 和 Excel 工作表来使用它。

> [!NOTE]
> 如果 Web 服务已添加其他终结点，则无法删除默认终结点。

1. 在机器学习工作室的左侧导航栏中，单击“Web 服务”。
2. 在“Web 服务”仪表板的底部，单击“管理终结点”  。 Azure 机器学习 Web 服务门户可打开 Web 服务的终结点页。
3. 单击“新建”  。
4. 键入新终结点的名称及说明。 终结点名称的长度必须少于或等于 24 个字符，并且必须由小写字母或数字组成。 选择日志记录级别以及是否启用示例数据。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](web-services-logging.md)。

## <a id="scaling"></a> 通过添加其他终结点来扩展 Web 服务

默认情况下，每个已发布的 Web 服务配置为支持 20 个并发请求，并且最高可达 200 个并发请求。 Azure 机器学习工作室自动优化设置以为 Web 服务提供最佳性能，并忽略门户值。

如果计划调用带有高于并发调用值 200 所支持的负载的 API，应在同一个 Web 服务上创建多个终结点。 然后可在所有终结点上随机分发负载。

Web 服务的扩展是常见任务。 扩展的一些原因是为了支持超过 200 个并发请求、通过多个终结点增加可用性或为 Web 服务提供单独的终结点。 通过 [Azure 机器学习 Web 服务](https://services.azureml.net/)门户为同一个 Web 服务添加其他终结点，可增加规模。

请记住，如果不使用相应的高速率调用 API，使用高并发数可能有害。 如果在针对高负载配置的 API 上放置相对较低的负载，则可能看到偶发的超时和/或延迟峰值。

同步 API 通常在需要低延迟的情况下使用。 此处的延迟表示 API 完成一个请求所需的时间，不考虑任何网络延迟。 假设有一个带有 50 毫秒延迟的 API。 若要使用限制级别高和最大并发调用 = 20 完全消耗可用容量，每秒需要调用此 API 20 * 1000 / 50 = 400 次。 进一步扩展，假设 50 毫秒的延迟，最大并行调用 200 允许每秒调用 API 4000 次。

## <a name="next-steps"></a>后续步骤

[如何使用 Azure 机器学习 Web 服务](consume-web-services.md)。
