---
title: 什么是学术知识 API？
titlesuffix: Azure Cognitive Services
description: 学术知识 API 可用于解释用户查询，并从学术图谱中检索大量信息。
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: b15ed5e2b31ed817d3f6558858e2b7285f98a70f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551715"
---
# <a name="academic-knowledge-api"></a>学术知识 API

欢迎使用学术知识 API。 借助此服务，可解释用户查询中的学术意图，并可从 Microsoft Academic Graph (MAG) 中检索到大量信息。 MAG 知识库是涵盖整个网页的一个异构实体图谱，它包含实现学术活动建模的多种实体，例如研究领域、作者、机构、论文、地点和活动。 

MAG 数据源自必应 Web 索引和必应的内部知识库。 由于必应索引持续不断，此 API 将通过必应发现和索引获取网页中的最新信息。 基于此数据库，学术知识 API 实现了由知识驱动的交互对话，从而将被动搜索与主动建议体验、丰富的研究论文图谱搜索结果以及一组论文和相关实体的属性值直方图分布进行无缝结合。

要详细了解 Microsoft Academic Graph，请参阅 [https://aka.ms/academicgraph](https://aka.ms/academicgraph)。

学术知识 API 已从认知服务预览版迁移至认知服务实验室。 项目的新主页是 [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge)。 截至 2018 年 5 月 24 日，现有 API 密钥将继续有效。 该日期之后，请生成新的 API 密钥。 请注意，现有密钥过期后，付费预览版将立即失效。 如果 API 的免费层不满足你的需求，请与我们的团队联系。 

## <a name="features"></a>功能
学术知识 API 由 4 个相关的 REST 终结点构成：  
  1. interpret - 解释自然语言用户查询字符串。 返回带批注的解释，实现可预测用户输入内容的丰富的搜索框自动完成体验。  
  2. evaluate - 评估查询表达式并返回学术知识实体结果。  
  3. calchistogram - 计算由查询表达式返回的学术实体属性值的分布直方图，例如按年份分布给定作业的引文。  
  
综合使用这些 API 方法，可创建丰富的语义搜索体验。 假定具有用户查询字符串，则 interpret 方法提供一个带批注的查询和一个结构化的查询表达式，同时选择性地基于潜在学术数据的语义完成用户查询。 例如，如果用户键入字符串“latent s”，则 interpret 方法可提供一组已排名的解释，提示用户可能正在搜索研究领域“latent semantic analysis”（潜在语义分析）、论文“latent structure analysis”（语义结构分析）或其他以“latent s”开始的实体表达式。 此信息可用于快速引导用户获得所需搜索结果。

evaluate 方法可用于从学术知识库中检索出一组相匹配的论文实体，而 calchistogram 方法可用于计算一组论文实体的属性值分布，这可用于进一步筛选搜索结果。        

## <a name="getting-started"></a>入门 
如需详细文档，请参阅左侧的副标题。  请注意，为了提高示例的可读性，REST API 调用包含未经 URL 编码的字符（如空格）。  你的代码需要应用适当的 URL 编码。
