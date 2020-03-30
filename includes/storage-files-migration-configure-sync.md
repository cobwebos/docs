---
title: 配置 Azure 文件同步
description: 配置 Azure 文件同步。在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209579"
---
此步骤将您在之前的步骤中在 Windows Server 上设置的所有资源和文件夹结合在一起。

* 登录到[Azure 门户](https://portal.azure.com)。
* 查找存储同步服务资源。
* 在每个 Azure 文件共享的存储同步服务资源中创建新*的同步组*。 在 Azure 文件同步术语中，Azure 文件共享将成为您通过创建同步组描述的同步拓扑中的*云终结点*。 创建同步组时，请为它指定一个熟悉的名称，以便在此处识别同步的文件集。 请确保引用具有匹配名称的 Azure 文件共享。
* 创建同步组后，您将看到同步组列表中显示的行。 单击名称（链接）以显示同步组的内容。 您将在"云终结点"下看到 Azure 文件共享。
* 找到命令按钮以 *= 添加服务器终结点*。 预配的本地服务器上的文件夹将成为此*服务器终结点*的路径。
