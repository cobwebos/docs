---
title: 在 Azure 开发测试实验室中配置和使用公共环境 | Microsoft Docs
description: 本文介绍如何在 Azure 开发测试实验室中配置和使用公共环境（Git 存储库中的 Azure 资源管理器模板）。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721687"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置和使用公共环境
Azure 开发测试实验室包含 [Azure 资源管理器模板的公共存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments)，可以使用此存储库来创建环境，而无需自行连接到外部 GitHub 源。 此存储库包含常用的模板，例如 Azure Web 应用、Service Fabric 群集和 SharePoint 场开发环境。 此功能类似于针对所创建的每个实验室包含的项目的公共存储库。 借助环境存储库，只需提供极少量的输入参数，即可快速开始使用预先编写的环境模板，在实验室中获得 PaaS 资源的顺畅入门体验。 

## <a name="configuring-public-environments"></a>配置公共环境
实验室所有者可在创建实验室期间为实验室启用公共环境存储库。 若要为实验室启用公共环境，请在创建实验室时，为“公共环境”字段选择“打开”。 

![为新实验室启用公共环境](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


对于现有实验室，未启用公共环境存储库。 请手动启用该存储库，以使用其中的模板。 对于使用资源管理器模板创建的实验室，默认也已禁用存储库。

可以使用以下步骤为实验室启用/禁用公共环境，并使实验室用户只能使用特定的环境： 

1. 选择实验室的“配置和策略”。 
2. 在“虚拟机基础”部分，选择“公共环境”。
3. 若要为实验室启用公共环境，请选择“是”。 否则请选择“否”。 
4. 如果启用了公共环境，则默认会启用存储库中的所有环境。 可以取消选择某个环境，使实验室用户不能使用它。 

![公共环境页](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>以实验室用户的身份使用环境模板
实验室用户只需在实验室页上的工具栏中选择“+添加”，即可基于已启用的环境模板列表创建一个新环境。 基础列表包含列表顶部的实验室管理员启用的公共环境模板。

![公共环境模板](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>后续步骤
此存储库是一个开源存储库，欢迎对其供稿，添加自己常用的和有帮助的资源管理器模板。 若要供稿，只需针对该存储库提交拉取请求即可。  
