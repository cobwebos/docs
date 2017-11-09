| **硬件** | |
| --- |---|
| CPU 内核数| 8 |
| RAM| 12 GB|
| 磁盘数目 | 3 <br><br> - OS 磁盘<br> - 进程服务器缓存磁盘<br> - 保留驱动器（用于故障回复）|
| 磁盘可用空间（进程服务器缓存） | 600 GB
| 磁盘可用空间（保留磁盘） | 600 GB|
| **软件** | |
| 操作系统版本 | Windows Server 2012 R2 <br> Windows Server 2016 |
| 操作系统区域设置 | 美国英语|
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 角色 | 不要启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V |
| 组策略| 不应在服务器上启用以下组策略 <br> - 阻止访问命令提示符 <br> - 阻止访问注册表编辑工具 <br> - 信任文件附件的逻辑 <br> - 打开脚本执行 <br> **注意：**可以在[此处](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)找到有关这些组策略的详细信息|
| Internet Information Service (IIS) 配置 | - 无预先存在的默认网站 <br> - 启用[匿名身份验证](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - 启用 [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) 设置  <br> - 不应在端口 443 上侦听预先存在的网站/应用程序<br>|
| **网络** | |
| 网络接口卡类型 | VMXNET3 |
| IP 地址类型 | 静态 |
| Internet 访问权限 | 服务器应该能够直接或通过代理服务器访问以下 URL： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi（横向扩展进程服务器不需要此 URL） <br> - time.nist.gov <br> - time.windows.com |
| 端口 | 443（控制通道协调）<br>9443（数据传输）|
