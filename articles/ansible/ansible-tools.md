---
title: 在 Azure 中使用 Ansible 的工具
description: 在 Azure 中安装和使用 Ansible 的单个工具
ms.service: ansible
keywords: ansible, azure, devops, 工具, vs code, visual studio code, 扩展
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>在 Azure 中使用 Ansible 的工具

以下工具可使使用 Ansible 和 Azure 更轻松、更高效。

## <a name="visual-studio-code-extension-for-ansible"></a>适用于 Ansible 的 Visual Studio Code 扩展

[适用于 Ansible 的 Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible)提供了多项从 Visual Studio Code 使用 Ansible 的功能：

- 当你键入时，自动从 Ansible 文档完成 Ansible 指令、模块和插件。
- 创作 Ansible playbook 时，如果单击 &lt;Ctrl>&lt;Space>，将显示代码片段。
- 语法突出显示功能将根据 YAML 语法以不同颜色和字体显示 Ansible playbook 代码。
- 可以从 Visual Studio Code 终端窗口运行 Ansible playbook。
    - （仅限 Windows）可从 Docker 容器运行 Ansible。
    - （Linux 和 macOS）可从 Docker 容器或从本地 Ansible 安装运行 Ansible。 
- 可以从 Azure Cloud Shell 运行 Ansible playbook。