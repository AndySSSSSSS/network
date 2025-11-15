# 目录
- [Cisco ISE](#CiscoISE)







# Before upgrade

1. 與客戶確認 **設備信息**、**目前版本**、**升級版本**、**升級目的**，檢查官方的upgrade path

   \- *Serial Number*、型號

   \- 是否為修補漏洞？

   \- 是否要新增功能？

   \- 是否是因為現有版本 EOL (End of Life)？

   

2. 與客戶協商**downtime**、確認**升級影響範圍**，並提交**upgrade plan**

   \- downtime包括upgrade time、configuration、testing、rolling back time

   \- 重要的業務需要考慮是否需要提前準備spare parts，pre-configure ，確保時間來不及時即插即用業務不中斷

   

3. 開support case

4. Download the firmware and check the md5

   \- 下載客戶目前版本，確保回退時可用。

   

5. 確認service list，確保升級前後服務一致不受影響

6. 兼容性測試(Lab實驗)

7. backup configuration



---

# After upgrade

1. 基本檢查

   \- 設備版本、介面、VLAN、Routing

2. 服務檢查

   \- 防火牆 policy、VPN

3. HA/Cluster同步

4. 客戶業務測試

5. 保存升級紀錄log ，以及向客戶提交升級報告

6. Standby



---



# CiscoISE

1. 首先，進入Cisco ISE Web GUI界面，查看系統版本。

<img src="../image/image-20251009183200701.png" width="800">


2. 備份ISE和Software準備

   - 設定和操作資料（Administration > System > Backup & Restore）

   - 備份系統日誌（Operations > Troubleshoot > Download Logs）

   - 匯出憑證和私鑰（Administration > System > Certificates > System Certificates）

   - 確認有效的 ISE 升級路徑（Administration > System > Maintenance > Repository）
  
     <img src="../image/image-20251010183246658.png" width="800">
  
     <img src="../image/image-20251010183610878.png" width="800">
  
     <img src="../image/image-20251010183516663.png" width="800">

   - 確認更新的版本並下載

     <img src="../image/image-20251009190821082.png" width="800">

3. 查看 ISE 升級指南和發行說明（http://cisco.com/go/ise）

   <img src="../image/image-20251009184345827.png" width="800">

4. 點擊左上角Menu -> Administration -> Upgrade。

<img src="../image/image-20251009183704225.png" width="800">

3. 根據提示逐步點擊。

   <img src="../image/image-20251009183921295.png" width="800">

   <img src="../image/image-20251009183936286.png" width="800">

   <img src="../image/image-20251011140848563.png" width="800">
   

<img src="../image/image-20251011141024925.png" width="800">

4. 升級完成後查看系統版本，然後再進行相關檢查。

   <img src="../image/image-20251011175907551.png" width="800">

---

# CiscoPI

## Web GUI approach

1. 首先，進入Cisco PI Web GUI界面，查看系統版本。

<img src="../image/image-20251016170337156.png" width="800">

2. 檢查官方的upgrade path

<img src="../image/image-20251016170730744.png" width="800">

3. 備份PI

<img src="../image/image-20251016231233031.png" width="800">

<img src="../image/image-20251016230908809.png" width="800">

4. Software準備

<img src="../image/image-20251016231341379.png" width="800">

5. upgrade

   從 **PI 3.4 起**，Cisco 將升級功能 **完全移除 GUI的Upgrade按鈕**，patch可以通過Administration → Software Update → Software Images直接更新

## CLI approach

參考網站：https://rscciew.wordpress.com/2014/08/23/prime-infrastructure-1-3-to-2-1-upgrade/

``` command
查看版本
show version
建立儲存庫
repository sftp
url sftp://192.168.118.128/home/andy/Downloads
user andy password plain 1
備份資料
backup 202510161800 repository sftp
```

<img src="../image/image-20251016182116308.png" width="800">

``` txt
application upgrade <filename> <repository_name>
application upgrade PI-Upgrade-36x_37x_to_3.8.0.0.310.tar.gz sftp
```

<img src="../image/image-20251016220304850.png" width="800">

``` txt
更新完成後檢查版本和相關配置測試
```

<img src="../image/image-20251016225722019.png" width="800">



---

# Cisco ASA

``` command
show version
```

<img src="../image/image-20251025175342981.png" width="800">

``` txt
check upgrade path
```

<img src="../image/image-20251025175551490.png" width="800">

``` txt
基本配置
enable
configure terminal

# 设置管理接口
interface Management0/0
 nameif management
 security-level 100
 ip address 192.168.118.130 255.255.255.0
 no shutdown

# 开启管理接口允许 ping
management-access management

# 允许 ICMP（ping）
access-list management_acl extended permit icmp any any
access-group management_acl in interface management

# 默认路由（若需要出网或跨段）
route management 0.0.0.0 0.0.0.0 192.168.118.1

# 启用管理服务（如 SSH、HTTP）
ssh 192.168.118.0 255.255.255.0 management
http 192.168.118.0 255.255.255.0 management

#SCP（Secure Copy Protocol，安全复制协议）
ssh scopy enable

# 保存配置
write memory

```

``` command
#備份內容
backup location disk0:
copy disk0:/ciscoasa.backup.2025-10-25-210000.tar.gz scp://andy:1@192.168.118.128/Downloads/
copy running-config scp://andy:1@192.168.118.128/Downloads/
```

<img src="../image/image-20251025214211483.png" width="800">

``` command
#下載firmware
copy scp://andy:1@192.168.118.128/Downloads/asa9-22-2-14-smp-k8.bin flash:/
flash 是主要的存储设备，用于存储 操作系统镜像 和 启动文件。
disk 是辅助存储设备，用于存储其他 非系统文件，如日志、配置文件、备份等。
```

<img src="../image/image-20251025220559960.png" width="800">

``` command
downgrade flash:/asa9-22-2-14-smp-k8.bin flash:/
```

<img src="C:\Users\andys\Pictures\Screenshots\螢幕擷取畫面 2025-10-25 220844.png" alt="螢幕擷取畫面 2025-10-25 220844" style="zoom:80%;" />
<img src="../image/螢幕擷取畫面 2025-10-25 220844.png" width="800">

<img src="../image/image-20251025221723041.png" width="800">



---

# Cisco ASA with firepower

``` txt
查看版本
```

<img src="../image/image-20251104174046141.png" width="800">

``` txt
upgrade path
```

<img src="../image/image-20251104205342998.png" width="800">

``` txt
备份 Backup and Restore -> Manual Backup -> Back Up Now
```

<img src="../image/image-20251104174957444.png" width="800">

``` txt
升级 Updates -> System Upgrade -> Browse
```

<img src="../image/image-20251104175829712.png" width="800">

<img src="../image/image-20251104181244978.png" width="800">

``` txt
upgrade完
```

<img src="../image/image-2025110420502962.png" width="800">



---

# Palo Alto Firewall

1. 查看版本

<img src="../image/image-20251026160847366.png" width="800">

2. 查看升級路徑

<img src="../image/image-20251028151753939.png" width="800">

3. 儲存目前設定檔的備份

   選擇**設備(Device)** > **設定(Setup)** > **操作(Operations)** 並點選 **匯出命名配置快照(Export named configuration snapshot)**

   選擇**running-config.xml**
   
   导出系统日志 **設備(Device)** > **支援(Support )** > **生成技術支持文件(Generate Tech Support)** 



4. upgrade

   **软件(Software)** > **左下角 立即檢查(Check now)**
   
   由於查不到所以上客戶支援平台找
   
   <img src="../image/image-20251027201715774.png" width="800">

<img src="../image/image-20251028144035157.png" width="800">

由於沒有license，不允許升級，實驗進行至此。(理論上按升級就正常升)

<img src="../image/image-20251028145444942.png" width="800">



---

# Check Point Firewall

``` txt
查看版本（show version all）
```

<img src="../image/image-20251028200122842.png" width="800">

``` txt
upgrade path，以發版本最低要求
```

<img src="../image/image-20251103200743517.png" width="800">

``` txt
back up : Maintenance > System Backup
```

<img src="../image/image-20251028195603475.png" width="800">

``` txt
Upgrades (CPUSE) > Status and Actions
```

<img src="../image/image-20251029140319669.png" width="800">

``` txt
進行two-step upgrade提示失敗
**Import of package Check_Point_R82_jumbo_hf_main_Bundle_T39_FULL.tgz Failed**
 This package is not supported by current Deployment Agent build.
 Please install CPUSE version 2474 or above to import the package.
 
 找到合適的Check Point Upgrade Service Engine (CPUSE) - Gaia Deployment Agent
 https://support.checkpoint.com/results/sk/sk92449
```

<img src="../image/image-20251031223740865.png" width="800">

``` txt
import upgrade file and install
```

<img src="../image/image-20251104091723477.png" width="800">

``` txt
upgrade完成後檢查
```

<img src="../image/image-20251104142256734.png" width="800">



---

# FortiGate firewall

``` txt
查看DHCP分配的IP
show system interface #不一定有
get system interface physical port1

也可以设置成static mode
config system interface
edit port1
set static mode
set ip 192.168.118.130/24
set allowaccess ping http ssh
end

然后进入后台界面，查看version
```

<img src="../image/image-20251105214703687.png" width="800">

```txt
upgrade path https://docs.fortinet.com/upgrade-tool/fortigate
```

<img src="../image/image-20251105215003810.png" width="800">

``` txt
backup
```

<img src="../image/image-20251105214824742.png" width="800">

``` txt
upgrade
```

<img src="../image/image-20251105214307012.png" width="800">

``` txt
upgrade完成
```

<img src="../image/image-20251105215304331.png" width="800">



---

# Forti Analyzer

``` txt
查看version
```

<img src="../image/image-20251106095726667.png" width="800">

``` txt
upgrade path https://docs.fortinet.com/upgrade-tool/fortianalyzer
```

<img src="../image/image-20251105215740785.png" width="800">

``` txt
backup
```

<img src="../image/image-20251106100203543.png" width="800">

``` txt
upgrade
```

<img src="../image/image-20251106100415315.png" width="800">

``` txt
由于没有有效的合约，不能进行升级
```

<img src="../image/image-20251106100516556.png" width="800">



---

# H3C router

``` txt
show version
```

<img src="../image/image-20251115100313817.png" width="800">

``` txt
升级前备份系统运行文件
boot-loader file boot flash:/VSR1000-CMW710-BOOT-R1340P16-X64.bin system flash:/VSR1000-CMW710-SYSTEM-R1340P16-X64.bin backup
```

<img src="../image/image-20251115102259366.png" width="800">

``` txt
因為沒有H3C的權限下載firmware文件，所以還是使用原來的firmware文件作為實驗upgrade file。

boot-loader file flash:/VSR1000-X64.ipe main
```

<img src="../image/image-20251115110003375.png" width="800">



---

# H3C Switch

``` txt
show version
```

<img src="../image/image-20251115120847285.png" width="800">





---

# Huawei router

``` txt
检查版本 display version
```

<img src="../image/image-20251110182308609.png" width="800">

``` txt
查看升级指南
```

<img src="../image/image-20251112180035272.png" width="800">

``` txt
配置FTP(AR1000v)
aaa
local-user ftpuser password irreversible-cipher Huawei@123
local-user ftpuser privilege level 15
local-user ftpuser service-type ftp
local-user ftpuser ftp-directory flash:/
quit

#指定接口
ftp server permit interface GigabitEthernet 0/0/0
#所有接口
ftp server permit interface all

ftp server enable
```

``` txt
指定下一次的startup firmware，然后reboot
startup system-software flash:/AR1000V_V300R022C00SPC100.cc
save
```

<img src="../image/image-20251110183719511.png" width="800">

``` txt
升级完成
```

<img src="../image/image-20251110184114255.png" width="800">

<img src="../image/image-20251110184235794.png" width="800">

<img src="../image/image-20251110184404535.png" width="800">



---

# Huawei USG firewall

``` txt
display version
```

<img src="../image/image-20251110202821600.png" width="800">

``` txt
查看升级指南
```

<img src="../image/image-20251114175736845.png" width="800">

``` txt
华为support拒绝了我需要的firmware，因此我将使用虚拟机原有的firmware当作升级版本。
```

<img src="../image/image-20251114175901782.png" width="800">

``` txt
升级步骤完成
```

<img src="../image/image-20251114180504895.png" width="800">





---

# Cisco Switch

``` command
show version
```

<img src="../image/image-20251103174153444.png" width="800">

``` commads
copy usbflash0:cat9k_lite_iosxe.17.12.05.SPA.bin flash:
install add file flash:cat9k_lite_iosxe.17.12.05.SPA.bin activate commit prompt-level none
```

<img src="../image/image-20251103174305323.png" width="800">

```command
show version
```

<img src="../image/image-20251103174449084.png" width="800">

---

# Cisco Router

``` txt
show version
```

<img src="../image/image-20251029152801765.png" width="800">

``` txt
Upgrade Guide
```

<img src="../image/image-20251029152937168.png" width="800">

``` txt
#下載firmware
copy scp://andy:1@192.168.118.128/Downloads/c8000v-universalk9.17.15.03a.SPA.bin flash:/
```

<img src="../image/image-20251029152616754.png" width="800">

``` txt
install add file flash:c8000v-universalk9.17.15.03a.SPA.bin activate commit prompt-level none
```

<img src="../image/image-20251029153335842.png" width="800">

``` txt
升級完成檢查
```

<img src="../image/image-20251029153813580.png" width="800">





---

# 問題合集

1. Cisco ISE 和 Cisco PI 在建立SFTP時，都出現了無法建立SSH session。

   解決方法：設備與 SFTP Server 的 SSH 握手階段 (加密演算法不相容)

​		調整SFTP Server 支援舊協定，在ubuntu中加入以下內容

``` command
進入編輯
sudo nano /etc/ssh/sshd_config
添加
KexAlgorithms +diffie-hellman-group1-sha1
Ciphers +aes128-cbc
MACs +hmac-sha1
重啟ssh
sudo systemctl restart sshd
```

2. Cisco ISE 和 Cisco PI，FTP 和 SFTP 路徑可參加下面(路徑會有差異)

``` command
repository ftp
url ftp://192.168.118.128/Downloads
user andy password plain 1

repository sftp
url sftp://192.168.118.128/home/andy/Downloads
user andy password plain 1
```

3. 升級或更新都應該要把patch或firmware都下載到設備中，以防設備Down了找不到firmware(在ISE和PI忘記做)

``` command
copy SFTP://<IP-Address>/<filename> <system_position>
copy SFTP://192.168.118.128/PI-Upgrade-36x_37x_to_3.8.0.0.310.tar.gz disk:/defaultRepo
```

4. EVE-ng 添加設備位置，该路径下有dynamips、iol和qemu三个目录，分别对应image、bin和qcow2三种格式的镜像

``` txt
/opt/unetlab/addons/qemu/<device_name>/<device_name>.qcow2
```

5. Checkpoint Firewall实验遇到空间不足的问题

``` txt
expert
查看 /opt、/var/log、/ 的可用空间。
df -h
查看分區
lvm lvs
找到 /dev/mapper/vg_splat-lv_current
查看物理磁碟與可用空間，看PFree
lvm pvs
增加空间
lvm lvextend -L +10G /dev/mapper/vg_splat-lv_current
調整檔案系統大小（df -T /）（看到類似/dev/mapper/vg_splat-lv_current on / type xfs (rw,...)）
xfs_growfs /
查看结果
df -h /
```

``` txt
进入expert模式可以查看
显示not enough space可以通过lvm_manager查看，里面有一项是upgrade reserved。
或者通过Web界面，Maintanence -> Snapshot 可以看到有多少可用空间。
**沒有可用空間原因是由於CheckPoint有一個計算公式，即使你還有未分配空間也未必有安裝預留空間。
我的解决方法是直接把磁盘的容量由100G增加到200G。這個空間是不能手動分配的。
```

<img src="C:\Users\andys\AppData\Roaming\Typora\typora-user-images\image-20251103224135165.png" alt="image-20251103224135165" style="zoom: 33%;" />

6. EVE中建立可上网的Cloud与window连接

``` txt
***********Windows通过宿主机 NAT 上网****************
**************在EVE上贴上如下指令********************
# 开启 IP 转发
sudo sysctl -w net.ipv4.ip_forward=1
# 添加 NAT 规则（假设 eth0 是外网接口）
EXT_IF=eth0
sudo iptables -t nat -A POSTROUTING -o $EXT_IF -j MASQUERADE
# 保存配置
sudo apt install iptables-persistent
sudo netfilter-persistent save
# 重启EVE
reboot

*****在EVE的poject上，把could mgmt 与 Window 连接*****

window通过DHCP分配IP

```

7. EVE改静态 IP 地址

``` txt
ifconfig

sudo vi /etc/network/interfaces

auto pnet0
iface pnet0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1

```

8. EVE安裝

``` txt
```









