| **硬件** | |
| --- |---|
| CPU 内核数| 8 |
| RAM| 12 GB|
| 磁盘数目 | 3 <br><br> - OS 磁盘<br> - 进程服务器缓存磁盘<br> - 保留驱动器（用于故障回复）|
| 磁盘可用空间（进程服务器缓存） | 600 GB
| 磁盘可用空间（保留磁盘） | 600 GB|
| **软件** | |
| 操作系统版本 | Windows Server 2012 R2 |
| 操作系统区域设置 | 美国英语|
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server 角色 | 不要启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V |
| **网络** | |
| 网络接口卡类型 | VMXNET3 |
| IP 地址类型 | 静态 |
| Internet 访问权限 | 服务器应该能够直接或通过代理服务器访问以下 URL： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi（横向扩展进程服务器不需要此 URL） <br> - time.nist.gov <br> - time.windows.com |
| 端口 | 443（控制通道协调）<br>9443（数据传输）|
