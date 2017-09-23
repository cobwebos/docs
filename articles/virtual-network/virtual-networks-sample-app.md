---
title: "用于 DMZ 的 Azure 示例应用程序 |Microsoft Docs"
description: "在创建外围网络后部署此简单的 Web 应用程序以测试流量流方案"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 60340ab7-b82b-40e0-bd87-83e41fe4519c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.translationtype: Human Translation
ms.sourcegitcommit: cb2e480a45871ad0c956dc976de955ca48ecdfd0
ms.openlocfilehash: 8506238e41c5d9dac8d76d729d4919b30a0528b9
ms.contentlocale: zh-cn
ms.lasthandoff: 01/05/2017

---
# <a name="sample-application-for-use-with-dmzs"></a>用于 DMZ 的示例应用程序
[返回安全边界最佳实践页面][HOME]

这些 PowerShell 脚本可以在 IIS01 和 AppVM01 服务器本地运行以安装和设置简单的 Web 应用程序，此 Web 应用程序把后端 AppVM01 服务器的内容显示在前端 IIS01 服务器的 html 网页中。

此应用程序为许多 DMZ 示例提供简单的测试环境，并演示终结点、NSG、UDR 和防火墙规则的更改如何影响流量流。

## <a name="firewall-rule-to-allow-icmp"></a>允许 ICMP 的防火墙规则
此简单的 PowerShell 语句可以在任意 Windows VM 上运行以允许 ICMP (Ping) 流量。 通过允许 ping 协议通过 Windows 防火墙（对于大部分 Linux 发行版而言，ICMP 默认打开），此防火墙更新将简化测试和故障排除。

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

如果使用以下脚本，则此防火墙规则添加是第一条语句。

## <a name="iis01---web-application-installation-script"></a>IIS01 - Web 应用程序安装脚本
此脚本将：

1. 打开本地服务器 Windows 防火墙上的 IMCPv4 (Ping) 以简化测试
2. 安装 IIS 和.Net Framework v4.5
3. 创建 ASP.NET 网页和 Web.config 文件
4. 更改默认应用程序池以简化文件访问
5. 为你的管理员帐户和密码设置匿名用户

当 RDP 进入 IIS01 时，应在本地运行此 PowerShell 脚本。

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Clasic Pipeline to remote file access will work easier
    Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - 文件服务器安装脚本
此脚本为此简单的应用程序设置后端。 此脚本将：

1. 打开防火墙上的 IMCPv4 (Ping) 以简化测试
2. 为网站创建一个目录
3. 创建一个通过 Web 页面远程访问的文本文件
4. 将目录和文件上的权限设置为“匿名”以允许访问
5. 关闭 IE Enhanced Security 以允许更轻松地从此服务器浏览 

> [!IMPORTANT]
> **最佳实践**：绝不会关闭生产服务器上的 IE Enhanced Security，而且从生产服务器在 Web 上冲浪通常不是个好主意。 另外，打开文件共享以进行匿名访问不是个好主意，但在此处为简单起见进行了此操作。
> 
> 

当 RDP 进入 AppVM01 时，应在本地运行此 PowerShell 脚本。 需要以管理员身份运行 PowerShell 以确保成功执行。

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successfull!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - DNS 服务器安装脚本
此示例应用程序中没有包括设置 DNS 服务器的脚本。 如果防火墙规则、NSG 或 UDR 测试需要包括 DNS 流量，则需要手动设置 DNS01 服务器。 两个示例的网络配置 xml 文件和 Resource Manager 模板都包括 DNS01（作为主 DNS 服务器）和由级别 3 托管的公共 DNS 服务器（作为备份 DNS 服务器）。 级别 3 DNS 服务器将会成为用作非本地流量的实际 DNS 服务器，由于未设置 DNS01，因此不会发生本地网络 DNS。

## <a name="next-steps"></a>后续步骤
* 在 IIS 服务器上运行 IIS01 脚本
* 在 AppVM01 上运行文件服务器脚本
* 浏览到 IIS01 上的公共 IP 来验证生成

<!--Link References-->
[HOME]: ../best-practices-network-security.md

