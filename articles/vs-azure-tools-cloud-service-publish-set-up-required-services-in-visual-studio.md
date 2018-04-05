---
title: 准备从 Visual Studio 发布或部署云服务 | Microsoft 文档
description: 了解设置云服务和存储帐户服务以及配置 Azure 应用程序的过程。
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 8a7d6f114bfa10170cdfe7126e01a35b02affd20
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>准备从 Visual Studio 发布或部署云服务

若要发布云服务项目，必须设置本文描述的以下服务：

* 一个云服务，用于在 Azure 环境中运行角色，和 
* 一个**存储帐户**，提供对 Blob、队列和表服务的访问权限。

## <a name="create-a-cloud-service"></a>创建云服务

云服务将在 Azure 环境中运行角色。 你可以在 Visual Studio 中或通过 [Azure 门户](https://portal.azure.com/)创建云服务，如以下各节中所述。

### <a name="create-a-cloud-service-from-visual-studio"></a>从 Visual Studio 创建云服务

1. 在以前创建的云服务项目中，右键单击该项目并选择“发布”。
1. 如有必要，使用 Microsoft 或与你的 Azure 订阅相关联的组织帐户登录，然后选择“下一步”以转到“设置”页。
1. 将显示“创建云服务和存储帐户”对话框（如未显示，请从“云服务”列表中选择“新建”）。
1. 为你的云服务输入区分大小写的名称，该名称是组成 URL 的一部分，且必须具有唯一性。 此外，选择区域或地缘组，然后选择“复制”选项。

### <a name="create-a-cloud-service-through-the-azure-portal"></a>通过 Azure 门户创建云服务

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择页面左侧的“云服务(经典)”。
1. 选择“+ 添加”，然后提供所需的信息（DNS 名称、订阅、资源组和位置）。 不需要在此时上传包，因为稍后将在 Visual Studio 中执行这一操作。
1. 选择“创建”以完成过程。

## <a name="create-a-storage-account"></a>创建存储帐户

存储帐户提供对 Blob、队列和表服务的访问权限。 可以通过 Visual Studio 或 [Azure 门户](https://portal.azure.com/)来创建存储帐户。

### <a name="create-a-storage-account-from-visual-studio"></a>在 Visual Studio 中创建存储帐户

1. 在具有以前创建的云服务项目的“解决方案资源管理器”中，找到角色项目中的“连接服务”节点，右键单击，然后选择“添加连接服务”。 （在 Visual Studio 2015 中，右键单击“存储”节点，并选择“创建存储帐户”。）
1. 在显示的“连接服务”列表中，选择“Azure 存储的云存储”。
1. 在出现的“Azure 存储”对话框中，选择“+创建新存储帐户”，随后会出现一个对话框，在其中指定你的订阅、帐户名称、定价层、资源组和位置。
1. 完成操作后，选择“创建”。 新的存储帐户将出现在你订阅中的可用存储帐户列表中。
1. 选择该帐户并选择“添加”。

### <a name="create-a-storage-account-through-the-azure-portal"></a>通过 Azure 门户创建存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择左上角的“+ 新建”。
1. 选择“Azure Marketplace”下的“存储”，然后从右侧选择“存储帐户 - Blob、文件、表、队列”。
1. 提供所需的信息（名称、部署模型等）。
1. 选择“创建”以完成过程。

## <a name="configure-your-app-to-use-the-storage-account"></a>将应用配置为使用存储帐户

创建存储帐户后，从 Visual Studio 连接到该帐户将自动更新项目的服务配置，包括 URL 和访问密钥。

如果使用“添加连接服务”在 Visual Studio 中创建了云服务，则可以通过打开 `ServiceConfiguration.Cloud.cscfg` 和 `ServiceConfiguration.Local.cscfg` 来检查连接。

如果通过 Azure 门户创建了云服务，请按照[在 Visual Studio 中创建存储帐户](#create-a-storage-account-from-visual-studio)中的相同步骤执行，但应选择现有帐户，而不是创建一个新帐户。 然后，Visual Studio 为你更新配置。

若要手动配置设置，请为云服务项目中的适用角色使用 Visual Studio 中的属性页面（右键单击该角色并选择“属性”）。 有关详细信息，请参阅[向存储帐户配置连接字符串](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account)。

### <a name="about-access-keys"></a>有关访问密钥

Azure 门户显示可用于访问每个 Azure 存储服务中资源的 URL，同时还显示帐户的主访问密钥和辅助访问密钥。 可使用这些访问密钥验证对存储服务发出的请求。

辅助访问密钥提供的对存储帐户的访问权与主访问密钥提供的对存储帐户的访问权相同，并在主访问密钥出现问题的情况下作为备份。 此外，建议定期重新生成访问密钥。 可在重新生成主密钥时，修改连接字符串设置以使用辅助密钥，并可在重新生成辅助密钥时修改连接字符串以便重新生成主密钥。

## <a name="next-steps"></a>后续步骤

要详细了解如何将应用从 Visual Studio 发布到 Azure，请参阅[使用 Azure Tools 发布云服务](vs-azure-tools-publishing-a-cloud-service.md)。
