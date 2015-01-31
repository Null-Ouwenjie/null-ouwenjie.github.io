---
layout: post
title: 《图解HTTP》- 第7章
category: 计算机基础
date: 2015-01-31
---


##第7章  确保 Web 安全的 HTTPS     

>在HTTP协议中有可能存在信息窃听或身份伪装等安全问题。     
>使用 HTTPS 通信机制可以有效地防止这些问题。     
>本章我们就了解一下 HTTPS。    


### HTTP 的缺点     

** HTTP 主要有这些不足，举例如下：    

- 通信使用明文（不加密），内容可能被窃听     
- 不验证通信方的身份，因此有可能遭遇伪装     
- 无法证明报文的完整性，所以有可能已遭篡改      


####`通信使用明文可能会被窃听`          

######TCP/IP 是可能被窃听的网络     

######加密处理防止被窃听     
>1. 通信的加密：HTTP 协议中没有加密协议，但可以通过和 SSL 或 TLS 的组合使用，加密 HTTP 的通信内容。称为 HTTPS 。     
>2. 内容的加密：客户端需要对 HTTP 报文进行加密处理后再发送请求。但是仍旧有被篡改的风险。     

####`不验证通信方的身份就可能遭遇伪装`              

######任何人都可发送请求       

- 无法确定请求发送至目标的Web服务器是否是按真实意图返回响应的那台服务器。**有可能是已伪装的Web服务器**     
- 无法确定响应返回到的客户端是否是意图接收响应的那个客户端。**有可能是已伪装的客户端**     
- 无法确定正在通信的对方是否具备访问权限。 因为某些Web服务器上保存着重要的信息，只想发给特定用户通信的权限。     
- 无法判定请求是来自何方、出自谁手。     
- 即使是无意义的请求也会照单全收。**无法阻止海量请求下的DoS攻击。      

###### 查明对手的证书       

- SSL 不仅提供加密处理，而且还使用了一种被称为证书的手段，可用于确定对方。     
- 证书由值得信任的第三方机构颁发，用以证明服务器和客户端是实际存在的。     
- 伪造证书从技术角度来说是异常困难的一件事。    


####`无法证明报文的完整性，可能已遭篡改`           

######接收到的内容可能有误      

###### 如何防止篡改      

1. 常用MD5和SHA-1等散列值校验的方法，以及用来确认文件的数字签名方法。      

**`为了有效的避免这些弊端，有必要使用 HTTPS `**     



### HTTP + 加密 + 认证 + 完整性保护 = HTTPS    

####` HTTP 加上加密处理和认证以及完整性保护后即是 HTTPS `      

> 为了解决 HTTP 的缺陷，需要在 HTTP 上再加上加密处理和认证等机制。     
我们把添加了加密及认证机制的 HTTP 称为 HTTPS 。     

####` HTTPS 是身披 SSL 外壳的 HTTP `      

>通常 HTTP 和 TCP 是直接通信。 当使用 SSL 时，则演变成先 HTTP 和 SSL 通信，再由 SSL 和 TCP 通信了。     

####`相互交换密钥的公开密钥加密技术`      

> SSL 采用一种叫做公开密钥加密的加密处理方式    

######共享密钥加密的困境     

>发送密钥就有被窃听的风险，但不发送，对方就不能解密。再说，如果密钥能够安全到达，那数据也应该能安全到达，密钥就多余了。     

######使用两把密钥的公开密钥加密        

>公开密钥加密使用一堆非对称的密钥。一把叫做私有密钥，一把叫做公开密钥。     
>使用公开密钥进行加密、使用私有密钥进行解密。    


###### HTTPS 采用混合加密机制    

>在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则使用共享密钥加密方式。     

####`证明公开密钥正确性的证书`     

>公开密钥存在一些问题，那就是无法证明公开密钥本身就是货真价实的公开密钥。    
或许在公开密钥传输途中，真正的公开密钥已被攻击者替换掉了。    
>为了解决上述问题，可以使用由数字证书认证机构和其相关机关颁发的公开密钥证书。      

**判定公开密钥的步骤**     

1. 服务器把自己的`公开密钥`登录至数字证书认证机构    
2. 数字证书认证机构用自己的`私有密钥`向服务器的公开密码署数字签名并颁发`公钥证书`     
3. 客户端拿到服务器的公钥证书后，使用数字证书认证机构的`公开密钥`，     
向数字证书认证机构验证公钥证书上的数字签名，以确认服务器的公开密钥的真实性。     
4. 使用服务器的`公开密钥`对报文加密后发送。     
5. 服务器用私有密钥对报文解密。     


######可证明组织真实性的EV SSL 证书       

>证书的一个作用是用来证明作为通信一方的服务器是否规范，     
另外一个作用是`可确认对方服务器背后运营的企业是否真是存在`。    
>持有EV SSL 证书的 Web 网站的浏览器地址栏处的背景色的`绿色`的，     
而且在地址栏的左侧显示了 SSL 证书中记录的组织名称以及办法证书的认证机构的名称。     

######用以确认客户端的客户端证书     

客户端证书存在以下问题：     

1. 证书的获取及发布。证书是需要自行安装的，不是所有人都会安装的。     
2. 客户端证书是需要付费购买的。      
3. 客户端证书毕竟只能用来证明客户端是实际存在的，而不能用来证明用户本人的真实有效性。     
（比如，用户的计算机安装了客户端证书，但是当其他人获得主人的计算机的使用权限时，也就意味着获得了证书的权限。）     

######认证机构信誉第一     

>SSL 机制中介入认证机构之所以可行，是因为建立在其信用绝对可靠这一大前提下的。    

######由自认证机构颁发的证书被称为自签名证书      
>如果使用OpenSSL 这套开源程序，每个人都可以构建一套属于自己的认证机构，从而自己给自己颁发服务器证书。     



####` HTTPS 的安全通信机制`     

** HTTPS 通信步骤 **        

1. 客户端通过发送Client Hello 报文开始 SSL 通信。指定 版本、`加密组件`列表等。     
2. 服务器可进行 SSL 通信时，会以 Server Hello 报文作为应答。     
3. 之后服务器发送Certificate 报文。报文中包含`公开密钥证书`   。     
4. 最后服务器发送 Server Hello Done 报文通知客户端，最初阶段的 SSL 握手协商部分结束。    
5. SSL 第一次握手结束之后，客户端以 Client Key Exchange 报文作为回应。     
该报文已使用步骤3中的`公开密钥`进行加密。报文中包含了`Pre-master secret`随机密码串     
6. 接着客户端继续发送 Change Cipher Spec 报文。在此报文之后的通信会采用   `Pre-master secret`密钥加密。     
7. 客户端发送 Finished 报文。 该报文包含连接至今全部报文的整体校验值。     
这次握手协商能否能够成功，要以服务器能否正确解密该报文作为判定标准。     
8. 服务器同样发送 Change Cipher Spec 报文。     
9. 服务器同样发送 Finished 报文。     
10. 服务器和客户端的Finished 报文交换完毕后， SSL 连接就算建立完成。     
从此处开始进行应用层协议的通信，即发送HTTP请求。（通信会受 SSL 的保护）     
11. 应用层协议通信，即发送 HTTP 响应。     
12. 最后由客户端断开连接。断开连接时，发送close_notify报文。之后再发送TCP FIN报文来关闭与 TCP 的通信。    
**在以上流程中，应用层发送数据时会附加一种叫做MAC（Message Authentication Code）的报文摘要。MAC能够查知报文是否遭到篡改，从而保护报文的完整性**     


###### SSL 和 TLS       

>使用 SSL时，它的处理速度会变慢。     
>需要加密和解密运算，增重硬件负担。     

- - - - - - - -        


>**为什么不一直使用 HTTPS ？**        

>1. 消耗CPU和内存        
>2. 加重服务器负载    
>3. 购买证书需要开销    

