<pre>
--新建“响应时间报告器”
ip sla monitor 1
 type echo protocol ipIcmpEcho 192.168.2.2 source-interface FastEthernet1/0
 timeout 1000
 threshold 3
 frequency 5
ip sla monitor schedule 1 life forever start-time now
--使用“响应时间报告器”进行跟踪  
track 1 rtr 1 reachability 
注：设置跟踪器对象为1，响应时间报告器对象为1，进行测试是否可达
--进行策略路由设置，以序列为顺序执行：
route-map ipTOtelecom permit 10
 match ip address ipTOtelecom
 set ip next-hop verify-availability 192.168.2.2 1 track 1
 set ip next-hop 192.168.1.1
--需要先开启routing模式，配置完并reload重启
show sdm prefer                 
  The current template is "desktop routing" template. 
sdm prefer routing
reload
--进行接口配置 
interface vlan 10
  ip policy route-map ipTOtelecom

默认会添加一条拒绝所有未匹配的条目
route-map ipTOtelecom deny 20 
注：当其它没有匹配到地址列表ipTOtelecom时将走默认路由。就像全局视图下没有配置策略路由时走静态路由表。此结论经过论证
注：当有多个set动作时，先设置的优先级最高，将会一直使用优先级最高的。当优先级最高的链路故障时，如果没有设置链路状态检测，则永远会走故障链路。
当配置了链路健康状态检测时，set动作将会比没有设置链路健康状态检测的动作高。
注：当设置：set ip next-hop 192.168.2.2 192.168.1.1时，则永远走192.168.2.2这个下一跳，当192.168.2.2down掉时，仍然走192.168.2.2，所以此配置是错误的，
应该配置链路状态健康检测。


--Cisco 3750
SWI_CORE01(config)#ip sla 10 
SWI_CORE01(config-ip-sla)#icmp-echo 192.168.10.253 source-ip 192.168.10.252 
SWI_CORE01(config-ip-sla-echo)#timeout 1000
SWI_CORE01(config-ip-sla-echo)#threshold 3 
SWI_CORE01(config-ip-sla-echo)#frequency 5
SWI_CORE01(config)#ip sla schedule 10 life forever start-time now 
SWI_CORE01(config)#track 10 ip sla 10 reachability 
SWI_CORE01#show track brief       
10      ip sla    10                   reachability     Up  


--cisco 3740E #20210818
----通过VACL方式实现vlan之间隔离
vlan 70,vlan 10
1. 配置访问控制列表，设置允许的条目
SWI_CORE01(config)#ip access-list extended denyVLAN70
SWI_CORE01(config-ext-nacl)#10 permit ip 192.168.17.0 0.0.0.255 192.168.10.0 0.0.0.255
2. 配置vlan策略，最后必须设定一条允许所有规则
SWI_CORE01(config)#vlan access-map denyVLAN70 10
SWI_CORE01(config-access-map)#match ip address denyVLAN70
SWI_CORE01(config-access-map)#action drop
SWI_CORE01(config)#vlan access-map denyVLAN70 20
SWI_CORE01(config-access-map)#action forward
3. 设置vlan策略使用升序顺序执行
SWI_CORE01(config)#vlan internal allocation policy ascending
4. 应用规则denyVLAN70到vlan 70
SWI_CORE01(config)#vlan filter denyVLAN70 vlan-list 70

----通过通过VLAN之间ACL方式实现vlan之间隔离
Switch(config)# vlan 10 // 创建vlan 10
Switch(config-vlan)# vlan 20
Switch(config-vlan)# vlan 30
Switch(config-vlan)# int vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0 // 配置vlan10虚端口IP
Switch(config-if)# int vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# int vlan 30
Switch(config-if)# ip address 192.168.30.1 255.255.255.0
******** 配置ACL ********
Switch(config)# access-list 101 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
Switch(config)# access-list 102 permit ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
******** 应用ACL至VLAN端口 ********
Switch(config)# int vlan 10
Switch(config-if)# ip access-group 101 in
Switch(config)# int vlan 20
Switch(config-if)# ip access-group 102 in
******** 完毕 ********
注：在vlan间的acl中当源地址段为应用 vlan接口的ip段时，就是用in方向；当目的地址段为应用vlan接口的ip段时，就是用out方向
注：一般情况下，通过VLAN之间ACL实现访问控制比较方便，但是当VLAN的端口比较分散时，采用VACL相对而言就要简单很多


### 20230209
## 控制同一VLAN中IP地址互访
access-list 101 permit ip host 192.168.13.200 host 192.168.13.111
vlan access-map denyhost1 10	#配置PBR
 action drop
 match ip address 101
vlan access-map denyhost1 20
 action forward
!
vlan filter denyhost1 vlan-list 30		#应用PBR，此方法只控制同一个VLAN


## 应用访问控制列表在vlan中
Extended IP access list jackli
    10 deny tcp host 172.168.2.219 host 172.16.30.100 eq 22 (109 matches)
    50 deny icmp host 172.168.2.219 host 172.16.30.100 (251 matches)
    100 permit ip any any (381 matches)
SWI_CORE02(config)#interface vlan 90
ip access-group jackli out		#应用策略在出方向















</pre>


