# cisco三层交换机常用案例



## 配置策略路由并应用到指定vlan--实验环境

```
# 新建"响应时间报告器"
ip sla monitor 1
 type echo protocol ipIcmpEcho 192.168.2.2 source-interface FastEthernet1/0
 timeout 1000
 threshold 3
 frequency 5
ip sla monitor schedule 1 life forever start-time now

# 调用"响应时间报告器"进行跟踪  
track 1 rtr 1 reachability 
注：设置跟踪器对象为1，响应时间报告器对象为1，进行测试是否可达

# 进行策略路由设置，以序列为顺序执行：
route-map ipTOtelecom permit 10
 match ip address ipTOtelecom
 set ip next-hop verify-availability 192.168.2.2 1 track 1	#1为要插入下一跳列表的序列顺序
 set ip next-hop 192.168.1.1	#当没有匹配到地址列表ipTOtelecom时将走默认路由。此结论经过论证
##route-map ipTOtelecom deny 20   #默认会添加一条拒绝所有未匹配的条目
注：
* 当有多个set动作时，先设置的优先级最高，将会一直使用优先级最高的。
* 当优先级最高的链路故障时，如果没有设置链路状态检测，则永远会走故障链路。
* 当配置了链路健康状态检测时，set动作将会比没有设置链路健康状态检测的动作高。
* 当设置: set ip next-hop 192.168.2.2 192.168.1.1时，则永远走192.168.2.2这个下一跳
* 当192.168.2.2down掉时，仍然走192.168.2.2，所以此配置是错误的，应该配置链路状态健康检测。

 
# 需要先开启routing模式，否则后面无法应用策略到vlan接口，配置完并reload重启
show sdm prefer                 
  The current template is "desktop routing" template. 
sdm prefer routing
reload

# 应用策略到vlan接口
interface vlan 10
  ip policy route-map ipTOtelecom

```



## Cisco 3750交换机配置Response Time Report--生产环境
```
SWI_CORE01(config)#ip sla 10 
SWI_CORE01(config-ip-sla)#icmp-echo 192.168.10.253 source-ip 192.168.10.252 
SWI_CORE01(config-ip-sla-echo)#timeout 1000
SWI_CORE01(config-ip-sla-echo)#threshold 3 
SWI_CORE01(config-ip-sla-echo)#frequency 5
SWI_CORE01(config)#ip sla schedule 10 life forever start-time now 
SWI_CORE01(config)#track 10 ip sla 10 reachability 
SWI_CORE01#show track brief       
10      ip sla    10                   reachability     Up  
SWI_CORE01(config)#ip route 119.23.85.229 255.255.255.255 192.168.10.253 track 10
注：用于静态路由配置，当track链路不Up时，则走此静态路由，否则不走
```



## 通过VACL方式实现vlan之间隔离

```
# 涉及的vlan
vlan 70
vlan 10

# 配置访问控制列表，设置允许的条目
SWI_CORE01(config)#ip access-list extended denyVLAN70
SWI_CORE01(config-ext-nacl)#10 permit ip 192.168.17.0 0.0.0.255 192.168.10.0 0.0.0.255

# 配置vlan策略，最后必须设定一条允许所有规则
SWI_CORE01(config)#vlan access-map denyVLAN70 10
SWI_CORE01(config-access-map)#match ip address denyVLAN70
SWI_CORE01(config-access-map)#action drop		#执行匹配的数据包 丢弃
SWI_CORE01(config)#vlan access-map denyVLAN70 20
SWI_CORE01(config-access-map)#action forward	#没匹配denyVLAN70的数据包 将被允许转发

# 设置vlan策略使用升序顺序执行
SWI_CORE01(config)#vlan internal allocation policy ascending

# 应用规则denyVLAN70到vlan70，source地址是vlan70
SWI_CORE01(config)#vlan filter denyVLAN70 vlan-list 70

---命令---
access-list 101 permit ip host 192.168.13.200 host 192.168.13.111
vlan access-map denyhost1 10			
 action drop
 match ip address 101
vlan access-map denyhost1 20
 action forward
!
vlan filter denyhost1 vlan-list 30		#应用PBR到VLAN列表
---------
```



## 通过VLAN之间ACL方式实现vlan之间隔离--实验环境

```
# 配置vlan虚接口
Switch(config)# vlan 10 
Switch(config)# vlan 20
Switch(config)# vlan 30
Switch(config)# int vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0 // 配置vlan10虚端口IP
Switch(config-if)# int vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# int vlan 30
Switch(config-if)# ip address 192.168.30.1 255.255.255.0

# 配置ACL
Switch(config)# access-list 101 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
Switch(config)# access-list 102 permit ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255

# 应用ACL至VLAN端口
Switch(config)# int vlan 10
Switch(config-if)# ip access-group 101 in
Switch(config)# int vlan 20
Switch(config-if)# ip access-group 102 in

注:
* 在acl中当源地址段为应用 vlan接口的ip段时，就是用in方向
* 当目的地址段为应用vlan接口的ip段时，就是用out方向
* 一般情况下，通过VLAN之间ACL实现访问控制比较方便，但是当VLAN的端口比较分散时，采用VACL相对而言就要简单很多


---命令---
Extended IP access list jackli
    10 deny tcp host 172.168.2.219 host 172.16.30.100 eq 22 (109 matches)
    50 deny icmp host 172.168.2.219 host 172.16.30.100 (251 matches)
    100 permit ip any any (381 matches)
SWI_CORE02(config)#interface vlan 90
ip access-group jackli out		#应用策略在出方向
----------
```




## 华为交换机配置debug日志

**配置console日志等级为debugging**
```
[router]info-center source default channel 0 log level debugging state on
<router>display channel console 
channel number: 0, channel name: console
MODU_ID  NAME     ENABLE LOG_LEVEL     ENABLE TRAP_LEVEL    ENABLE DEBUG_LEVEL  
ffff0000 default  Y      debugging     Y      debugging     Y      debugging    
```
**配置远程会话日志等级为debugging**
```
[router]info-center source default channel 1 log level debugging state on
[router]display channel monitor 
channel number: 1, channel name: monitor
MODU_ID  NAME     ENABLE LOG_LEVEL     ENABLE TRAP_LEVEL    ENABLE DEBUG_LEVEL  
ffff0000 default  Y      debugging       Y      debugging     Y      debugging 
```
**开启debug**
```
<router>debugging ip icmp
<router>terminal monitor		
<router>terminal debugging 
<router>undo debugging all
```



## 华为交换机配置mstp配置

```
# [dsw3] 
stp region-configuration
 instance 0 vlan 10 40		# 默认所有vlan都是instance 0，MSTP较RSTP就是多了多实例
 instance 1 vlan 20
 active region-configuration
stp instance 0 priority 0 
stp instance 1 priority 4096
stp mode mstp 

# [dsw4]
stp region-configuration
 instance 0 vlan 10 40
 instance 1 vlan 20
 active region-configuration
stp instance 0 priority 4096 
stp instance 1 priority 0
stp mode mstp 

# 接入层交换机配置成mstp后会重新计算拓扑，丢15个包左右
# [ASW]
stp region-configuration
 instance 0 vlan 10 40
 instance 1 vlan 20
 active region-configuration
stp instance 0 priority 61440 
stp instance 1 priority 61440
stp mode mstp 



# 生成树拓扑结构
<dsw3>display stp brief 
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/7        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/8        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/9        DESI  FORWARDING      NONE
   0    Eth-Trunk3                  DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/7        DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/8        DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/9        DESI  FORWARDING      NONE
   1    Eth-Trunk3                  DESI  FORWARDING      NONE
<dsw4>display stp brief 
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/7        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/8        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/9        DESI  FORWARDING      NONE
   0    Eth-Trunk3                  ROOT  FORWARDING      NONE
   1    GigabitEthernet0/0/7        DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/8        DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/9        DESI  FORWARDING      NONE
   1    Eth-Trunk3                  MAST  FORWARDING      NONE
[asw1]display stp brief
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/7        ROOT  FORWARDING      NONE
   0    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
   1    GigabitEthernet0/0/7        MAST  FORWARDING      NONE
   1    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
<asw2>display stp brief
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/7        ROOT  FORWARDING      NONE
   0    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
   1    GigabitEthernet0/0/1        DESI  FORWARDING      NONE
   1    GigabitEthernet0/0/7        MAST  FORWARDING      NONE
   1    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
<asw3>display stp brief 
 MSTID  Port                        Role  STP State     Protection
   0    GigabitEthernet0/0/1        DESI  FORWARDING      NONE
   0    GigabitEthernet0/0/7        ROOT  FORWARDING      NONE
   0    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
   1    GigabitEthernet0/0/7        MAST  FORWARDING      NONE
   1    GigabitEthernet0/0/8        ALTE  DISCARDING      NONE
```



## 华为交换机配置nqa实现浮动路由

```
# 在ensp上测试能实现路由冗余，但在现网环境中不能实现路由冗余 
ip route-static 172.168.2.0 255.255.255.0 192.168.105.9
ip route-static 172.168.2.0 255.255.255.0 192.168.104.8 preference 70
ip route-static 192.168.10.0 255.255.255.0 192.168.104.8
ip route-static 192.168.10.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 192.168.21.0 255.255.255.0 192.168.104.8
ip route-static 192.168.21.0 255.255.255.0 192.168.105.9 preference 70


# 更换为nqa方式，在ensp上测试能实现路由冗余，但在现网环境中不能实现路由冗余，因为参数不适应现网环境
[csw-nqa-root-icmp]display this 
nqa test-instance root icmp
 test-type icmp
 destination-address ipv4 192.168.104.8
 frequency 4
 interval seconds 2
 timeout 1
 probe-count 2
 start now
[csw]display current-configuration | include ip rout
ip route-static 0.0.0.0 0.0.0.0 192.168.101.1
ip route-static 172.168.2.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 172.168.2.0 255.255.255.0 192.168.104.8 track nqa root icmp
ip route-static 192.168.10.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 192.168.10.0 255.255.255.0 192.168.104.8 track nqa root icmp
ip route-static 192.168.21.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 192.168.21.0 255.255.255.0 192.168.104.8 track nqa root icmp


[dsw3-nqa-root-icmp]display this
nqa test-instance root icmp
 test-type icmp
 destination-address ipv4 192.168.104.2
 frequency 4
 interval seconds 2
 timeout 1
 probe-count 2
 start now
[dsw3]display current-configuration | include ip rout
ip route-static 0.0.0.0 0.0.0.0 192.168.104.2 track nqa root icmp
ip route-static 0.0.0.0 0.0.0.0 192.168.10.253 preference 70

[dsw4-nqa-root-icmp]display this 
nqa test-instance root icmp
 test-type icmp
 destination-address ipv4 192.168.105.2
 frequency 4
 interval seconds 2
 timeout 1
 probe-count 2
 start now
[dsw4]display current-configuration | include ip rout
ip route-static 0.0.0.0 0.0.0.0 192.168.105.2 track nqa root icmp
ip route-static 0.0.0.0 0.0.0.0 192.168.10.252 preference 70
```


**生产建议配置**
> 这里的frequency跟ensp配置不一样，因为在真实网络中，当你下面的frequency配置为4时，命令行会警告你大于8，如果你未按从警告的配置，将导致nqa与其它方式联用失效，例如与静态路由track联用将会失效。
```
# 公式：frequency > interval x （probe-count - 1） + timeout
# frequency：defautl 0次
# timeout: default 3s 
# interval seconds：default 4s 
# probe-count: default probe 3次

nqa test-instance root icmp
 test-type icmp
 destination-address ipv4 192.168.104.8
 probe-count 2
 interval seconds 2
 timeout 1
 frequency 9
 start now

ip route-static 172.168.2.0 255.255.255.0 192.168.104.8 preference 70
ip route-static 172.168.2.0 255.255.255.0 192.168.105.9 track nqa root icmp
ip route-static 192.168.10.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 192.168.10.0 255.255.255.0 192.168.104.8 track nqa root icmp
ip route-static 192.168.21.0 255.255.255.0 192.168.105.9 preference 70
ip route-static 192.168.21.0 255.255.255.0 192.168.104.8 track nqa root icmp
```



## 收集交换机日志到syslog

```bash
## 华为日志配置
# 用户模式下手动配置时间
clock timezone SH add 08:00:00
clock datetime 13:57:00 2024-08-15

# 配置模式下自动同步时间配置
clock timezone SH add 08:00:00
ntp-service unicast-server 203.107.6.88	# 执行2次
ntp-service unicast-server 203.107.6.88

# 查看时间
[ASW1]display clock
2024-08-15 15:55:21+08:00
Thursday
Time Zone(SH) : UTC+08:00

# 配置日志
info-center source default channel 2 log level debugging
info-center loghost source Vlanif60
info-center loghost 192.168.13.198 facility local7


# 查看日志配置
[ASW1]display current-configuration | include info-
info-center source default channel 2 log level debugging
info-center loghost source Vlanif60
info-center loghost 192.168.13.198 facility local7




## 华三日志配置
# 配置模式下自动同步时间配置
---- H3C S5048 配置ntp
clock timezone SH add 08:00:00
ntp-service enable  
ntp-service unicast-server 203.107.6.88  
clock protocol ntp
---- H3C S5120-52P-LI 配置ntp
ntp-service unicast-server 203.107.6.88 


# 查看时间
[UA-ASW07]display clock
16:15:28.618 SH Thu 08/15/2024
Time Zone : SH add 08:00:00

# 配置日志
---- H3C S5048 配置
info-center source default loghost level debugging
info-center loghost source Vlan-interface 20
info-center loghost 192.168.13.198 facility local7
-----H3C S5120-52P-LI 配置
info-center source default channel 2 log level debugging
info-center loghost source Vlan-interface 20
info-center loghost 192.168.13.198 facility local7

# 查看日志配置
[UA-ASW07]display current-configuration | include info-
 info-center loghost source Vlan-interface20
 info-center loghost 192.168.13.198
 info-center source default loghost level debugging
 
 
 
## 思科交换机
# 配置用户名密码
aaa new-model
 username admin privilege 15 password YOU_PASSWORD
 service password-encryption

# 在vty模式下配置备用密码，如果不配置上面username admin，则思科交换机不会记录用户名和密码
line vty 0 4
 password YOU_PASSWORD
 
# 开启登录的事件
login on-success log
login on-failure log

# 配置命令审计，将会记录全局模式下修改的命令
archive
 log config
  logging enable
  logging size 1000
  notify syslog contenttype plaintext
  
# 收集日志到syslog
logging on
logging 172.168.2.199
logging console debugging
logging monitor debugging
logging buffered debugging
logging trap debugging
logging facility syslog
logging source-interface Vlan102
service timestamps log datetime msec
```





## 华为和华三交换机配置snmp

```bash
# 华为
snmp-agent
snmp-agent sys-info version v2c
snmp-agent protocol source-interface vlanif 10
snmp-agent community read G4axLE69
snmp-agent trap enable
snmp-agent target-host trap address udp-domain 192.168.13.236 params securityname G4axLE69 v2c

# 华三
snmp-agent
snmp-agent sys-info version v2c
snmp-agent community read G4axLE69
snmp-agent trap enable
snmp-agent target-host trap address udp-domain 192.168.13.236 params securityname G4axLE69 v2c

# 思科
snmp-server community G4axLE69 RW
snmp-server enable traps
```







## 问题汇总：

### 1. 服务器无法连接核心交换机，分析如下：
```
源目的地址：192.168.13.236（Prometheus） -> 192.168.102.2（CSW）
通信路径：192.168.13.236（Prometheus） -> 192.168.13.254（192.168.102.3）-> 泛洪192.168.102.2 -> 经过192.168.102.7（MSW），然后到达192.168.102.2（CSW）-> 回包192.168.13.236（Prometheus）-> 192.168.102.2（CSW）查找本地路由表：192.168.13.0 255.255.255.0 192.168.103.1，最终走192.168.103.1到达华为防火墙 -> 华为防火墙因为有状态检查，会对ICMP和TCP的首包进行状态检测，如果不是进出同一个包，则会被丢弃，配置路径：系统-配置-高级配置-配置状态检查
关闭会话检测命令：
undo firewall session link-state check icmp
undo firewall session link-state check tcp

临时解决办法：
在核心交换机上配置明细路由，使192.168.102.2（CSW）查找本地路由表时走192.168.102.7，路由表：192.168.13.236 255.255.255.0 192.168.102.7
后续步骤：更换MSW交换机为千兆交换机，因为百兆交换机流量无法满足192.168.13.236（Prometheus）的负载
```