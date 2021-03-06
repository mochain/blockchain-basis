﻿# POW共识机制

磨链输出计划

## POW共识机制
* 在bitcoin中挖矿目的不是创造新的bitcoin，这是一种激励机制，这种机制也实现了分布式的安全。

* 参考：《精通比特币》

### POW机制简介
POW（Proof of Work），工作量证明机制。我们最直观的理解就是，一份证明，这个证明确认你做了一定的工作量，类似于现代生活中一些检测考试，通过检测考试你就取得了一份证明，只不过这个证明是一个工作量的证明。
工作量证明一开始是以工作量证明系统提出，这个概念来自Cynthia Dwork 和Moni Naor 1993年在学术论文中，是一种拒绝服务攻击和滥用服务的对策，要求发起者需要消耗一定量的计算机资源来进行计算。那么POW这个词汇在1999年 Markus Jakobsson 和Ari Juels的文章中正式提出。
提到工作量证明，一般都会说到hash现金，亚当·贝克（Adam Back）在1997年发明的，用于抵抗邮件的拒绝服务攻击及垃圾邮件网关滥用。在比特币之前，哈希现金被用于垃圾邮件的过滤。哈希现金也被哈尔·芬尼以可重复使用的工作量证明（RPOW）的形式用于一种比特币之前的加密货币实验中。另外，戴伟的B-money、尼克·萨博的比特金（Bit-Gold）这些比特币的先行者，都是在哈希现金的框架下进行挖矿的。


### 工作证明原理
首先工作量证明需要客户端做一个有难度的工作且得出一个结果，这个结果公布后，验证的一方需要很快能进行验证。这是不对等的。比如我们在一个字符串后加一个随机数（nonce），对这个字符串进行SHA256计算，然后得到的结果用16进制来表示，我们要求这个计算后的16进制表示的初始几位为：0000，那么才能算通过了验证。这种规则就需要计算机去不断的尝试，当然你可以记得其中一些，但是这个概率毕竟是很小的。正常情况下需要不断的输出计算尝试，直到出现正确的要求结果。
数学期望值，计算过程中会统计实际的计算次数，平均后得到的计算的次数，这个数学期望就是要求的“工作量”，当然这是一个符合数学统计学中的概率事件。


### bitcoin中的POW共识机制
bitcoin的出现让人们开始了解到POW共识机制，在bitcoin中，把挖矿生成一个新的区块并把交易数据写入区块看做是一道 **工作量证明的数学难题**，那么这道题目中有四个重点：
1.工作量证明函数：bitcoin中使用的就是SHA256算法，这个算法是输出256位的hash函数（本文不对hash函数和SHA265函数做具体说明）。目前还未出现针对SHA256算法的有效攻击方法，当然通过算法漏洞攻击这里不展开讨论。
2.区块头：bitcoin中的一个区块由区块头和区块中包含的交易列表组成（大小为1M）,这里简述下区块头的组成：

* 区块头大小为80字节。
* 4字节的版本号。
* 32字节的上一个区块的散列值。
* 32字节的Merkle Root Hash，体现区块头和区块中的交易的关系，区块中包含的交易列表，通过Merkle  Tree算法生成Merkle Root Hash。
* 4字节的当前的难度值。
* 4字节的随机数（nonce）。

3.难度值：difficulty，这是一个指标，不恒定。它最为关键的作用就是决定了bitcoin网络中，矿工需要经过多少次hash运算才能获得记账权生成区块，进而获得区块奖励（12.5bitcoin）。bitcoin中区块产生的平均速率是10分钟一个，每经过2016个区块后，节点按照公式：新难度值 = 旧难度值 * ( 过去2016个区块花费时长 / 20160 分钟 )调整难度值。控制区块的平均产生时间，如果产生区块速率比10分钟快，那么增加难度值，比10分钟慢就降低难度。

4.目标值：target，目标值公式：目标值 = 最大目标值 / 难度值
最大目标值是一个恒定值：
0x00000000FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF。目标值的大小和难度值是一个反比的关系。在bitcoin中矿工计算出来的区块的hash值必须小于这个目标值。换个说法方便理解：工作量证明过程中不断尝试变换nonce进行SHA256的计算，那么尝试的目的是为了找到一个指定前面有一定数量000的值，这个时候前面要求的0越多，那么表示你的难度越大。
（为什么0越多难度越大？你尝试下用不断扔一对骰子以得到小于一个特定点数的游戏。第一局，目标是12。只要你不扔出两个6， 你就会赢。然后下一局目标为11。玩家只能扔10或更小的点数才能赢，假如目标降低为了2，那就难度可想而知。）


### 工作量证明过程
整个工作量证明过程其实不复杂。

* 生成币基交易coinbase。
* 打包交易，组成一个交易列表。
* 通过Merkle Tree算法生成Merkle Root Hash。
* 组装区块头。
* 区块头作为工作量证明的输入，不断变换nonce值，通过公式：SHA256(SHA256(Block_Header))双重SHA256计算。结果不断和当前网络的目标值进行比对，一旦发现小于了目标值(target)，那么工作量证明完成。
* 广播区块到网络中，网络中节点验证。
* 验证后等待后续区块生成确认（一般6个）。


大致介绍了POW共识机制。






