---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172851"
---
## <a name="set-up-the-development-environment"></a>设置开发环境

本部分演示如何设置开发环境。 这包括创建 ASP.NET MVC 应用、添加“连接服务”连接、添加控制器，以及指定所需的命名空间指令。

### <a name="create-an-aspnet-mvc-app-project"></a>创建 ASP.NET MVC 应用项目

1. 打开 Visual Studio。

1. 从主菜单中，选择“文件”   > “新建”   > “项目”  。

1. 在“新建项目”  对话框中，选择“Web”   > “ASP.NET Web 应用程序(.NET Framework)”  。 在“名称”  字段中，指定“StorageAspNet”  。 选择“确定”  。

    ![“新建项目”对话框屏幕截图](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. 在“新建 ASP.NET Web 应用程序”  对话框中，选择“MVC”  ，然后选择“确定”  。

    ![“新建 ASP.NET Web 应用程序”对话框屏幕截图](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用连接服务连接到 Azure 存储帐户

1. 在“解决方案资源管理器”  中，右键单击项目。

1. 从上下文菜单中，选择“添加”   > “连接服务”  。

1. 在“连接服务”  对话框中，选择“Azure 存储的云存储”  。

    ![“连接服务”对话框屏幕截图](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. 在“Azure 存储”  对话框中，选择要在本教程中使用的 Azure 存储帐户。 若要创建新的 Azure 存储帐户，请选择“创建新的存储帐户”  并填写表单。 选择现有的存储帐户或创建新的存储帐户后，选择“添加”  。 Visual Studio 会安装 Azure 存储的 NuGet 包，并将一个存储连接字符串添加到 Web.config  。

1. 在中**解决方案资源管理器**，右键单击**依赖关系**，选择**管理 NuGet 包**，并添加对最新版本的 NuGet 包引用Microsoft.Azure.ConfigurationManager。

> [!TIP]
> 若要了解如何使用 [Azure 门户](https://portal.azure.com)创建存储帐户，请参阅[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。
>
> 也可以使用 [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md)、[Azure CLI](../articles/storage/common/storage-azure-cli.md) 或 [Azure Cloud Shell](../articles/cloud-shell/overview.md) 创建存储帐户。
