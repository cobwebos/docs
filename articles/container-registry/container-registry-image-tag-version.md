---
title: Azure 容器注册表中的标记和版本映像
description: 标记和版本控制 Docker 容器映像的最佳实践
services: container-registry
author: stevelasker
ms.service: container-registry
ms.topic: article
ms.date: 07/10/2019
ms.author: stevelas
ms.openlocfilehash: ea7c0831f4ecc345cbcd8a9b8eb6d6566e8c5023
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297770"
---
# <a name="recommendations-for-tagging-and-versioning-container-images"></a>标记和版本控制容器映像的建议

将容器映像推送到容器注册表, 然后部署它们时, 需要提供映像标记和版本控制策略。 本文介绍两种方法, 其中每个方法都适合容器生命周期:

* **稳定标记**-你重复使用的标记, 例如, 指示*mycontainerimage: 1.0*的主要或次要版本。
* **唯一标记**-向注册表推送的每个映像的不同标记, 如*mycontainerimage: abc123*。

## <a name="stable-tags"></a>稳定标记

**建议**：使用稳定标记来维护容器生成的**基本映像**。 避免部署具有稳定标记, 因为这些标记会继续接收更新, 并可能会导致生产环境中的不一致。

*稳定标记*意味着开发人员或生成系统可以继续提取特定标记, 这将继续获取更新。 稳定不表示内容已冻结。 相反, 稳定意味着映像应该在该版本的意图上稳定。 为保持 "稳定", 可能会对应用安全修补程序或框架更新提供服务。

### <a name="example"></a>示例

框架团队随附1.0 版。 他们知道他们会提供更新, 其中包括次更新。 为了支持给定的主要和次要版本的稳定标记, 它们具有两组稳定标记。

* `:1`–主要版本的稳定标记。 `1`表示 "最新" 或 "最新" 1. * 版本。
* `:1.0`-版本1.0 的稳定标记, 使开发人员可以绑定到1.0 的更新, 并在发布时不会前滚到1.1。

即使当前主版本是`:latest`什么, 团队也使用标记, 该标记指向最新的稳定标记。

当基础映像更新可用或者框架的任何类型的服务版本时, 具有稳定标记的映像将更新为最新的摘要, 表示该版本的最新稳定版本。

在这种情况下, 主要和次要标记都在继续提供服务。 通过基本映像方案, 映像所有者可以提供服务映像。

## <a name="unique-tags"></a>唯一标记

**建议**：使用唯一标记进行**部署**, 尤其是在可在多个节点上缩放的环境中。 你可能需要特意部署一致版本的组件。 如果你的容器重启或 orchestrator 扩展了多个实例, 则你的主机不会意外地提取较新的版本, 这与其他节点不一致。

唯一标记是指推送到注册表中的每个映像都具有唯一标记。 不重复使用标记。 要生成唯一标记, 可以遵循多种模式, 其中包括:

* **日期时间戳**-此方法相当常见, 因为你可以清楚地判断映像是何时生成的。 但是, 如何将它关联回您的生成系统呢？ 是否需要同时查找已完成的生成？ 你处于哪个时区？ 是否所有生成系统都已校准到 UTC？
* **Git 提交**–此方法可用于开始支持基本映像更新。 如果基本映像更新发生, 则生成系统将使用与上一个生成相同的 Git 提交来启动。 但是, 基本映像包含新内容。 通常, Git commit 提供*半*稳定标记。
* **清单摘要**-推送到容器注册表的每个容器映像都与清单关联, 并由唯一的 SHA-256 哈希或 digest 标识。 尽管它是唯一的, 但它很长, 难于阅读, 并且与您的生成环境不相关。
* **生成 ID** -此选项最适合, 因为它可能是增量的, 它使你可以与特定生成关联, 以查找所有项目和日志。 但就像清单摘要一样, 人们很难阅读。

  如果你的组织有多个生成系统, 则在此选项中, 用生成系统名称作为标记的`<build-system>-<build-id>`前缀会变体:。 例如, 你可以将 API 团队的 Jenkins 生成系统和 web 团队的 Azure Pipelines 生成系统中的生成区分开来。

## <a name="next-steps"></a>后续步骤

有关本文中的概念的更多详细讨论, 请参阅博客文章[Docker 标记:标记和版本控制 docker 映像](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/)的最佳做法。

若要最大程度地利用 Azure 容器注册表, 请参阅[Azure 容器注册表的最佳实践](container-registry-best-practices.md)。

<!-- IMAGES -->


<!-- LINKS - Internal -->

