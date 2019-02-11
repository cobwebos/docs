---
title: 将 Chef 与 Azure 配合使用
description: 介绍如何使用 Chef 来配置和测试 Azure 基础结构
ms.service: virtual-machines-linux
keywords: azure, chef, devops, 虚拟机, 概述, 自动控制
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: c0ec8b98ff711f8e5746d6d4731266ed4b09cc8f
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658838"
---
# <a name="using-chef-with-azure"></a>将 Chef 与 Azure 配合使用
[Chef](http://www.chef.io) 是一个功能强大的自动化平台，可将 Azure 上的虚拟机基础结构转换为代码。 无论大小如何，Chef 均可自动控制基础结构在网络中配置、部署和托管的方式。

本文介绍使用 Chef 管理 Azure 基础结构的优点。

## <a name="chef-extension-on-azure"></a>Azure 上的 Chef 扩展
在 Azure 门户上使用 [Chef 扩展](https://docs.microsoft.com/azure/chef/chef-extension-portal)预配一个虚拟机，其中 Chef 客户端作为后台服务运行。 预配后，这些虚拟机即可由 Chef 服务器进行管理。

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
在 Azure Cloud Shell 中直接使用 Chef 工作站！ 直接从 Cloud Shell 运行所有 Chef 实用程序和 InSpec。 可以从以下位置利用 Chef 命令：

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

将我们的命令实用程序与 Cloud Shell 中提供的其他工具（例如 `git`、`az-cli` 和 `terraform`）结合使用，并从浏览器编写基础结构和实现合规性自动化。

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>自动控制基础结构、应用以及与一个平台的符合性
公司需要速度和安全性以在数字市场中竞争。 Chef 和 Microsoft 共同帮助个人、团队和企业完成所有这些事情。 借助 Chef Automate 平台，现在可以实现自动化并持续交付基础结构、应用程序，并可以在 Microsoft 资产中实现合规性。

## <a name="test-drive-chef-automate-on-azure"></a>在 Azure 上测试驱动 Chef Automate
由 Chef 提供支持的 [Chef Automate Azure 市场解决方案](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate)允许你以协作方式构建、部署和管理基础结构和应用程序。 只需单击一下，即可访问 Chef Automate 随附的所有商业功能；获得所有内容的端到端可视化，实现持续的符合性，以及利用统一工作流管理所有更改。

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 Chef 创建 Windows 虚拟机](/azure/virtual-machines/windows/chef-automation)
