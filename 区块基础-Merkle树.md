# 区块基础-Merkle树

磨链输出计划

Merkle tree中文叫做梅克尔树，这当然不是一棵真正的植物树，merkle tree是计算机数据结构中的一种树，是由计算机科学家 Ralph Merkle 提出的，并以他本人的名字来命名。
![1.png-111.2kB][1]
Merkle tree是数据结构中的一种树结构，可以是二叉树，也可以是多叉树，他和数据结构中树的特点几乎一致，和普通树不同的是：merkle tree上的叶节点存放hash计算后的hash值，非叶节点是其对应的子节点串联的字符串的hash值。上图可知：hash0=hash0-0+hash0-1。中本聪在比特币系统中很好的运用了merkle tree树，现在的以太坊也使用该模式来做区块链系统中的验证互联。

在维基上对merkle tree解释：
![2.png-134kB][2]
在具体介绍merkle tree之前先明确两个概念：

HASH：hash运算一直出现在区块链的介绍文章中，这个技术其实在互联网中一直被广泛应用，一段数据经过hash运算后生成一段密文哈希值，在网络环境中把这个hash值作为一段数据校验，我们在网络上下载文件的时候，把数据文件下载到本地，然后运算hash，如果和之前的hash值一致，那么就说明这个下载文件在网络传输下载过程中并没有损坏和篡改，保证数据一致。Hash算法保证了，一旦黑客或者恶意操作的人对源头数据作任何一点点的修改就会导致整个hash值会出现较大不一样。从而使用这种方式验证数据完整性和安全性还是十分有效的。
![3.png-115.1kB][3]
HASH LIST：结合上图hash算法后，一组数据生成一组hash值。在我们常用的P2P下载软件中，数据通过网络中点对点的方式传输，很多人认为在传输过程中只是一个文件，事实上很多数据文件都会进行一个切割，然后生成一个hash list，一个数据被分割后传输，一旦出现一个或者多个数据被篡改，那么结合hash list就可验证那几个数据块是有问题的，即可选择有问题的数据块重新下载即可。这里涌入一个hash root的概念，在区块链中有merkle hash root值。这个数据值就是拼凑在一起的hash值，通过hash root，我们可以通过事先获知hash root，然后结合hash list计算，逐步校验每一块的hash值并进行组合，组合值和hash root比较，一致就说明数据块是没有问题的，这种方式更好的运用在实际的网络数据传输中。
Merkle Tree：之前的hash list中如果深入思考下，在网络传输中分切很多数据块后，你获得了正确的hash root，那么你必须要获得素有的块hash值才能去组合计算，并和正确的hash root比较，那么这样会导致整个效率问题。Merkle tree的机制就是通过树结构，不需要全部获取块hash值，也可验证。大致说明下原理：
![4.png-50.7kB][4]
这是一个merkle tree，根节点为root值，hash7+hash8生成hash3，hash3+hash4生成hash1。以此类推。简单结合实际网络中应用。Hash（7...14）是一个数据的切块hash值，每一对值生成上一层的hash，也就是hash（3...6），然后生成hash1和hash2，最后hash1+hash2结合后生成merkle root。这种方式在网络中验证恶意或者篡改数据块，事前先有准备的merkle root，这里假设7是恶意节点：
首先比对 merkle tree root，发现不一致。
比对：hash1和hash2，那么可以得知hash2一致，hash1不一致。
比对：hash3和hash4，那么可以得知hash4一致，hash3不一致。
比对：hash7和hash8，那么hash7不一致。
![5.png-53.5kB][5]
在验证恶意节点的时候，上面举例红色节点和hash2，经过这些验证即可，那么hash7很快就被找出来了，这种检索的效率理论上为：Log(N) 。
结合区块链，在比特币系统的使用就是merkle tree root证明，了解比特币的知道，区块链中的区块分为区块头和区块数据。区块头中：上一区块头的hash值、时间戳、难度值、nonce、merkle tree root值。结合笔者前文SPV认证。可以让节点只下载区块头，然后通过merkle tree原理做支付验证。
![6.png-239.6kB][6]
在以太坊中，一个区块头中，包含三个merkle tree（帕特里夏树），分为交易、收据（receipts）、状态。三种状态树支持以下查询操作：这笔交易是否包含在特定的区块中，查询某地址在过去一段时间内，发出某类型事件的所有实例，当前余额，账户真实存在性，合约的假设性运行结果模拟。


  [1]: http://static.zybuluo.com/JackyJin/8h0ra5k7iaphq83lsyoxx62j/1.png
  [2]: http://static.zybuluo.com/JackyJin/3lp73jakb3tuyhkut1t0nvbh/2.png
  [3]: http://static.zybuluo.com/JackyJin/3texg7hgbeu4d77saprj8cek/3.png
  [4]: http://static.zybuluo.com/JackyJin/jebv638xa1mizqzbmw26dlee/4.png
  [5]: http://static.zybuluo.com/JackyJin/x7l48orhvf2oktffe41akufg/5.png
  [6]: http://static.zybuluo.com/JackyJin/79ixep8ned3zufqux2swevl4/6.png
