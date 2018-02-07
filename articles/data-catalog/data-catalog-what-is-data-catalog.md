---
title: "Azure 数据目录简介 | Microsoft Docs"
description: "本文概述 Microsoft Azure 数据目录，包括其功能和旨在解决的问题。 使用数据目录，任何用户均可注册、发现、了解和使用数据源。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: edf6e1d8b86d9773b54353bb70f000bcdf40e5ec
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="what-is-azure-data-catalog"></a>什么是 Azure 数据目录？
Azure 数据目录是完全托管的云服务，其用户可以发现所需的数据源并理解所找到的数据源。 同时，数据目录还可帮助组织从其现有投资中获取更多价值。 

借助数据目录，任何用户（分析人员、数据科学家或开发者）都可以发现、了解和使用数据源。 数据目录包含元数据和批注的众包模型。 它是供组织的所有用户贡献其知识和构建数据社区和文化的一个集中位置。

## <a name="discovery-challenges-for-data-consumers"></a>数据使用者面临的数据发现难题
一直以来，发现企业数据源都是基于部落知识的有机过程。 对于想要从其信息资产中获取最大价值的公司，这种方法存在许多挑战：

* 用户可能并不知道数据源的存在，除非在另一个过程中与之接触。 不存在一个统一注册数据源的位置。
* 除非用户知道数据源的位置，否则其无法使用客户端应用程序连接到数据。 要使用数据，用户需要知道连接字符串或路径。
* 除非用户知道数据源的文档的位置，否则其无法了解数据的预期用途。 数据源和文档可能位于各种位置，并通过各种方式使用。
* 如果用户对信息资产有疑问，他们必须找到负责数据的专家或团队，并在线下向其进行咨询。 数据与专家针对数据用途的观点之间没有明显联系。
* 除非用户了解请求访问数据源的过程，否则，发现数据源及其文档仍无助于访问数据。

## <a name="discovery-challenges-for-data-producers"></a>数据生成者面临的数据发现难题
虽然数据使用者面临上述难题，负责生成和维护信息资产的用户也面临着自身的难题：

* 使用描述性元数据批注数据源通常是徒劳的。 客户端应用程序通常会忽略存储在数据源中的说明。
* 为数据源创建文档通常也是徒劳的。 让文档与数据源保持同步是一项需要持续进行的工作，而用户可能对视为过期的文档缺少信任。
* 创建和维护数据源的文档非常复杂且耗时。 为了让使用数据源的每个用户可随时获取文档，面临的挑战可能更加艰巨。
* 限制对数据源的访问，确保数据使用者知道如何请求访问是一个持续性的挑战。

这些挑战共同形成巨大的障碍，使得公司难以鼓励和推动企业数据的使用及了解。

## <a name="azure-data-catalog-can-help"></a>Azure 数据目录的帮助
数据目录旨在解决这些问题，并帮助企业充分利用现有的信息资产。 数据目录可帮助管理数据的用户更轻松地发现和理解数据源。

数据目录提供基于云的服务，可在其中注册数据源。 数据保留在现有位置，但其元数据的副本将连同数据源位置的引用一起添加到数据目录。 此元数据还会编制索引，方便通过搜索功能轻松发现每个数据源，并让发现数据源的用户理解该数据源。

注册数据源之后，注册数据源的用户或企业中的其他用户可以充实其元数据。 任何用户都可以提供描述、标记或其他元数据（例如请求数据源访问权限的文档和过程）来批注数据源。 此描述性元数据可补充从数据源注册的结构化元数据（例如列名和数据类型）。

注册源的主要目的是发现和理解数据源及其用途。 企业用户可能需要用于商业智能、应用程序开发、数据科学或需要正确数据的任何其他任务的数据。 他们可以使用“数据目录发现”体验快速查找符合其需求的数据、了解数据以评估其适用性，并通过在其所选工具中打开数据源来使用数据。 

与此同时，用户还可通过对已注册的数据源进行标记、记录和批注来参与目录。 他们还可以注册新的数据源，随后目录用户的社区可以发现、了解和使用这些数据源。

![数据目录功能](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>了解有关数据目录的详细信息
若要了解有关数据目录功能的详细信息，请参阅：

* [How to register data sources](data-catalog-how-to-register.md)
* [How to discover data sources](data-catalog-how-to-discover.md)
* [How to annotate data sources](data-catalog-how-to-annotate.md)
* [How to document data sources](data-catalog-how-to-documentation.md)
* [How to connect to data sources](data-catalog-how-to-connect.md)
* [How to work with big data](data-catalog-how-to-big-data.md)
* [How to manage data assets](data-catalog-how-to-manage.md)
* [How to set up the Business Glossary](data-catalog-how-to-business-glossary.md)
* [常见问题](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>后续步骤
若要开始使用数据目录，请转到：
* [Microsoft Azure 数据目录](https://www.azuredatacatalog.com)
* [Azure 数据目录入门](data-catalog-get-started.md)
