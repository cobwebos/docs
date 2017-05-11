---
title: "Azure Linux 虚拟机文档 - 教程和 API 参考 | Microsoft 文档"
description: "了解如何使用所选的 Linux 分发版创建虚拟机。 文档介绍了创建 VM 模板的不同方式。"
services: virtual-machines\linux
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\linux
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 57ed1bac9af92f56e418d9ddca7a5d7f4752d7ac
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---
<div class="content">
    <h1>Linux 虚拟机</h1>
    <div class="introHolder" style="justify-content: space-between;">
        <div class="intro" style="min-width: 200px">
            <p>Azure Linux 虚拟机使用所选 Red Hat、Ubuntu 或 Linux 分发按需提供具有高可伸缩性且十分安全的虚拟化基础结构。 使用我们的快速入门、教程和示例了解如何创建、配置、管理和缩放 Linux VM。</p>
        </div>
        <a href="https://azure.microsoft.com/en-us/resources/videos/create-linux-virtual-machine/">
            <div class="calloutHolder" style="max-width: 250px">
                <div>
                    <img src="media/index/create-linux-virtual-machine.png" style="width: 250px" />
                </div>
                <div>
                    <p style="margin-top: 0; color: #6e6e6e">创建 Linux 虚拟机。 (4:11)</p>
                </div>
            </div>
        </a>
    </div>
<h2 style="margin-top: 0px; margin-bottom: 0px;">5 分钟快速入门</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">了解如何在运行 Ubuntu 的虚拟机中部署 NGINX Web 服务器：</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>Azure CLI</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>Azure 门户</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    </div>
<div>

<h2 style="margin-top: 36px">分步教程</h2>
<p>了解如何在 Azure 上部署、管理和缩放 Linux VM</p>
<ol>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-vm">创建和管理 Linux VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-disks">创建和管理 VM 磁盘</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-automate-vm-deployment">自动执行 VM 配置</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-custom-images">创建自定义 VM 映像</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-availability-sets">创建高度可用的 VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-create-vmss">创建 VM 规模集</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-load-balancer">对 VM 进行负载均衡</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-virtual-network">管理 VM 和虚拟网络</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-backup-vms">备份 VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-monitoring">监视 VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-azure-security">管理 VM 上的安全性</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd">使用 Jenkins、Docker 和 GitHub 创建 CI/CD 基础结构</a></li>
    
</ol>

<h2 style="margin-top: 36px">免费 PluralSight 视频培训</h2>
<ul class="panelContent cardsW">
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-infrastructure.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>管理基础结构</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-vms.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>虚拟机入门</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-iaas-monitoring.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>IaaS 监视入门</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<h2>示例</h2>
<p>将你的第一个应用程序部署到 Azure。</p>
<ul class="spaced">
    <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Azure CLI</a></li>
    <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
</ul>

<h2 style="margin-top: 36px">引用</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>命令行</h3>
                        <p><a href="/cli/azure/vm">Azure CLI</a></p>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>语言</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/en-us/develop/nodejs/#azure-sdk">Node.js</a></p>
                        <p><a href="http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.compute.html">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST</h3>
                        <p><a href="/rest/api/compute">REST API 参考</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>

