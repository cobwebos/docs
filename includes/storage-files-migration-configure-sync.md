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
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209579"
---
此步骤将在前面的步骤中将在 Windows Server 上设置的所有资源和文件夹结合在一起。

* 登录到 [Azure 门户](https://portal.azure.com)。
* 找到你的存储同步服务资源。
* 在每个 Azure 文件共享的存储同步服务资源中创建新的*同步组*。 在 Azure 文件同步术语中，Azure 文件共享将成为同步拓扑中的*云终结点，该终结点*是你在创建同步组时所描述的。 创建同步组时，为其提供一个熟悉的名称，以便您可以在此处识别要同步的文件集。 请确保引用具有匹配名称的 Azure 文件共享。
* 创建同步组后，会在同步组列表中看到该行。 单击 "名称" （链接）以显示同步组的内容。 你将在 "云终结点" 下看到你的 Azure 文件共享。
* 找到命令按钮 " *+ 添加服务器终结点*"。 你预配的本地服务器上的文件夹将成为此*服务器终结点*的路径。
