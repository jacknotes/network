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




