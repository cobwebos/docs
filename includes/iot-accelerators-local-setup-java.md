---
title: include 文件
description: include 文件
services: iot-accelerators
author: v-krghan
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/25/2019
ms.author: v-krghan
ms.custom: include file
ms.openlocfilehash: 79fcadc75876af39d65dcfce88dac6802d55efd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450748"
---
## <a name="download-the-source-code"></a>下载源代码

远程监视源代码存储库中包含运行微服务 Docker 映像时所需的源代码和 Docker 配置文件。

若要克隆并创建本地版本的存储库，请通过命令行环境导航到本地计算机上的合适文件夹。 然后，运行下列命令集之一来克隆 java 存储库：

若要下载最新版本的 java 微服务实现，请运行：


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> 这些命令除了下载用于在本地运行微服务的脚本，还下载所有微服务的源代码。 虽然运行 Docker 中的微服务时不需要源代码，但是如果以后计划修改解决方案加速器并在本地测试更改，则源代码非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的 Azure 服务。 使用以下脚本来部署 Azure 服务。 以下脚本示例假定你在 Windows 计算机上使用 java 存储库。 若在其他环境中工作，请相应地调整路径、文件扩展名和路径分隔符。

### <a name="create-new-azure-resources"></a>创建新的 Azure 资源

如果尚未创建所需的 Azure 资源，请执行以下步骤：

1. 在命令行环境中，导航到已克隆的存储库副本中的 **\services\scripts\local\launch** 文件夹。

1. 运行以下命令来安装 **pcs** CLI 工具并登录到你的 Azure 帐户：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 运行 **start.cmd** 脚本。 该脚本将提示你输入以下信息：
   * 解决方案名称。
   * 要使用的 Azure 订阅。
   * 要使用的 Azure 数据中心的位置。

     脚本使用解决方案名称在 Azure 中创建资源组。 此资源组包含解决方案加速器使用的 Azure 资源。 不再需要相关的资源后，可以删除此资源组。

     此脚本还向本地计算机添加一组前缀为 **PCS** 的环境变量。 这些环境变量提供远程监视，以便能够从 Azure 密钥保管库资源中读取的详细的信息。 此密钥保管库资源是远程监视将读取的位置从其配置值。

     > [!TIP]
     > 在此脚本完成时，它还会将环境变量保存到一个名为 **\<your home folder\>\\.pcs\\\<solution name\>.env** 的文件中。 可以将它们用于将来的解决方案加速器部署。 请注意，运行 **docker-compose** 时，在本地计算机上设置的任何环境变量将覆盖 **services\\scripts\\local\\.env** 文件中的值。

1. 退出命令行环境。

### <a name="use-existing-azure-resources"></a>使用现有的 Azure 资源

如果已创建了所需的 Azure 资源，请在本地计算机上创建相应的环境变量。
设置以下环境变量：
* **PCS_KEYVAULT_NAME** -Azure 密钥保管库资源的名称
* **PCS_AAD_APPID** -AAD 应用程序 ID
* **PCS_AAD_APPSECRET** -AAD 应用程序机密

将此 Azure 密钥保管库资源中读取配置值。 这些环境变量可以保存在**\<主文件夹\>\\.pcs\\\<解决方案名称\>.env**从部署的文件。 请注意，运行 **docker-compose** 时，在本地计算机上设置的环境变量将覆盖 **services\\scripts\\local\\.env** 文件中的值。

某些微服务所需的配置存储中的实例**Key Vault**创建初始部署。 根据需要应修改在密钥保管库中的相应变量。