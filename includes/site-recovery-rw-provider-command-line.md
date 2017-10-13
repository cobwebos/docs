UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <用于数据传输的 IP 地址] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

参数：

* /ServerMode：必需。 指定是要同时安装配置服务器和进程服务器，还是只安装进程服务器。 输入值：CS、PS。
* InstallLocation：必需。 用于安装组件的文件夹。
* /MySQLCredsFilePath。 必需。 MySQL 服务器凭据存储到的文件路径。 该文件的格式应是：
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath。 必需。 保管库凭据文件的位置
* /EnvType。 必需。 安装类型。 值：VMware、NonVMware
* /PSIP 和 /CSIP。 必需。 进程服务器和配置服务器的 IP 地址。
* /PassphraseFilePath。 必需。 通行短语文件的位置。
* /BypassProxy。 可选。 指定配置服务器不使用代理连接到 Azure。
* /ProxySettingsFilePath。 可选。 代理设置（默认代理需要身份验证，或自定义代理）。 该文件的格式应是：
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort。 可选。 用于复制数据的端口号。
* SkipSpaceCheck。 可选。 跳过缓存的空间检查。
* AcceptThirdpartyEULA。 必需。 接受第三方 EULA。
* ShowThirdpartyEULA。 必需。 显示第三方 EULA。 如果作为输入提供，将忽略所有其他参数。
