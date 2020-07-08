---
title: 配置 Azure 文件同步
description: 配置 Azure 文件同步。跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 562099c65273cad50596268b89055ec78d92d18a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82143556"
---
此步骤将在前面的步骤中设置在 Windows Server 实例上的所有资源和文件夹结合在一起。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 找到你的存储同步服务资源。
1. 在每个 Azure 文件共享的存储同步服务资源中创建新的*同步组*。 在 Azure 文件同步术语中，Azure 文件共享将成为同步拓扑中的一个*云终结点，该终结点*是你在创建同步组时所描述的。 创建同步组时，请为它提供一个熟悉的名称，以便在此处识别要同步的文件集。 请确保引用具有匹配名称的 Azure 文件共享。
1. 创建同步组后，该同步组的行会出现在同步组列表中。 选择 "名称" （链接）以显示同步组的内容。 你将在**云终结点**下看到你的 Azure 文件共享。
1. 找到 " **+ 添加服务器终结点**" 按钮。 你预配的本地服务器上的文件夹将成为此*服务器终结点*的路径。
