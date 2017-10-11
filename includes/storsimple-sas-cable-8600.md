<!--author=alkohli last changed:02/22/16-->

#### <a name="to-attach-the-sas-cables"></a>若要将 SAS 电缆连接
1. 标识主机箱和 EBOD 机箱。 两个机箱可通过查看其各自的底板来标识。 请参阅下的图以获取指导。 
   
    ![备份箱和 EBOD 机箱的底板](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **主机箱和 EBOD 机箱的背面视图**
   
   | Label | 描述 |
   |:--- |:--- |
   | 1 |主机箱 |
   | 2 |EBOD 机箱 |
2. 找到主机箱和 EBOD 机箱上的序列号。 序列号不干胶标签附在每个机箱的后耳。 序列号必须在两个机箱上完全相同。 [请联系 Microsoft 支持部门](../articles/storsimple/storsimple-contact-microsoft-support.md)立即如果序列号不匹配。 请参阅下图以查找序列号。
   
    ![显示序列号的机箱的背面视图](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **序列号不干胶标签的位置**
   
   | Label | 描述 |
   |:--- |:--- |
   | 1 |机箱的耳部 |
3. 使用提供的 SAS 电缆将 EBOD 机箱连接到主机箱，如下所示：
   
   1. 标识主机箱和 EBOD 机箱上的四个 SAS 端口。 SAS 端口在主机箱上标记为 EBOD 并对应于 EBOD 机箱上的端口 A，将 SAS 电缆图中，连接下面所示。
   2. 使用提供的 SAS 电缆连接 EBOD 端口 a。
   3. 在控制器 0 上的 EBOD 端口应连接到 EBOD 控制器 0 上的端口 A。 控制器 1 上的 EBOD 端口应连接到 EBOD 控制器 1 上的端口 A。 请参阅下图的指南。 
      
      ![SAS 电缆连接到你的设备](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **将 SAS 电缆连接**
      
      | Label | 描述 |
      |:--- |:--- |
      | A |主机箱 |
      | B |EBOD 机箱 |
      | 1 |控制器 0 |
      | 2 |控制器 1 |
      | 3 |EBOD 控制器 0 |
      | 4 |EBOD 控制器 1 |
      | 5, 6 |SAS 端口在主机箱 (标为 EBOD) |
      | 7, 8 |EBOD 机箱 (端口 A) 上的 SAS 端口 |

