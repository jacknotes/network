#VPN
<pre>
vpn两种模式：
1. 隧道模式
2. 传输模式

#传输模式：
在整个传输过程中，IP包头并没有被封装进去，只是对数据包信息进行封装，所以这种模式被黑客攻击后，虽说看不到数据包信息，但是还是可看到双方的地址信息。所以这种模式常用于同一局域网内，例如：网络管理员通过网管主机登录公司内网的服务器进行维护管理，就可以选用传输模式VPN对其管理流量进行加密。

#隧道模式:
隧道模式中，VPN设备将整个三层数据报文封装在VPN数据内，再为封装后的数据报文添加新的IP包头。由于新IP包头中封装的是VPN设备的ip地址信息，所以当攻击者截获数据后，不但无法了解实际载荷数据的内容，同时也无法知道实际通信双方的地址信息。
由于隧道模式的VPN在安全性和灵活性方面具有很大的优势，在企业环境中应用十分广泛，总公司和分公司跨广域网的通信、移动用户在公网访问公司内部资源等很多情况，都会应用隧道模式的VPN对数据传输进行加密。

#VPN的类型：
1. 站点到站点VPN
2. 远程访问VPN

#站点到站点VPN：
站点到站点VPN就是通过隧道模式在VPN网关之间保护两个或者更多的站点之间的流量，站点间的流量通常是指局域网之间（L2L）的通信流量。L2L的VPN多用于总公司与分公司、分公司之间在公网上传输重要业务数据。
对于两个局域网的终端用户来说，在VPN网关中间的网络是透明的，就好像通过一台路由器连接的两个局域网。总公司的终端设备通过VPN连接访问分公司的网络资源。数据包封装的地址都是公司内网地址（一般为私有地址），而VPN网关对数据包进行的再次封装过程，客户端是全然不知的。

#远程访问VPN：
远程访问VPN通常用于单用户设备与VPN网关之间通信连接，单用户设备一般为一台pc或小型办公网络等。VPN连接的一端为PC，可能会让很多人误解远程访问VPN使用传输模式，但因为该种VPN往往也是从公网传输关键数据，而且单一用户更容易成为黑客的攻击对象，所以远程访问VPN对于安全性要求较高，更适用于隧道模式。
要想实现隧道模式的通信，就需要给远程客户端分配两个IP地址：一个是它自己的NIC地址，另一个是内网地址。也就是说远程客户端在VPN建立过程中同时充当VPN网关（使用NIC地址）和终端用户（使用内网地址）。
当远端的移动用户与总公司的网络实现远程访问VPN连接后，就好像成为总公司局域网中的一个普通用户，不仅使用总公司网段内的地址访问公司资源，而且因为其使用隧道模式，真实的ip地址被隐藏起来，实际公网通信的一段链路对于远端移动用户而言就像是透明的。

#加密算法：
加密就是一种将数据转换成另外一种形式的过程，如果不了解用于加密的算法，解密几乎是不可能的。举例而言，A给B发信息内容为”I LOVE YOU”,由于担心C从中间截获，于是，A与B协商了一种简单的加密方法：把所有的字母按照26个字母顺序+3，”I LOVE YOU”就变成了”L ORYH BYX”，即使C截获了数据也无法得知信息的真正含义。这里“+3”就可以理解为一种简单的加密算法。
实际VPN设备所使用的算法是相当复杂的，一般会涉及一些较为复杂的数学算法，利用这些算法可以实现数据加密、数据完整性验证、身份验证等***的基本功能。一般来说，可以将这些加密算法分为两大类：对称加密算法和非对称加密算法。
#1、对称加密算法：
	对称加密算法使用同一秘钥对信息提供安全的保护。假设对称加密算法的秘钥为“K”，客户端传输的明文数据为“M”，VPN网关加密后的数据为“C”，而E和D为加密和解密函数，则数据加密过程如下：
	发送发和接收方共享秘钥“K”，也就是说加密和解密使用同一把钥匙。
	发送方的VPN网关通过加密函数E将明文数据M加密成为密文数据C。
	接收方的VPN网关通过解密函数D将数据还原为明文数据M。
目前常见的对称加密算法有DES、3DES、AES等。
1） DES算法
	DES加密算法曾经在VPN领域应用很广，属IBM的研发产品，其秘钥长度为64位，其中8位用于奇偶校验，所以实际有效长度为56位。虽然该算法目前还没找到更好的方法破解，但是通过暴力破解已经可以在较短时间内攻破DES算法，所以在实际工程实施过程中（如果没有设备硬件性能支持）已经不建议使用该种算法。
2）3DES算法
	NIST在1999年研发了3DES算法，理论上3DES算法是DES算法的增强版本，因为3DES使用了三个阶段的DES，即同时使用了三个不同的56位秘钥，所以相当于产生了一个168位的有效秘钥长度。这种级别的秘钥目前还没有计算机有能力在短时间内破解，而且其执行效率虽然在软件环境中比较慢，但在硬件环境中并不明显。
3）AES算法
3DES算法虽然目前为止是安全的，但随着计算机硬件的更新，总有一天要被攻破，NIST在2002年研发了AES算法欲取代DES和3DES算法。该算法比3DES算法更安全，它支持128、192和256位秘钥长度，有效的秘钥长度可达上千位。更重要的是，AES算法采用了更为高效的编写方法，对CPU占用率较少，所以诸如IPSEC VPN等时机工程的实施过程中趋向于使用AES来提供更好的加密功能。
#2、 非对称加密算法
1） 算法的原理
非对称几秒算法使用公钥和私钥两个不同的秘钥进行加密和解密。用一个秘钥加密的数据仅能被另一个秘钥解密，且不能从一个秘钥推出另一个秘钥。假设接收方的公钥和私钥分别为P和Q，客户端传输的明文数据为M，VPN网关加密后的数据为C，而D、E分别为加密和解密函数，数据加密过程如下：
通信双方交换公钥（主要是把接收方的公钥传输给发送方）。
发送方的VPN网关通过对端公钥将明文数据M加密成为密文数据C。
接收方的VPN网关通过自己的私钥解密自己公钥加密过的数据。整个过程私钥始终没有在网络中传输。
2） 算法的优、缺点
非对称加密算法的最大优势在于其安全性。目前为止，还没有任何一种方法可以在合理的时间范围内攻破该种算法。为了方便理解，在这里举例说明。
假如有两个数字分别为25169和29663，如果要求将两个数相乘，可以轻松地得出答案：746588047。但如果给出的是74588047，要求猜出该数字是由哪两个数相乘得出的，就需要花费很长时间才可能得出答案。如果数字很大，那么可能花费一生的时间也难以得出答案，这就是非对称加密算法的安全性所在。更重要的是，私钥永远不可能被任何其他设备得到。
非对称加密算法也不是完美无缺的，由于其计算过程复杂，它的计算效率要比对称加密算法低很多（大约慢1500倍）。
3） DH算法(密钥管理，DH组)
常用的非对称加密算法有：RSA（使用三位数学家名字的首字母来命名）、DSA（digital signature algorithm，数字签名算法）、DH（diffie-hellman，迪菲赫尔曼）。前两种常用于验证功能，而DH一般被用来实现ipsec中的internet秘钥交换（IKE）协议。
DH算法的原理与传统意义上的非对称加密算法有点区别：通信双方交换公钥后，会用自己的秘钥和对方的公钥通过DH算法计算出一个共享秘钥，然后使用这个共享秘钥加密传输数据。从算法原理看，可以说DH算法已经将对称加密算法和非对称加密算法综合在一起。
DH算法支持可变的秘钥长度，由于公钥和私钥的长度不同，因此通过DH算法计算出的共享秘钥的有效长度也就不同。这些都是通过DH算法的秘钥组定义的。Cisco的路由器只支持DH组1、2和5。其中DH组1的有效秘钥长度为768，DH组2的有效秘钥长度为1024，DH组5的有效秘钥长度为1536。秘钥的有效长度越长，安全性也就越强。同时CPU的资源占用率也就越高。因此，选择合适的DH组要从网络的安全需求和设备本身的性能两个方面考虑。

3、 密钥交换
在探讨加密算法的过程中，忽略了一个关键的问题：秘钥的交换。许多有经验的网络工程师使用对称加密算法加密数据时，都会担心秘钥如何实现安全共享。
一种解决方案就是带外共享，即通信双方通过一张磁盘、一张纸或打一个电话方式实现秘钥的共享。这种方案最大的缺陷是实施过程花费的时间较长。如果管理的VPN设备数量较多，或者公司对于秘钥的安全性要求较高，需要一小时更换一次秘钥，这种方案基本就不可能实施了。
另一种解决方案就是带内共享。即通过telnet、ssh等连接方式通过网络传输秘钥。这种方法可以提高共享秘钥的效率，但前提是必须保证传输秘钥的通道绝对安全，而传输秘钥本身就是为了建立一条安全的通道，这似乎陷入一种死循环中。
其实，解决这个问题并不困难，可以通过非对称加密算法（如前面提到的DH算法）加密对称加密算法的秘钥，在用对称加密算法加密实际要传输的数据。如果公司需要一小时更换一次秘钥，只需让非对称加密算法一小时重新计算一次即可，而且由于非对称加密算法的私钥不会在网络上传输，其安全性也可得到保障。

#三、 HMAC算法数据报文验证（完整校验）
数据报文验证包括两个方面：数据来源验证（身份验证）和报文完整性验证。
HMAC算法的原理如下：
1） 双方共享执行hash算法的秘钥key。
2） 路由器A的用户数据与共享秘钥key通过hash算法得到数字签名。
3） 路由器A将数字签名和用户数据一同传输给路由器B。
4） 路由器B执行相同的算法过程得到数字签名。
5） 路由器B比对数字签名是否一致
如果数据在传输过程中被篡改或损坏，接收方通过HASH算法计算出来的数字签名就会和发送方的数字签名不同，于是便可以得知数据的内容在传输过程中被篡改。同理，如果窃听者想冒充一个通信方，他虽然可以伪造通信方的身份信息，但绝对无法伪造用户数据和共享秘钥计算后的数字签名。
#2、 MD5和SHA
MD5和SHA是HMAC的两种常用算法，在包括VPN的很多领域得到广泛应用。
MD5（message-digest algorithm 5，信息-摘要算法）在RFC1321中有明确规定，它创建了一个128位的签名，是目前HMAC功能中应用最为广泛的一种算法。例如：以前我们讲过的RIPV2，OSPF等很多路由选择协议都使用该算法做验证。MD5执行速度较快，但其安全性相对SHA稍差一点。
SHA（secure hash algorithm，安全散列算法）是由NIST（美国国家标准与技术研究院（National Institute of Standards and Technology，NIST）直属美国商务部）开发的，且已成为美国国家标准，一般成为SHA-1，它可以产生160位的签名（20字节的长度）。
目前，已有人证明不同的输入数值通过MD5计算可以得到相同的数字签名，说明MD5的签名可能具有一定程度的虚假性。SHA也出现类似的问题，并且有人宣称数字签名理论上是可以伪造的。因为存在这种安全隐患，现在已经开发了SHA-256和SHA-512等（有时统称为SHA-2），它们具有更长的签名长度，对于目前的计算机水平来说，可以消除上述安全隐患。

#四、 IPSEC VPN
IPSEC技术实现VPN是目前较为广泛的一种应用，实际工作中可能会遇到多个厂家的设备，有时需要网络工程师分析分体、排除故障，因此，除了查阅各厂商的专业文档外，了解VPN建立的各个阶段也是尤为重要的。
#1、 IPSec连接
对等体之间建立IPSec VPN的连接需要三个步骤。
1） 流量触发IPSec
一般来说，IPSec建立过程是由对等体之间发送的流量触发的，一旦有VPN流量经过VPN网关，连接过程便开始建立了。当然，手工配置也可以实现这一过程。在配置设备实现此步骤前，网络工程师需要明确哪些流量需要被“保护”。
2） 建立管理连接
IPSec使用ISAKMP/IKE阶段1来构建一个安全的管理连结。这里需要注意的是，这个管理连接只是一个准备工作，它不被用来传输实际的数据。在配置设备实现此步骤前，网络工程师需要明确设备如何实现验证，使用何种加密及认证算法，使用哪种DH组等问题。
3） 建立数据连接
IPSec基于安全的管理连接协商建立安全的数据连接，而ISAKMP/IKE阶段2就是用来完成这个任务的，数据连接用于传输真正的用户数据。在配置设备实现此步骤前，网络工程师需要明确使用何种安全协议，针对具体的安全协议应使用加密或验证算法，以及数据的传输模式（隧道模式或传输模式）等问题。
经过IPSec建立的三部曲后，VPN流量便可以按照协商的结果被加密/解密了。但是VPN连接并不是一次性的，无论是管理连接还是数据连接都有一个生存周期与之关联，一旦到期连接便会被终止。如果需要继续传输VPN数据，连接需要重新被构建，这种设计主要是出于安全性的考虑的。
#2、 ISAKMP/IKE阶段1
ISAKMP描述了秘钥管理的架构，它定义了消息的格式和秘钥交换协议的机制，以及构建连接的协商过程。而IKE是一个混合型的协议，它定义了秘钥的产生、共享和管理。IKE使用UDP端口500，一般来说，ISAKMP和IKE关键字可互换使用。
ISAKMP/IKE阶段1的交换过程有两个模式：主模式和积极模式。积极模式比主模式快，主模式比积极模式安全。
无论VPN的类型是站点到站点还是远程访问，都需要完成三个任务。
1. 协商采用何种方式建立管理连接。
	1.加密算法	2.HMAC功能（完整校验算法）3.身份验证的类型 4.DH密钥组（密钥管理） 5.管理连接的生存周期（生命周期）
2. 通过DH算法算出共享秘钥信息。
3. 对等体彼此进行身份验证。
	1.使用密钥加密用户身份信息 2.使用密钥和用户信息通过完整校验算法算出数字签名 3.双方比对数字签名确认身份

在主模式中，这三个任务是通过六个数据报文完成的：前两个数据包用于协商对等体间的管理连接使用何种安全策略（交换ISAKMP/IKEc传输集）；中间的两个数据包通过DH算法产生并交换加密算法和HMAC功能所需的秘钥，最后两个数据包使用预共享秘钥等方式执行对等体间的身份验证。这里需要注意的是，前四个报文为明文传输，从第5个数据报文开始为密文传输，而前四个数据包通过各种算法最终产生的秘钥用于第5、第6个数据包及后续数据的加密。

#ISAKMP/IKE阶段1建立过程：
1） 交换ISAKMP/IKE传输集
ISAKMP/IKE传输集就是一组用来保护管理连接的安全措施，有些书籍称之为IKE策略或ISAKMP策略，它主要包括以下几个方面。
加密算法：DES,3DES或AES。
HMAC功能：MD5或SHA-1。
设备验证的类型：预共享秘钥（也可以使用RSA签名等方法）。
Diffie-Hellman秘钥组：cisco支持1、2,、5、7（cisco的路由器不支持秘钥组7）。
管理连接的生存周期。

设备可能会有不止一个传输集，如果设备发起来接，它会将传输集列表（包括所有传输集）发送到远端对等体设备进行依次对比，直到找到匹配的结果。如果对比所有传输集后没有发现匹配的传输集，管理连接将无法建立，ipsec连接失败。例如，两端设备都只有一个传输集，且一端配置DES加密算法，另一端配置了3DES加密算法，就会导致IPSEC建立失败。如果给其另一端多配置一个传输集使用DES算法，对等体会自动找到与之匹配的传输集，管理连接便会建立起来。

如果使用cisco产品实现VPN连接，在ISAKMP/IKE传输集中除了生存周期，其他项必须匹配才能建立连接。如果对等图之间的生存周期不同，对等体会使用双方生存周期数值较小的一个。这同样是ipsec的规则。但有些厂商没有遵循这一规则，因此如果遇到cisco与其他厂商设备构建ipsec连接时，应注意确保ISAKMP/IKE传输集中所有参数的匹配。

2） 通过DH算法实现秘钥交换
第一步只是协商管理连接的安全策略，而共享秘钥的产生与交换就要通过Diffie-Hellman来实现。
DH算法属于非对称加密算法，因此它将产生公钥和私钥对的组合，且彼此共享公钥。VPN对等体用对方的公钥和自己的私钥通过一种功能运算产生一个安全的共享秘钥，即使有人截获数据，也会因为没有私钥而无法对出共享秘钥。

3） 实现设备之间的身份验证
设备身份验证时最常使用的方法就是预共享秘钥，即在对等体之间通过带外的方式共享秘钥，并存储在设备的本地。设备验证的过程可以通过加密算法或HMAC功能两种方法实现，而加密算法很少用于身份验证，多数情况都会通过HMAC功能实现。

#2、 ISA/KMP/IKE阶段1相关配置命令
1） 配置安全策略
ISAKMP/IKE策略包含以下参数：策略的序列号、加密算法、验证方法、DH组、生存周期。配置命令如下。（用于加密和验证阶段1第5、6个数据包）
Crypto isakmp policy priority命令用于建立ISAKMP/IKE的管理连接策略。每个策略对应一个序列号（priority），范围为1-10000，序列号数值越低，优先级越高。该命令将使命令行进入isakmp子命令模式。
Encryption命令用于指定管理连接建立的最后两个数据报文（用于身份验证）采用何种加密算法。
Hash命令指定了验证过程采用HAMC的功能。
Authencication命令指定了设备身份验证的方式。Pre-share采用预共享秘钥的方式。
Group命令用于指定DH秘钥组，默认使用DH1。组号越大，算法越安全，占用设备的资源也就越多。
Lifetime命令指定了管理连接的生存周期，其默认值为86400s（24小时）。
使用show crypt isakmp policy命令查看上述配置。

2） 配置预共享秘钥
通过下述命令配置预共享秘钥。
office-router#crypto isakmp key wandian address 122.226.124.58
0表示秘钥为明文，6表示秘钥被加密。
Keystring表示秘钥的具体内容。
Peer-address表示对端与之共享秘钥的对等体设备地址。
Subnet_mask：这里为可选命令，如果没有指定，默认将使用255.255.255.255作为掩码。
共享秘钥配置完成之后，可以通过show crypto isakmp key命令查看，命令框中显示了秘钥处于明文和密文时，命令显示内容的差异。
为了增强安全性，在IOS12.3（2）T版本中，增加了一个选项来加密秘钥，但是要求设备的IOS镜像必须支持AES加密，具体命令如下。
office-router#key config-key password-encrypt
New key:
Confirm key:
office-router#password encryption aes
在new key后面输入的秘钥至少为8个字母。

#五、 ISAKMP/IKE阶段2
ISAKMP/IKE阶段2主要是在两个ipsec对等体间建立数据连接，其主要完成以下任务。
定义对等体间需要保护何种流量。
定义用来保护数据的安全协议。
定义传输模式。
定义数据连接的生存周期及秘钥刷新的方式。
其中，ipsec对等体一般是通过ACL来匹配那些需要加密传输的***流量。

#1、 ISAKMP/IKE阶段2的建立过程
1） 安全关联（sa）
Ipsec需要在两个对等体之间建立一条逻辑连接，这就要使用一个被称为安全关联的信令协议，这是因为ipsec需要无连接的IP协议在安全运行之前要成为面向连接的协议。SA的连接是在源点和终点之间的单向连接，如果需要双向连接，就需要两个SA连接，每个方向一个。SA连接由三个要素定义。
1.安全参数索引（SPI）：用于唯一表示每条SA连接。
2.安全协议的类型：IPSEC定义了两种安全协议，即AH和ESP。
3.目的IP地址。

ISAKMP/IKE阶段2具有这种特性，即ISAKMP/IKE的数据连接实际是通过两个单向连接建立的，而两个连接采用的加密或者认证方式都是相同的。这就使ISAKMP/IKE阶段2的这个特性不易被观察到。

数据连接的传输集顶一个数据连接时如何被保护的。与管理连接的传输集类似。对等体设备可以保存一个或多个传输集，但其具体内容却完全不同。

安全协议：AH协议，ESP协议。
连接模式：隧道模式，传输模式。
加密方式：对于ESP而言，有DES、3DES、AES、AES-192、AES-256或不使用加密算法。
验证方式:MD5或SHA-1。

3） ISAKMP/IKE阶段2的安全协议
IPSEC的数据连接可以通过安全协议实现对数据连接的保护：AH协议和ESP协议。可以通过其中的一个协议来实现数据的加密和验证，如使用ESP协议，也可以使用两个协议一起来实现。AH使用IP协议号51，ESP使用IP协议号50。
AH（认证头协议）在RFC 2402中有明确定义。
数据完整性服务。
数据验证。
保护数据回放攻击。
AH协议保护整个数据报文，但易变的字段除外，如IP包头中的TTL和TOS字段。
AH头和数据整个包结构：
下一个头：这个八位字段定义了IP数据报文携带的有效载荷类型（TCP,UDP,ICMP,OSPF等），它与封装前IP首部中的协议字段功能一样。
有效载荷长度：这个八位字段的功能与字面意思不同，它并不定义有效载荷的长度，实际只定义了AH头部的长度。
安全参数索引（SPI）：这个32字段是由接收端设备分配的一个数字，用来唯一表示一条单向连接，可以提供超过1亿种标识号码。
序列号：这个32位字段对于数据报文提供排序信息，用来防止重放攻击。即使数据报文重传，该序列号也不会重复，而且当需要到达232也不会回绕，而必须重新建立新的连接。
完整性校验和（ICV）：这个字段提供验证功能，它就是MD5或SHA等HMAC功能产生的数字签名。AH的ICV值是完整的IP数据报文产生的数字签名，也就是说，它对整个IP数据报文进行完整性校验。
从报文结果不难发现，AH协议只能实现验证功能，而并未提供任何形式的数据加密，而且正因为其对整个IP数据报文实现验证功能，所以他与NAT或PAT不能一起使用。

ESP在RFC2406中有明确定义，它与AH区别如下：
ESP对用户数据实现加密功能。
ESP只对IP数据的有效载荷进行验证，不包括外部的IP包头。

因此，如果有黑客对IP包头内容进行篡改，ESP是无法检测到的。这一点一点却使ESP可以和NAT一起共用。但是，无论是ESP还是AH都无法和PAT一起使用，这是由于PAT要修改第四层的数据包头。

ESP头-密文数据-ESP尾部数据包结构：
ESP头部：SPI字段、序列号与AH报文结构中对应的字段的功能类似。
ESP尾部：补丁用于减少有效载荷被窃听并被猜测的可能性：补丁长度定义了补丁字节数。下一个头与AH报文结构中对应字段的功能类似。ICV依然提供验证功能，但是ICV只是ip数据报文的有效载荷通过HMAC功能产生的数字签名。
另外，ESP协议加密功能的实现与采用何种连接模式相关。

#2、 ISAKMP/IKE阶段2的配置命令
ISAKMP/IKE阶段2的配置过程有三个部分组成。
 1） 配置Crypto ACL
定义何种流量需要被保护的一种方法就是建立一个crypto ACL，通过ACL匹配ipsec ***流量，其中，permit语句指定了需要被保护的流量，而deny语句定义了不需要保护的流量。通常情况，两端对等体设备上的crypto ACL互为镜像，否则阶段2的连接建立就会失败。配置命令如下：

2） 配置阶段2的传输集
在ipsec对等体之间可以配置多个数据连接的传输集，必须保证两端至少有一对匹配的传输集，这样ISAKMP/IKE阶段2的数据SA连接才能协商成功。设备的传输集由设备性能决定，如果所有对等体设备的性能相近，则共同使用一种传输集即可，如果设备间性能差异较为明显，通常就需要多个传输集了。配置名另如下。
office-router(config)#crypto ipsec transform-set VPN12 esp-3des esp-md5-hmac
mode tunnel
Transform_set_name：为传输集名称，该名称具有唯一性，不能与任何其他传输集相同。
Transform1：传输集选项，参考下表：
AH和ESP安全协议：
AH:ah-md5-hmac	ah-sha-hmac
ESP验证:esp-md5-hmac	esp-sha-hmac
ESP加密:
esp-null	esp-des	esp-3des
esp-aes 128	esp-aes 192	esp-aes 256
要想查看路由器上的传输集，使用show crypto ipsec transform-set命令。

如果在数据连接建立之后修改传输集的配置，并不会影响现有的SA设置，只有连接的生存周期到期，SA才会重新建立连接或者SA被手动清除（通过clear crypto sa或clear crypto ipsec sa命令）。

3） 配置crypto map
Crypto map的功能就是将所有的信息组织在一起构建ipsec会话。通常路由器的接口上只对应一个crypto map，一台路由器可以在多个接口上实现流量保护，这时可能就需要多个crypto map了。
Crypto map有两种类型：静态的crypto map和动态的crypto。在构建L2L会话时通常会使用静态的，具体配置命令如下。
crypto map vpn 10 ipsec-isakmp
 set peer 122.226.124.58
 set peer 114.80.89.34
 set security-association lifetime kilobytes 86400
 set security-association lifetime seconds 86400
 set transform-set VPN12
 set pfs group1
 match address 120
命令解释：
Map_name：crypto map的名称。
Seq_num：crypto map的序列号，其范围是1-65535，数值越小，优先级越高。
Match address：用于调用crypto ACL的名字或编号。
Set peer：用于指定ipsec的对等体设备，即配置的设备应该与谁建立连接。
Set transform-set：指定传输集的名称，这里最多可以列出六个传输集的名称。
Set PFS：（PFS:perfect forward secrecy，完美转发保密）保证两个阶段中的秘钥只能使用一次，进一步增强了安全性，但使用PFS可能占用设备更多的资源。Set pfs命令用于启用这项功能并指定使用哪个DH秘钥组，这是一条可选命令。
Set security-association lifetime：用于指定SA的生存周期。默认情况下，cisco的设备已经设定数据连接的生存周期为3600s或4608000KB，相当于一小时内以10Mb/s速率传输的流量。生存周期的阈值是由时间和流量两个方面的因素决定的。且相当于任何一项到达阈值的限制时，SA就会被重新协商建立。
如果在IPSEC对等体之间只设置lifetime，即使没有ipsec流量传递，SA也始终处于激活状态，如果在***设备上配置了很多ipsec连接，这些暂时没有实际意义的SA就会占用设备的内存和CPU资源。从IOS12.2（15）T开始，cisco为数据SA引入空闲超时计时器，如果没有流量通过SA传输，SA就会被设备自动删除。
Set security-association idle-time命令用于设定空闲超时计数器，范围为60-86400s。默认情况下，空闲超时计时器是关闭的。

查看命令：
查看IKE策略
R1#show crypto isakmp policy
查看管理连接SA的状态
R1#show crypto isakmp sa
查看IPSec传输集
R1#show crypto ipsec transform-set
查看数据连接SA的状态
R1#show crypto ipsec sa
查看Crypto Map
R1#show crypto map


#总结：
IKE第一阶段：
配置管理连接传输集
IKE第二阶段：
配置数据连接传输集

当添加vpn网段意味要修改VPN感性趣流（关于overload的访问控制列表），直接删除标准访问控制列表再重新添加访问控制列表并不会造成公司断网。


#补充：
1.数据要加密，所以就有了加密算法（1 类似加密函数），加密函数直接加密数据会不安全，因为别人用解密函数直接可以解密，所以就有了共享密钥，例如：m(y+x)=c【m是加密函数，y是预共享密钥，x是用户数据，c是加密后的数据】，c到对端后再用解密函数来解密，得出x,例如：n(y+c)=x【n是解密函数，y是预共享密钥，c是加密后的用户数据，x是解密后的数据】
2.由于数据可能被黑客或者外来人侵害（1.来源更改，2.数据更改），所以要用完整校验算法（2 也称hash值或散列值，有MD5和SHA两种算法）来算取是否未被黑客任何更改，完整校验算法使用例如：hash值（也称散列值），算出来的数字签名是无法被解密的，总的就是说通过完整校验算法加密后的数据是无法被还原的。工作过程：一端用完整校验算法的密钥key（完整校验算法本身的密钥key）和用户数据通过完整校验算法来算出一个数字签名，这一端把算好的数字签名和用户数据发送给另一端，另一端收到后也同样用完整校验算法的密钥key和用户数据通过完整校验算法来算出一个数字签名，然后这一端用自己算出来的数字签名和对端的数字签名进行比对，如果吻合就确定数据完整性安全，可以进行正常数据传输了。
3.VPN连接并不是一次性的，无论是管理连接传输集还是数据连接传输集都有一个生存周期与之关联，一旦到期连接便会被终止，所以有了生命周期（3），当达到这个生命周期阀值时就会断开连接，如果需要继续传输VPN数据，连接需要重新被构建，这种设计主要是出于安全性的考虑的。
4.密钥管理（4 DH组1，2，5，7[思科没有7]），工作过程：密钥管理双方交换公钥获得对方的公钥，然后用公钥和自己私钥通过DH组加密算法算出一个新的共享密钥，对端要解密就必须先求出共享密钥后才能用解密算法解密数据，求出共享密钥使用DH组解密算法代入对方公钥和自己私钥，使得自己私钥跟自己公钥，对方私钥和公钥配对，成功后方可解密出共享密钥。如果公司需要一小时更换一次秘钥，只需让非对称加密算法（DH组）一小时重新计算一次即可，而且由于非对称加密算法的私钥不会在网络上传输，其安全性也可得到保障。
5.设备身份验证时最常使用的方法就是预共享秘钥（5），即在对等体之间通过带外的方式共享秘钥，并存储在设备的本地。设备验证的过程可以通过加密算法或HMAC功能两种方法实现，而加密算法很少用于身份验证，多数情况都会通过HMAC功能实现。


1.建立管理连接传输集

IPSec使用ISAKMP/IKE阶段1来构建一个安全的管理连结。这里需要注意的是，这个管理连接只是一个准备工作，它不被用来传输实际的数据。在配置设备实现此步骤前，网络工程师需要明确设备如何实现验证，使用何种加密及认证算法，使用哪种DH组等问题

2.建立数据连接传输集

IPSec基于安全的管理连接协商建立安全的数据连接，而ISAKMP/IKE阶段2就是用来完成这个任务的，数据连接用于传输真正的用户数据。在配置设备实现此步骤前，网络工程师需要明确使用何种安全协议，针对具体的安全协议应使用加密或验证算法，以及数据的传输模式（隧道模式或传输模式）等问题。

3.流量触发IPSec

一般来说，IPSec建立过程是由对等体之间发送的流量触发的，一旦有VPN流量经过VPN网关，连接过程便开始建立了。当然，手工配置也可以实现这一过程。在配置设备实现此步骤前，网络工程师需要明确哪些流量需要被“保护”。

IKE使用UDP端口500，一般来说，ISAKMP和IKE关键字可互换使用。
ISAKMP/IKE阶段1的交换过程有两个模式：主模式和积极模式。积极模式比主模式快，主模式比积极模式安全。

Crypto map的功能就是将所有的信息组织在一起构建ipsec会话。通常路由器的接口上只对应一个crypto map，一台路由器可以在多个接口上实现流量保护，这时可能就需要多个crypto map了。
Crypto map有两种类型：静态的crypto map和动态的crypto。在构建L2L会话时通常会使用静态的，具体配置命令如下。
crypto map的序列号，其范围是1-65535，数值越小，优先级越高。
Set transform-set：指定传输集的名称，这里最多可以列出六个传输集的名称。
Set PFS：（PFS:perfect forward secrecy，完美转发保密）保证两个阶段中的秘钥只能使用一次，进一步增强了安全性，但使用PFS可能占用设备更多的资源。Set pfs命令用于启用这项功能并指定使用哪个DH秘钥组，这是一条可选命令。
Set security-association lifetime：用于指定SA的生存周期。默认情况下，cisco的设备已经设定数据连接的生存周期为3600s或4608000KB，相当于一小时内以10Mb/s速率传输的流量。生存周期的阈值是由时间和流量两个方面的因素决定的。且相当于任何一项到达阈值的限制时，SA就会被重新协商建立。
如果在IPSEC对等体之间只设置lifetime，即使没有ipsec流量传递，SA也始终处于激活状态，如果在***设备上配置了很多ipsec连接，这些暂时没有实际意义的SA就会占用设备的内存和CPU资源。从IOS12.2（15）T开始，cisco为数据SA引入空闲超时计时器，如果没有流量通过SA传输，SA就会被设备自动删除。
Set security-association idle-time命令用于设定空闲超时计数器，范围为60-86400s。默认情况下，空闲超时计时器是关闭的。

从报文结果不难发现，AH协议只能实现验证功能，而并未提供任何形式的数据加密，而且正因为其对整个IP数据报文实现验证功能，所以他与NAT或PAT不能一起使用。
有黑客对IP包头内容进行篡改，ESP是无法检测到的。这一点一点却使ESP可以和NAT一起共用。但是，无论是ESP还是AH都无法和PAT一起使用，这是由于PAT要修改第四层的数据包头。

双网关负载均衡高可用
gw1#show running-config 
*Mar  1 00:02:20.607: %SYS-5-CONFIG_I: Configured from console by console
gw1#show running-config 
Building configuration...

Current configuration : 2340 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname gw1
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
!
!
ip cef
no ip domain lookup
!
!
ip sla monitor 1
 type echo protocol ipIcmpEcho 1.1.1.1 source-ipaddr 1.1.1.2
 frequency 5
ip sla monitor schedule 1 life forever start-time now
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
ip tcp synwait-time 5
!
track 1 rtr 1 reachability
!         
!
!
!
!
interface FastEthernet0/0
 ip address 1.1.1.2 255.255.255.0
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 192.168.2.1 255.255.255.0 secondary
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1.2
 shutdown
!
interface FastEthernet1/0
 no switchport
 ip address 10.10.10.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet1/1
 shutdown
!
interface FastEthernet1/2
 shutdown
!
interface FastEthernet1/3
 shutdown
!
interface FastEthernet1/4
 shutdown
!
interface FastEthernet1/5
 shutdown
!
interface FastEthernet1/6
 shutdown
!         
interface FastEthernet1/7
 shutdown
!
interface FastEthernet1/8
 shutdown
!
interface FastEthernet1/9
 shutdown
!
interface FastEthernet1/10
 shutdown
!
interface FastEthernet1/11
 shutdown
!
interface FastEthernet1/12
 shutdown
!
interface FastEthernet1/13
 shutdown
!
interface FastEthernet1/14
 shutdown 
!
interface FastEthernet1/15
 shutdown
!
interface Vlan1
 no ip address
!
!
router ospf 10
 log-adjacency-changes
 network 10.10.10.0 0.0.0.255 area 10
 network 192.168.1.0 0.0.0.255 area 10
 network 192.168.2.0 0.0.0.255 area 10
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 1.1.1.1 track 1
ip route 0.0.0.0 0.0.0.0 10.10.10.2 10
!
!
ip nat inside source list inside1 interface FastEthernet0/0 overload
!
!         
ip access-list extended inside1
 permit ip 192.168.1.0 0.0.0.255 any
 permit ip 192.168.2.0 0.0.0.255 any
 permit ip 192.168.10.0 0.0.0.255 any
 permit ip 192.168.3.0 0.0.0.255 any
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end

gw2#show running-config 
Building configuration...

Current configuration : 2057 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname gw2
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
!
!
ip cef
no ip domain lookup
!
!
ip sla monitor 2
 type echo protocol ipIcmpEcho 2.1.1.1 source-ipaddr 2.1.1.2
 frequency 5
ip sla monitor schedule 2 life forever start-time now
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
ip tcp synwait-time 5
!
track 2 rtr 2 reachability
!         
!
!
!
!
interface FastEthernet0/0
 ip address 2.1.1.2 255.255.255.0
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 192.168.3.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no switchport
 ip address 10.10.10.2 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet1/1
!
interface FastEthernet1/2
!
interface FastEthernet1/3
!
interface FastEthernet1/4
!
interface FastEthernet1/5
!
interface FastEthernet1/6
!
interface FastEthernet1/7
!
interface FastEthernet1/8
!
interface FastEthernet1/9
!
interface FastEthernet1/10
!
interface FastEthernet1/11
!         
interface FastEthernet1/12
!
interface FastEthernet1/13
!
interface FastEthernet1/14
!
interface FastEthernet1/15
!
interface Vlan1
 no ip address
!
!
router ospf 10
 log-adjacency-changes
 network 10.10.10.0 0.0.0.255 area 10
 network 192.168.3.0 0.0.0.255 area 10
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 2.1.1.1 track 2
ip route 0.0.0.0 0.0.0.0 10.10.10.1 10
!
!         
ip nat inside source list isp2 interface FastEthernet0/0 overload
!
!
ip access-list extended isp2
 permit ip 192.168.3.0 0.0.0.255 any
 permit ip 192.168.10.0 0.0.0.255 any
 permit ip 192.168.1.0 0.0.0.255 any
 permit ip 192.168.2.0 0.0.0.255 any
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end
单网关负载均衡高可用
gw#show running-config 
Building configuration...

Current configuration : 3312 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname gw
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
!
!
ip cef
no ip domain lookup
!
!
ip sla monitor 1
 type echo protocol ipIcmpEcho 1.1.1.1 source-interface FastEthernet0/0
 timeout 1000
 threshold 3
 frequency 5
ip sla monitor schedule 1 life forever start-time now
ip sla monitor 2
 type echo protocol ipIcmpEcho 2.1.1.1 source-interface FastEthernet0/1
 timeout 1000
 threshold 3
 frequency 5
ip sla monitor schedule 2 life forever start-time now
!
!
!
!
!
!
!
!
!
!
!
!         
!
!
!
!
ip tcp synwait-time 5
!
track 1 rtr 1 reachability
!
track 2 rtr 2 reachability
! 
!
!
!
!
interface FastEthernet0/0
 ip address 1.1.1.2 255.255.255.0
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 2.1.1.2 255.255.255.0
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no switchport
 ip address 192.168.2.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 ip policy route-map load
!
interface FastEthernet1/1
 no switchport
 ip address 192.168.0.1 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 ip policy route-map load
!
interface FastEthernet1/2
!
interface FastEthernet1/3
!         
interface FastEthernet1/4
!
interface FastEthernet1/5
!
interface FastEthernet1/6
!
interface FastEthernet1/7
!
interface FastEthernet1/8
!
interface FastEthernet1/9
!
interface FastEthernet1/10
!
interface FastEthernet1/11
!
interface FastEthernet1/12
!
interface FastEthernet1/13
!
interface FastEthernet1/14
!
interface FastEthernet1/15
!
interface Vlan1
 no ip address
!
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 1.1.1.1
ip route 0.0.0.0 0.0.0.0 2.1.1.1
!
!
ip nat inside source route-map nat1 interface FastEthernet0/0 overload
ip nat inside source route-map nat11 interface FastEthernet0/1 overload
ip nat inside source route-map nat2 interface FastEthernet0/1 overload
ip nat inside source route-map nat22 interface FastEthernet0/0 overload
!
!
ip access-list extended isp1
 permit ip any 1.1.1.0 0.0.0.255
 permit ip any 114.114.114.0 0.0.0.255
 permit ip any 202.96.209.0 0.0.0.255
 permit ip any 6.6.6.0 0.0.0.255
ip access-list extended isp2
 permit ip any 8.8.8.0 0.0.0.255
 permit ip any 4.4.4.0 0.0.0.255
 permit ip any 2.1.1.0 0.0.0.255
!
route-map load permit 10
 match ip address isp1
 set ip next-hop verify-availability 1.1.1.1 1 track 1
 set ip next-hop verify-availability 2.1.1.1 2 track 2
!
route-map load permit 20
 match ip address isp2
 set ip next-hop verify-availability 2.1.1.1 1 track 2
 set ip next-hop verify-availability 1.1.1.1 2 track 1
!
route-map nat2 permit 10
 match ip address isp2
 match interface FastEthernet0/1
!
route-map nat1 permit 10
 match ip address isp1
 match interface FastEthernet0/0
!
route-map nat11 permit 10
 match ip address isp1
 match interface FastEthernet0/1
!
route-map nat22 permit 10
 match ip address isp2
 match interface FastEthernet0/0
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end

###Real VPN

!
! Last configuration change at 07:13:33 UTC Tue Sep 5 2017
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname office-router
!
boot-start-marker
boot-end-marker
!
!
enable password cisco!@#a123
!
no aaa new-model
!
ip cef
!
!
!
!
!
!
no ipv6 cef
!
multilink bundle-name authenticated
!
!
!
!
license udi pid CISCO2921/K9 sn FGL183911HQ
!
!
!
redundancy
!
!
!
!
!
! 
!
crypto isakmp policy 10		//第一阶段IKE
 encr 3des	//加密算法
 hash md5	//完整校验算法
 authentication pre-share    //身份认证方式（预共享密钥，证书认证）
 group 2     //密钥管理
 lifetime 28800   //生命周期
crypto isakmp key wandian address 114.80.89.34    //设置对端共享密钥
crypto isakmp key wandian address 122.226.124.58  //设置对端共享密钥
!
!
crypto ipsec transform-set VPN12 esp-3des esp-md5-hmac  //第二阶段ipsec，设置转换集为VPN12,加密算法，完整校验算法
 mode tunnel 	//设置传输方式（这里为私网访问方式为隧道模式，还有一种公网访问方式为传输模式）
!
!
!
crypto map vpn local-address GigabitEthernet0/1 //设置加密图，名字为vpn,本端vpn ip为GE0/1口
crypto map vpn 10 ipsec-isakmp 	//加密图vpn调用第一阶段和第二阶段
 set peer 114.80.89.34	//设置对端公网IP
 set peer 122.226.124.58	//设置对端公网IP
 set security-association lifetime kilobytes 86400	//设置安全生命周期传输有效数据包
 set security-association lifetime seconds 86400	//设置安全生命周期有效时间
 set transform-set VPN12 	//设置转换集为之前设置好的VPN12（调用转换集）
 set pfs group1		//设置密钥管理版本
 match address 120	//匹配访问控制列表120
!
!
!
!
!
interface Embedded-Service-Engine0/0
 no ip address
 shutdown
!
interface GigabitEthernet0/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 180.168.251.178 255.255.255.248
 no ip redirects
 no ip proxy-arp
 ip nat outside
 ip virtual-reassembly in
 duplex auto
 speed auto
 crypto map vpn 	//调用并启用vpn
!
interface GigabitEthernet0/2
 ip address 192.168.1.1 255.255.255.0
 no ip redirects
 no ip proxy-arp
 ip nat inside
 ip virtual-reassembly in
 duplex auto
 speed auto
!
ip forward-protocol nd
!
ip http server
no ip http secure-server
!
ip nat pool hasco 180.168.251.178 180.168.251.182 netmask 255.255.255.248
ip nat inside source list 101 interface GigabitEthernet0/1 overload
ip nat inside source static tcp 192.168.1.221 21 180.168.251.178 21 extendable
ip nat inside source static tcp 192.168.1.155 22 180.168.251.178 22 extendable
ip nat inside source static tcp 192.168.1.155 80 180.168.251.178 80 extendable
ip nat inside source static tcp 192.168.1.221 8080 180.168.251.178 8080 extendable
ip nat inside source static tcp 192.168.1.222 3389 180.168.251.178 9753 extendable
ip nat inside source static tcp 192.168.1.221 3389 180.168.251.178 9876 extendable
ip nat inside source static tcp 192.168.1.223 3389 180.168.251.179 9876 extendable
ip route 0.0.0.0 0.0.0.0 180.168.251.177
ip route 192.168.1.0 255.255.255.0 192.168.1.254
ip route 192.168.2.0 255.255.255.0 192.168.1.254
!
access-list 101 deny   ip 192.168.1.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 101 deny   ip 192.168.2.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 101 deny   ip 192.168.1.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 101 deny   ip 192.168.2.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 101 deny   ip 192.168.1.0 0.0.0.255 192.168.80.0 0.0.0.255
access-list 101 deny   ip 192.168.2.0 0.0.0.255 192.168.80.0 0.0.0.255
access-list 101 permit ip 192.168.1.0 0.0.0.255 any
access-list 101 permit ip 192.168.2.0 0.0.0.255 any
access-list 120 permit ip 192.168.1.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 120 permit ip 192.168.2.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 120 permit ip 192.168.1.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 120 permit ip 192.168.2.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 120 permit ip 192.168.1.0 0.0.0.255 192.168.80.0 0.0.0.255
access-list 120 permit ip 192.168.2.0 0.0.0.255 192.168.80.0 0.0.0.255
!
!
!
control-plane
!
!
!
line con 0
line aux 0
line 2
 no activation-character
 no exec
 transport preferred none
 transport output pad telnet rlogin lapb-ta mop udptn v120 ssh
 stopbits 1
line vty 0 4
 password cisco!@#
 login
 transport input all
!
scheduler allocate 20000 1000
!
end

##Real Switch

!
version 12.2
no service pad
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname Switch
!
boot-start-marker
boot-end-marker
!
enable password cisco
!
!
!
no aaa new-model
switch 1 provision ws-c3750x-24
system mtu routing 1500
ip routing
!
ip dhcp pool vlan1
   network 192.168.1.0 255.255.255.0
   default-router 192.168.1.254 
   dns-server 180.168.255.118 202.96.209.5 
!
ip dhcp pool vlan2
   network 192.168.2.0 255.255.255.0
   default-router 192.168.2.254 
   dns-server 180.168.255.118 202.96.209.5 
!
!
!
!
crypto pki trustpoint TP-self-signed-3869983360
 enrollment selfsigned
 subject-name cn=IOS-Self-Signed-Certificate-3869983360
 revocation-check none
 rsakeypair TP-self-signed-3869983360
!
!
crypto pki certificate chain TP-self-signed-3869983360
 certificate self-signed 01
  3082023F 308201A8 A0030201 02020101 300D0609 2A864886 F70D0101 04050030 
  31312F30 2D060355 04031326 494F532D 53656C66 2D536967 6E65642D 43657274 
  69666963 6174652D 33383639 39383333 3630301E 170D3933 30333031 30303031 
  32325A17 0D323030 31303130 30303030 305A3031 312F302D 06035504 03132649 
  4F532D53 656C662D 5369676E 65642D43 65727469 66696361 74652D33 38363939 
  38333336 3030819F 300D0609 2A864886 F70D0101 01050003 818D0030 81890281 
  8100E81C FE06F8AA 649D7262 2544E19F E78E6660 FD755C26 2230AB54 ADCB5022 
  75FFA819 DD24FCDC E9912485 810DBD72 2C95569A 4529E48B 4B8B7D95 2A9A5CA1 
  422E3616 25863340 9C2C7BF3 DF247636 FEC22115 6CE2AF7F E7E0042D 1D05EEE9 
  3B36EFDD F99D7717 EE042DF2 67B46461 98C7F15C 0E8A46F8 BA7C2191 A5ABDD65 
  0C010203 010001A3 67306530 0F060355 1D130101 FF040530 030101FF 30120603 
  551D1104 0B300982 07537769 7463682E 301F0603 551D2304 18301680 14209649 
  A761328C A5E73509 427853D1 63431560 9A301D06 03551D0E 04160414 209649A7 
  61328CA5 E7350942 7853D163 4315609A 300D0609 2A864886 F70D0101 04050003 
  8181002B 7467E929 049BA69D 883069B0 EA48F383 87AE7612 BBAF1990 C8E2E2A7 
  8E5489D7 472AF53A 22874A99 38C9CC04 3A47FC91 A3FAA843 181BC8BF 75FE2EDA 
  06435F88 A22E9967 42397AF2 CA8DA948 4F3C9A05 14115918 4BA365D8 A705C028 
  F4CC6487 D8D86B74 C1A5523B 1A0C4FB9 EBC6C469 517371CD B1AF7A6D 5D4E947F D6E6F1
  quit
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
vlan internal allocation policy ascending
!
!
!
interface FastEthernet0
 no ip address
 no ip route-cache cef
 no ip route-cache
 no ip mroute-cache
 shutdown
!
interface GigabitEthernet1/0/1
 spanning-tree portfast
!
interface GigabitEthernet1/0/2
 switchport mode access
!
interface GigabitEthernet1/0/3
 switchport mode access
!
interface GigabitEthernet1/0/4
 switchport mode access
!
interface GigabitEthernet1/0/5
 switchport mode access
!
interface GigabitEthernet1/0/6
 switchport mode access
!
interface GigabitEthernet1/0/7
 switchport mode access
!
interface GigabitEthernet1/0/8
 switchport mode access
!
interface GigabitEthernet1/0/9
 switchport mode access
!
interface GigabitEthernet1/0/10
 switchport mode access
!
interface GigabitEthernet1/0/11
 switchport mode access
!
interface GigabitEthernet1/0/12
 switchport mode access
!
interface GigabitEthernet1/0/13
 switchport mode access
!
interface GigabitEthernet1/0/14
 switchport mode access
!
interface GigabitEthernet1/0/15
 switchport mode access
!
interface GigabitEthernet1/0/16
 switchport mode access
!
interface GigabitEthernet1/0/17
 switchport mode access
!
interface GigabitEthernet1/0/18
 switchport mode access
!
interface GigabitEthernet1/0/19
 switchport mode access
!
interface GigabitEthernet1/0/20
 switchport mode access
!
interface GigabitEthernet1/0/21
 switchport mode access
!
interface GigabitEthernet1/0/22
 switchport mode access
!
interface GigabitEthernet1/0/23
 switchport access vlan 2
 switchport mode access
!
interface GigabitEthernet1/0/24
 switchport access vlan 2
 switchport mode access
!
interface GigabitEthernet1/1/1
!
interface GigabitEthernet1/1/2
!
interface GigabitEthernet1/1/3
!
interface GigabitEthernet1/1/4
!
interface TenGigabitEthernet1/1/1
!
interface TenGigabitEthernet1/1/2
!
interface Vlan1
 ip address 192.168.1.254 255.255.255.0
!
interface Vlan2
 ip address 192.168.2.254 255.255.255.0
!
ip default-gateway 192.168.1.1
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.1.1
ip http server
ip http secure-server
!
!
!
line con 0
line vty 0 4
 password cisco
 login
line vty 5 15
 login
!
end

</pre>
 



