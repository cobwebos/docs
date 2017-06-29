---
title: "将 Azure VM 代理用于与 Jenkins 的持续集成。"
description: "Azure VM 代理从属于 Jenkins。"
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: zh-cn
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>将 Azure VM 代理用于与 Jenkins 的持续集成。

本快速入门介绍如何使用 Jenkins Azure VM 代理插件在 Azure 中创建按需 Linux (Ubuntu) 代理。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 如果尚没有 Jenkins（作为 master），可以从[解决方案模板](install-jenkins-solution-template.md)入手 
* 如果尚没有 Azure 服务主体，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)。

## <a name="install-azure-vm-agents-plugin"></a>安装 Azure VM 代理插件

如果从[解决方案模板](install-jenkins-solution-template.md)开始，Azure VM 代理插件将安装在 Jenkins (master) 中。

否则，从 Jenkins 仪表板内安装 Azure VM 代理插件。

## <a name="configure-the-plugin"></a>配置插件

* 在 Jenkins 仪表板中，单击“管理 Jenkins”->“配置系统”->。 滚动到页面底部，找到带有下拉菜单“添加新云”的部分。 从菜单中选择“Microsoft Azure VM 代理”
* 从“Azure 凭据”下拉菜单中选择现有帐户。  若要添加新的“Microsoft Azure 服务主体”，请输入以下值：订阅 ID、客户端 ID、客户端密码和 OAuth 2.0 令牌终结点。

![Azure 凭据](./media/jenkins-azure-vm-agents/service-principal.png)

* 单击“验证配置”，确保配置文件正确。
* 保存配置，继续进行下一步。

## <a name="template-configuration"></a>模板配置

### <a name="general-configuration"></a>常规配置
接下来，配置模板用于定义 Azure VM 代理。 

* 单击“添加”来添加模板。 
* 为新模板提供名称。 
* 在标签中输入“ubuntu”。 在作业配置期间使用此标签。
* 从组合框中选择所需区域。
* 选择所需的 VM 大小。
* 指定 Azure 存储帐户名称或留空使用默认名称“jenkinsarmst”。
* 指定保留时间（分钟）。 此设置定义 Jenkins 在自动删除闲置代理前可等待的分钟数。 如果不希望自动删除闲置代理，请指定为 0。

![常规配置](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>映像配置

若要创建 Linux (Ubuntu) 代理，请选择“映像引用”并使用以下配置作为示例。 请参阅 [Azure 应用商店](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1)获取 Azure 支持的最新映像。

* 映像发布者：Canonical
* 映像产品：UbuntuServer
* 映像 SKU：14.04.5-LTS
* 映像版本：最新
* OS 类型：Linux
* 启动方法：SSH
* 提供管理员凭据
* 在 VM 初始化脚本中输入：
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![映像配置](./media/jenkins-azure-vm-agents/image-config.png)

* 单击“验证模板”来验证配置。
* 单击“保存” 。

## <a name="create-a-job-in-jenkins"></a>在 Jenkins 中创建作业

* 在 Jenkins 仪表板中，单击“新建项目”。 
* 输入一个名称，选择“自由风格项目”并单击“确定”。
* 在“常规”选项卡中，选择“限制可运行项目的位置”并在标签表达式中键入“ubuntu”。 现在可在下拉菜单中看到“ubuntu”。
* 单击“保存” 。

![设置作业](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>生成新项目

* 返回 Jenkins 仪表板。
* 右键单击新创建的作业，然后单击“立即生成”。 将开始生成。 
* 生成完成后，转到“控制台输出”。 可看到该生成在 Azure 上远程执行。

![控制台输出](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>引用

* Azure Friday 视频：[Continuous Integration with Jenkins using Azure VM agents](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents)（使用 Azure VM 代理与 Jenkins 持续集成）
* 支持信息和配置选项：[Azure VM 代理 Jenkins 插件 Wiki](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


