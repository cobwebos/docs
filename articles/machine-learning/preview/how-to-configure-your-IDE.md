---
title: "如何配置 Azure 机器学习工作台以用于 IDE？  | Microsoft Docs"
description: "配置 Azure 机器学习工作台以用于 IDE 的指南。"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 8a4bb8d825f89b08e9b7514d67ae2d2512cceec9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-configure-azure-machine-learning-workbench-to-work-with-an-ide"></a>如何配置 Azure 机器学习工作台以用于 IDE 

Azure 机器学习工作台可配置为用于常用的 Python IDE（集成开发环境）。 它支持数据科学开发体验在数据准备、代码编写、运行跟踪和操作化之间顺畅移动。 目前，支持的 IDE 是：
- Microsoft Visual Studio Code 
- JetBrain PyCharm 

## <a name="configure-workbench"></a>配置工作台
1. 在应用的左上角单击“文件”菜单。 
2. 在浮出控件中选择“配置项目 IDE”选项 
3. 在“名称”字段中键入“`VS Code`”或“`PyCharm`”（随意命名）
4. 在“执行路径”中输入 IDE 可执行文件的位置（输入可执行文件的名称和扩展名即完成）

### <a name="default-install-path-for-visual-studio-code"></a>Visual Studio Code 的默认安装路径  

* Windows 32 位 - `C:\Program Files (x86)\Microsoft VS Code\Code.exe`
* Windows 64 位 - `C:\Program Files\Microsoft VS Code\Code.exe`
* macOS - 选择 .app 路径（例如 `/Applications/Visual Studio Code.app`），应用会为你追加路径的其余部分。 默认情况下，可执行文件的完整路径是 `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`。 如果已在 VS Code 中执行了 `Shell Command: Install 'code' command in PATH` 命令，则也可以在 `/usr/local/bin/code` 中引用 VS Code 脚本

### <a name="default-install-path-for-pycharm"></a>PyCharm 的默认安装路径 

* Windows 32 位 - `C:\Program Files (x86)\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm.exe`。 
* Windows 64 位 - `C:\Program Files\JetBrains\PyCharm Community Edition 2017.2.1\bin\pycharm64.exe`。
* macOS - 选择 .app 路径（例如“/Applications/PyCharm CE.app”），应用会为你追加路径的其余部分。 默认情况下，可执行文件的完整路径是 `/Applications/PyCharm CE.app/Contents/MacOS/pycharm`。 还可以在 bin 文件夹 `/usr/local/bin/charm` 中查找 PyCharm

## <a name="open-project-in-ide"></a>在 IDE 中打开项目 
完成配置后，通过打开 Azure Machine Learning Workbench 中的“文件”菜单并单击“打开项目(<IDE_Name>)”，可以打开 Azure 机器学习项目。 此操作将在配置的 IDE 中打开当前活动项目。 _注意：如果你不在项目中，“打开项目(<IDE_Name>)”将禁用。_

## <a name="configuring-the-integrated-terminal-in-visual-studio-code"></a>在 Visual Studio Code 中配置集成终端

### <a name="windows"></a>Windows 
我们已将默认 shell 重写为 cmd 而非 PowerShell。 单击“打开项目 (<IDE_Name>)”时，将看到以下提示： 

_是否允许 shell `C:\windows\System32\cmd.exe`（定义为工作区设置）在终端中启动？_

回答 `yes` 以允许将 shell 配置为与 Azure ML 工作台命令行接口无缝协作。

### <a name="mac"></a>Mac
若要在 Mac 上使用 Visual Studio Code 的集成终端运行 `az` 命令，需要手动将 `PATH` 设置为与项目的 `.vscode/settings.json` 文件中键 `terminal.integrated.env.osx` 下的 `PATH` 相同的值。 可以通过在终端中运行以下命令来执行此操作：`PATH=<PATH in .vscode/settings>`
