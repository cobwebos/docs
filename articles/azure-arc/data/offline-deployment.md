---
title: 脱机部署
description: 脱机部署使你能够从专用容器注册表拉取容器映像，而不是从 Microsoft 容器注册表拉取。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 575903654a165bef0d09ac6abf0793af3f6784e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934793"
---
# <a name="offline-deployment-overview"></a>脱机部署概述

通常，在创建 Azure Arc 数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组时使用的容器映像会直接从 Microsoft 容器注册表 (MCR) 提取。 在某些情况下，你部署到的环境将无法连接到 Microsoft 容器注册表。  对于这种情况，可以使用计算机提取容器映像 _，该计算机可以访问_ Microsoft 容器注册表，然后将其标记并推送到专用容器注册表 _，该注册表可从_ 要部署启用了 Azure Arc 的数据服务的环境中连接。

由于为启用了 Azure Arc 的数据服务提供了每月更新，因此，最好使用脚本执行此过程，将容器映像提取、标记并推送到专用容器注册表。  此脚本可以自动执行，也可以手动运行。

可在 Azure Arc GitHub 存储库中找到 [示例脚本](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) 。

> [!NOTE]
> 此脚本需要安装 python 和 [DOCKER CLI](https://docs.docker.com/install/)。

脚本将以交互方式提示输入以下信息。  或者，如果你想要运行脚本而不使用交互式提示，则可以在运行脚本之前设置相应的环境变量。

|Prompt|环境变量|注意|
|---|---|---|
|提供源容器注册表-按 ENTER 可使用 `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|通常，你将从 Microsoft 容器注册表中提取映像，但如果你使用不同的注册表参加个人预览版，则可以使用提供给你的信息作为预览计划的一部分。|
|提供源容器注册表存储库-按 ENTER 可使用 `arcdata` ：|SOURCE_DOCKER_REPOSITORY|如果要从 Microsoft 容器注册表拉取，存储库将为 `arcdata` 。|
|提供源容器注册表的用户名-按 ENTER 以使用 none：|SOURCE_DOCKER_USERNAME|仅在从需要登录的源中拉取容器映像时才提供值。  Microsoft 容器注册表不需要登录名。|
|提供源容器注册表的密码-按 ENTER 以使用 none：|SOURCE_DOCKER_PASSWORD|仅在从需要登录的源中拉取容器映像时才提供值。  Microsoft 容器注册表不需要登录名。 这是屏蔽密码提示。  如果键入或粘贴密码，则不会显示密码。|
|为源中的映像提供容器映像标记，按 ENTER 以使用 " `<current monthly release tag>` "：|SOURCE_DOCKER_TAG|默认标记名称将每月更新一次，以反映 Microsoft 容器注册表中当前发布的月份和年份。|
|提供目标容器注册表 DNS 名称或 IP 地址：|TARGET_DOCKER_REGISTRY|目标注册表 DNS 名称或 IP 地址。  这是将映像推送 _到_的注册表。|
|提供目标容器注册表存储库：|TARGET_DOCKER_REPOSITORY|目标注册表上要将映像推送到的存储库。|
|提供目标容器注册表的用户名-按 enter 以使用 none：|TARGET_DOCKER_USERNAME|用于登录到目标容器注册表的用户名（如果有）。|
|提供目标容器注册表的密码-按 enter 以使用 "无"：|TARGET_DOCKER_PASSWORD|用于登录到目标容器注册表的密码（如果有）。 这是屏蔽密码提示。  如果键入或粘贴密码，则不会显示密码。|
|为目标中的映像提供容器映像标记：|TARGET_DOCKER_TAG|通常，您将使用与源相同的标记，以避免混淆。|