---
title: 「译」什么是 IKEv2
date: 2022/03/15 15:40:00
categories: 网络
tags:
  - 翻译
  - 转载
  - IKEv2
  - IPsec
---

> 转载自 CactusVPN ，原文：[What Is IKEv2?](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-ikev2/)，如有侵权，请联系删除

**IKEv2 VPN** 协议在过去几年中变得越来越流行——尤其是在移动用户中。考虑到协议的效率和安全性，不难看出为什么。

但实际上 IKEv2 是什么？它如何设法为在线用户提供安全的在线体验？好吧，这是您需要了解的所有信息（以及更多信息）：

> **什么是 IKEv2？**
>
> IKEv2（Internet Key Exchange version 2）是一种处理请求和响应操作的[VPN 加密协议](https://www.cactusvpn.com/beginners-guide-to-vpn/vpn-protocol/)。它通过在身份验证套件中建立和处理 SA（Security Association）属性来确保流量是安全的——通常是 IPSec，因为 IKEv2 基本上基于它并内置于其中。
>
> IKEv2 是微软与思科共同开发的，是 IKEv1 的继承者。

## 这是 IKEv2 的工作原理

与任何 VPN 协议一样，IKEv2 负责在 [VPN 客户端](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-a-vpn-client-software/) 和 [VPN 服务端](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-a-vpn-server-how-does-a-vpn-server-work/) 之间建立安全隧道。它首先对客户端和服务器进行身份验证，然后就将使用哪种[加密方法](https://www.cactusvpn.com/beginners-guide-to-vpn/vpn-encryption/)达成一致

我们已经提到 IKEv2 处理 SA 属性，但 SA 是什么？简单地说，就是在两个网络实体（在本例中为 VPN 客户端和 VPN 服务器）之间建立安全属性的过程。它通过为两个实体生成相同的对称加密密钥来做到这一点。然后，所述密钥用于加密和解密通过 VPN 隧道传输的所有数据。

### 关于 IKEv2 的一般技术细节

- IKEv2 支持 IPSec 的最新加密算法，以及多种其他加密密码
- 通常，IKE 守护进程（作为后台进程运行的程序）在用户空间（专用于运行应用程序的系统内存）中运行，而 IPSec 堆栈在内核空间（操作系统的核心）中运行。这有助于提高性能
- IKE 协议使用 UDP 数据包和 UDP 端口 500。通常，创建 SA 需要 4 到 6 个数据包
- IKE 基于以下底层安全协议
    - [ISAKMP](https://en.wikipedia.org/wiki/Internet_Security_Association_and_Key_Management_Protocol)（Internet Security Association and Key Management Protocol，互联网安全协会和密钥管理协议）
    - [SKEME](http://www.di-srv.unisa.it/~ads/corso-security/www/CORSO-9900/oracle/skeme.pdf)（Versatile Secure Key Exchange Mechanism，多功能安全密钥交换机制）
    - [OAKLEY](https://en.wikipedia.org/wiki/Oakley_protocol)（Oakley Key Determination Protocol，奥克利密钥确定协议）
- IKEv2 VPN 协议支持 MOBIKE（IKEv2 Mobility and Multihoming Protocol，IKEv2 移动性和多宿主协议），该功能允许协议抵抗网络变化。
- IKEv2 支持 PFS ([Perfect Forward Secrecy](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-pfs/))
- 虽然 IKEv2 是由 Microsoft 与 Cisco 共同开发的，但也有该协议的开源实现 (如 [OpenIKEv2](https://sourceforge.net/projects/openikev2), [Openswan](https://www.openswan.org/) 和 [strongSwan](https://www.strongswan.org/download.html))
- IKE 在处理身份验证过程时使用 [X.509 证书](https://en.wikipedia.org/wiki/X.509)

## IKEv1 vs IKEv2

下面列出了 IKEv2 和 IKEv1 之间的主要区别：

- 由于其 [EAP](https://en.wikipedia.org/wiki/Extensible_Authentication_Protocol) 身份验证，IKEv2 默认提供对远程访问的支持
- IKEv2 比 IKEv1 消耗更少的带宽
- IKEv2 VPN 协议对双方都使用加密密钥，使其比 IKEv1 更安全
- IKEv2 支持 MOBIKE，这意味着它可以抵抗网络变化
- IKEv1 不像 IKEv2 那样具有内置的 NAT 穿越功能
- 与 IKEv1 不同，IKEv2 实际上可以检测 VPN 隧道是否 “alive”。该功能允许 IKEv2 自动重新建立断开的连接
- IKEv2 加密支持比 IKEv1 更多的算法
- IKEv2 通过改进的序列号和确认，提高了可靠性
- IKEv2 协议将首先确定请求者是否实际存在，然后再继续执行任何操作。因此，它更能抵抗 [DoS 攻击](https://www.avg.com/en/signal/what-is-ddos-attack)

## IKEv2 安全吗？

是的，IKEv2 是一种可以安全使用的协议。它支持 256 位加密，可以使用 AES、3DES、Camellia 和 ChaCha20 等密码。更重要的是，IKEv2/IPSec 还支持 PFS + 协议的 MOBIKE 功能，确保您在更换网络时不会掉线。

另一个值得一提的是，IKEv2 的基于证书的身份验证过程确保在确认请求者的身份之前不采取任何行动。

另外，微软确实致力于 IKEv2，这不是一个非常值得信赖的公司。然而，他们并不是单独研究协议，而是与思科合作。此外，IKEv2 并不是完全封闭的源代码，因为该协议存在开源实现。

尽管如此，我们仍应解决与 IKEv2/IPSec 有关的三个安全相关问题：

### 1. 密码问题

[早在 2018 年](https://hackernoon.com/security-gaps-found-in-ipsec-5a075b44609e?gi=a8b84ed0d0c0)，一些研究就凸显了 IKEv1 和 IKEv2 的潜在安全漏洞。只要您不使用 IKEv1 协议，IKEv1 问题就不应该真正困扰您。至于 IKEv2 问题，如果它使用的登录密码较弱，它似乎相对容易被黑客入侵。

不过，[如果您使用强密码](https://www.cactusvpn.com/beginners-guide-online-security/password-best-practices/)，这通常不是一个巨大的安全问题。如果您使用的是第三方 VPN 服务，也可以这样说，因为他们将代表您处理 IKEv2 登录密码和身份验证。只要您选择一个体面、安全的提供商，应该没有问题。

### 2. NSA 对 ISAKMP 的利用

德国*《明镜周刊》(Der Spiegel)*公布了泄露的美国国家安全局(NSA)演示文件，声称 NSA 能够利用 IKE 和 ISAKMP 对 IPSec 流量进行解密。如果您不知道，IPSec 使用 ISAKMP 来实现 VPN 服务协商。

不幸的是，细节有点模糊，并且没有确切的方法来保证演示文稿是有效的。如果您非常担心这个问题，您应该避免自己建立连接，而是从使用强大加密密码的可靠 VPN 提供商处获得 IKEv2 连接。

### 3. 中间人攻击

似乎旨在允许协商多个配置的 IPSec VPN 配置，可能会受到 [downgrade attacks](https://en.wikipedia.org/wiki/Downgrade_attack)（一种中间人攻击）。即使使用 IKEv2 而不是 IKEv1，也会发生这种情况。

## IKEv2 快吗？

是的，IKEv2/IPSec 提供了不错的网速。事实上，它是对在线用户可用的最快的 VPN 协议之一——甚至可能和 PPTP 或 SoftEther一 样快。这一切都归功于其改进的架构和高效的响应/请求消息交换过程。此外，它运行在 UDP 端口 500 上的事实确保了低延迟。

更好的是，由于其 MOBIKE 功能，您无需担心 IKEv2 的速度会在您更换网络时下降或中断。

## IKEv2 的优缺点

优点

- IKEv2 安全性非常强，因为它支持多种高端密码
- 尽管 IKEv2 安全标准很高，网速依旧很快
- IKEv2 由于其 MOBIKE 支持，可以轻松抵抗网络变化，并且可以自动恢复断开的连接
- IKEv2 可在黑莓设备上使用，也可以在其他移动设备上进行配置
- 设置 IKEv2 VPN 连接相对简单

缺点

- 由于 IKEv2 仅使用 UDP 端口 500，防火墙或网络管理员可能会阻止它
- IKEv2 不像其他协议（PPTP、L2TP、OpenVPN、SoftEther）那样提供跨平台兼容性

## 什么是 IKEv2 VPN 支持？

IKEv2 VPN 支持基本上是[第三方 VPN 提供商](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-a-vpn-provider/)通过其服务提供对 IKEv2/IPSec 连接的访问。幸运的是，越来越多的 VPN 提供商已经开始认识到该协议对移动用户的重要性，因此您现在比以前更有可能找到提供 IKEv2 连接的服务。

尽管如此，我们还是建议选择可以访问多种 VPN 协议的 VPN 提供商。虽然 IKEv2/IPSec 在移动设备上是一个很好的协议，但当您在家中使用其他设备时，拥有一个像样的备份（如 OpenVPN 或 SoftEther）并没有什么坏处。

## IKEv2 与其他 VPN 协议

在开始之前，我们应该提到，当我们在本节中讨论 IKEv2 时，我们将指代 IKEv2/IPSec，因为这是 VPN 提供商通常提供的协议。此外，IKEv2 通常不能单独使用，因为它是内置于 IPSec 中的协议（这就是它与它配对的原因）。有了这个，让我们开始吧：

### 1. IKEv2 vs L2TP/IPSec

当 VPN 提供商提供 L2TP 和 IKEv2 时，它们通常与 IPSec 配对。这意味着它们倾向于提供相同级别的安全性。尽管如此，虽然 L2TP/IPSec 是闭源的，但 IKEv2 有开源实现。斯诺登声称美国国家安全局削弱了L2TP/IPSec，尽管没有确凿的证据支持这一说法。

IKEv2/IPSec 比 L2TP/IPSec 更快，因为 L2TP/IPSec 的双重封装特性更占用资源，而且协商 VPN 隧道的时间也更长。虽然由于与 IPSec 配对，这两种协议几乎使用相同的端口，但 L2TP/IPSec 可能更容易被 NAT 防火墙阻止，因为 L2TP 有时往往不能很好地与 NAT 配合使用——特别是在路由器上没有启用 L2TP 直通的情况下。

另外，当我们谈到稳定性时，应该提到的是，IKEv2 比 L2TP/IPSec 稳定得多，因为它可以抵抗网络变化。基本上，这意味着您可以在 IKEv2 连接不中断的情况下从 WiFi 连接切换到数据计划连接。更不用说即使 IKEv2 连接断开，它也会立即恢复。

至于可访问性，L2TP/IPSec 在比 IKEv2/IPSec 更多的平台上原生可用，但 IKEv2 在黑莓设备上可用。

总的来说，对于移动用户来说，IKEv2/IPSec 似乎是一个更好的选择，而 L2TP/IPSec 则适用于其他设备。

> 如果您想了解有关 L2TP 的更多信息，[请点击此链接](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-l2tp/)

### 2. IKEv2 vs IPSec

IKEv2/IPSec 在所有方面都比 IPSec 好得多，因为它在 IKEv2 的高速和稳定性的基础上提供了 IPSec 的安全优势。此外，IKEv2 本身并不能与 IPSec 进行比较，因为 IKEv2 是 IPSec 协议套件中使用的协议。另外， IKEv2 本质上是基于 IPSec 隧道。

> 如果您想了解更多关于 IPSec 的信息，[请查看我们关于它的文章](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-ipsec/)

### 3. IKEv2 vs OpenVPN

OpenVPN 由于其增强的安全性而在在线用户中非常受欢迎，但您应该知道 IKEv2 可以提供类似级别的保护。确实，IKEv2 在 IP 级别保护信息，而 OpenVPN 在传输级别做到这一点，但这并不是什么应该有很大区别的事情。

但是，我们不能否认 OpenVPN 是开源的，这使它成为比 IKEv2 更具吸引力的选择。当然，如果您使用 IKEv2 的开源实现，这不再是一个大问题。

就在线速度而言，IKEv2 通常比 OpenVPN 更快——即使 OpenVPN 使用 UDP 传输协议也是如此。另一方面，网络管理员更难阻止 OpenVPN 连接，因为该协议使用端口 443，这是 [HTTPS](https://www.cactusvpn.com/vpn/https-vs-vpn/) 流量端口。不幸的是，IKEv2 仅使用网络管理员可以阻止的 UDP 端口 500，而不必担心阻止其他重要的在线流量。

至于连接稳定性，两种协议的表现都不错，但 IKEv2 在移动设备上超过了 OpenVPN，因为它可以抵抗网络变化。的确，OpenVPN 可以配置为使用 “float” 命令执行相同的操作，但它不如 IKEv2 高效和稳定。

关于跨平台支持，IKEv2 有点落后于 OpenVPN，但它确实适用于黑莓设备。此外，IKEv2 通常更容易设置，因为它通常原生集成到可用的平台中。

> 想了解更多关于 OpenVPN 的信息吗？[这是我们写的关于它的深入指南](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-openvpn/)

### 4. IKEv2 vs PPTP

IKEv2 通常是比 PPTP 更好的选择，因为它比 PPTP 更安全。一方面，它支持 256 位加密密钥和 AES 等高端密码。此外，据我们所知，IKEv2 流量尚未被 NSA 破解。[PPTP 流量就不能这么说了](https://hacker10.com/internet-anonymity/secret-documents-show-the-nsa-is-spying-on-vpn-users/)。

除此之外，PPTP 远不如 IKEv2 稳定。它不能像 IKEv2 那样轻松抵抗网络变化，而且更糟糕的是它非常容易被防火墙阻止——尤其是 NAT 防火墙，因为 PPTP 在 NAT 上不原生支持。事实上，如果路由器上没有启用 PPTP [透传](https://www.cactusvpn.com/beginners-guide-to-vpn/vpn-passthrough/)，甚至无法建立 PPTP 连接。

通常，PPTP 使其在竞争中脱颖而出的主要亮点之一是其非常高的速度。好吧，有趣的是，IKEv2 实际上能够提供与 PPTP 提供的速度相似的速度。

基本上，PPTP 比 IKEv2 更好的唯一方法是在可用性和易于设置方面。您会看到，PPTP 原生内置于大量平台中，因此配置连接非常简单。不过，由于对 PPTP 的本机支持已开始从某些操作系统的较新版本中删除，因此未来可能不会出现这种情况。例如，PPTP 在 iOS 10 和 macOS Sierra 上不再原生可用。

总而言之，如果可能，您应该始终选择 IKEv2 而不是 PPTP。

> 如果您想了解有关 PPTP 的更多信息，并了解它为何如此危险的选择，[请点击此链接](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-pptp/)

### 5. IKEv2 vs WireGuard®

您的数据在这两种协议下都应该是安全的。但是，如果您想要一种更现代的密码学方法，您应该坚持使用 WireGuard。虽然 IKEv2 有一些开源实现，但大多数提供商不使用它们。

与 L2TP/IPSec 一样，IKEv2/IPSec 更容易被阻止，因为它使用的端口更少：*UDP 500*、*ESP IP 协议 50*、*UDP 4500*。从好的方面来说，IKEv2 提供了 MOBIKE——一种让协议抵抗网络变化的功能。因此，如果您在移动设备上从 WiFi 切换到移动数据，您的 VPN 不应断开连接。

两种协议都非常快。使用 WireGuard 有时我们的速度会更快，但速度不会快很多。

它们都适用于大多数操作系统。唯一的区别是 IKEv2/IPSec 在黑莓设备上本机可用。

无论您需要速度还是安全性（或两者兼而有之），任一协议都是绝佳选择。如果您使用智能手机，可能会坚持使用 IKEv2/IPSec，因为 MOBIKE 功能非常有用。

> 如果您想了解有关 Wireguard 的更多信息，[请参阅我们指南的链接](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-wireguard/)

### 6. IKEv2 vs SoftEther

IKEv2 和 SoftEther 都是相当安全的协议，尽管 SoftEther 可能因为它是开源的而更值得信赖，但您也可以找到 IKEv2 的开源实现。两种协议也都非常快，尽管 SoftEther 可能比 IKEv2 快一点。

当谈到稳定性时，情况就不同了。一方面，SoftEther 更难被防火墙阻止，因为它运行在端口 443（HTTPS 端口）上。另一方面，IKEv2 的 MOBIKE 功能使其能够无缝抵抗网络变化（例如当您从 WiFi 连接切换到数据计划时）。

您可能还想知道，虽然 SoftEther VPN 服务器支持 IPSec 和 L2TP/IPSec 协议(以及其他协议)，但它不支持 IKEv2/IPSec 协议。

最后，SoftEther 是比 IKEv2 更好的选择，尽管如果你是移动用户，你可能更喜欢使用 IKEv2——尤其是因为它可以在黑莓设备上使用。

> 如果您想了解有关 SoftEther 的更多信息，[请查看此链接](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-softether/)

### 7. IKEv2 vs SSTP

IKEv2 和 SSTP 提供了类似的安全级别，但 SSTP 具有更强的防火墙抵抗力，因为它使用 TCP 端口 443，该端口通常不能被阻止。另一方面，SSTP 不像 IKEv2 那样在许多平台上可用。 SSTP 仅内置于 Windows 系统（Vista 及更高版本）中，并且可以在路由器、Linux 和 Android 上进一步配置。 IKEv2 适用于所有这些平台以及更多（macOS、iOS、FreeBSD 和 BlackBerry 设备）。

IKEv2 和 SSTP 都是 Microsoft 开发的，但 IKEv2 是 Microsoft 与 Cisco 共同开发的。这使得它比微软全资拥有的 SSTP 更值得信赖——微软[过去曾让 NSA 访问加密消息](https://www.theguardian.com/world/2013/jul/11/microsoft-nsa-collaboration-user-data)，这也[是 PRISM 监视计划的一部分](https://www.cloudwards.net/prism-snowden-and-government-surveillance/)。

就连接速度而言，这两种协议相当紧密，但 IKEv2 很可能比 SSTP 更快。为什么？因为 SSTP 的速度经常与 OpenVPN 的速度进行比较，而且我们已经提到 IKEv2 比 OpenVPN 更快。除此之外，还有一个事实是 SSTP 只使用 [TCP，它比 UDP（IKEv2 使用的传输协议）慢](https://www.cactusvpn.com/beginners-guide-to-vpn/tcp-vs-udp/)。

> 有兴趣了解更多关于 SSTP 的信息吗？[这是我们写的一篇文章](https://www.cactusvpn.com/beginners-guide-to-vpn/what-is-sstp/)

### 那么，IKEv2 协议是一个好的选择吗？

是的，IKEv2 是提供安全、流畅的在线体验的不错选择。我们仍然建议您使用 OpenVPN 或 SoftEther，但如果这些选项由于某种原因不可用，IKEv2 也可以很好地工作——尤其是如果您使用手机并且经常旅行。

## 综上所述什么是 IKEv2？

IKEv2 既是 VPN 协议又是 IPSec 套件中使用的加密协议。

本质上，它用于在 VPN 客户端和 VPN 服务器之间建立和验证安全通信。

IKEv2 使用起来非常安全，因为它支持强大的加密密码，并且还改进了 IKEv1 中存在的所有安全漏洞。此外，IKEv2 是移动用户的绝佳选择，因为它支持 MOBIKE，允许 IKEv2 连接抵抗网络变化。

尽管如此，我们还是建议选择提供与 IKEv2 一起访问多种协议的 VPN 提供商。虽然它对移动用户来说是一个很好的选择，但拥有更好的协议（如 OpenVPN 和 SoftEther）作为其他设备的替代品也没有什么坏处。
