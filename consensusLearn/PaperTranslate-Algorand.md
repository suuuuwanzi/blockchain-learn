# Algorand - Silvio Micali

Algorand, because use algorithmic randomness to select a set of verifiers who are in charge of constructing the next block of valid transactions.we ensure that such selections are provably immune from manipulations and unpredictable until the last minute, but also that they ultimately are universally clear.

In Algorand all power resides with the set of all users.不会把用户分成矿工、普通用户。

### 比特币的假设

**<font size=3.5 color="green">Honest Majority of Computational Power</font>**

### 比特币的问题

1. Computational Waste
2. Concentration of Power 5个矿池
3. Ambiguity（分叉）必须等待几个后续的块作为确认
4. law-enforcement and monetary-policy 执法和货币政策，比特币的匿名性可能被用来洗钱或金融犯罪或者用来
5. The risk of validator centralization, network forking, block scarcity and high energy costs required to mine a block have all been extensively debated with no realistic long-term solutions to date. 

##引言
为什么提出Algorand

- Money变得越来越virtual
- 没法找到一个全球可信的中心化实体
- 账本没办法被有效的管理

Algorand：想实现一个分布式账本，但是具有类似集中式系统所具备的便利和效率。用**随机**取选择验证者，保证选择是无法被操纵和控制的，直到选择出验证者之前，所有人都无法预测。分叉可能性大约是一万亿分之一

## 3 Algorand 简述
Algorand的核心：

* 提出新的**拜占庭协议**（Byzantine Agreement）：更快、支持节点的动态变换
* 分类加密：从大集合中随机挑选出一部分作为验证者集合  random selection。利用密码学函数，根据前r-1个区块的验证者集合，选择区块r的验证者集合

Algorand的主要**挑战**：

1. 不需要准入，任何人可以在任何时间加入Algorand中
2. 对手是动态的，且十分强大（可以在任何时间影响任何用户，且有团结所有恶意节点的能力

**Algorand系统的两个阶段**：

1. 假设：恶意节点不能引入新的恶意节点，少于1/3是恶意用户，用户每个人只有一个key
2. 用户可以有许多key，恶意节点可以引入恶意用户，至多1/3的资金被掌握在恶意用户的手中

**网络不是完全同步或者完全异步的，假设固定大小的信息，总是在已知的一段时间内被固定百分比的用户收到，两种消息大小1KB（支付、控制信息）/1MB（区块），比特币中，1KB的消息在1s左右被95%的用户收到，1MB的区块消息在少于1.5分钟的时间被95%的用户收到**

> 比特币： block-generation latency 远大于 block-transmission latency ，10分钟是块产生延迟，而实际还要等待3个确认才可以认为分叉可能低，实际的块产生延迟为30分钟

**Algorand的优势**：

1. 几乎不可能分叉
2. 计算量需求小
3. block-generation latency 和 block-transmission latency接近，都是秒级
4. 由于几乎不会分叉，下一块产生的时延即为真正的延迟

下一个区块的产生时间为\\(\Lambda +(3+m)\lambda \\),\\(\Lambda\\)是x个不同的区块抵达一定比例用户处所需的时间（如，95%的用户收到这个区块的时间），\\(\lambda\\)是1KB的消息\\(\chi\\)被相应比例用户收到的时延，\\(2^{-m}\\)是分叉产生的概率。

eg：如果90%的钱被诚实的一方掌握，x（产生的不同的区块）是很少的，\\(\chi\\)大约=200，当诚实一方所占比例下降，Algorand的性能将大打折扣（如果恶意方控制20%的资金，则\\(\chi\\) = 750）

**Simple Honest Majority**:
simple majority，在验证者是个小集合的时候适用


**附加属性：Lazy Honesty**：

至多\\(\frac {1} {3}\\)的player是被对手操纵的（be controlled and perfectly coordinated）
诚实节点少量的absences:突然失去连接或重启可被容忍
但如果诚实节点集体失联（on vacation en mass：集体度假），实际参与到协议中的节点大多数变成了恶意节点，因为作恶者是不会休息，持续攻击的。


*<font size=3.5 color="red">Algorand can work safely with "lazy-but-honest" users</font>*

**Lazy Honesty**
简单来说，一个用户是lazy-but-honest的,如果：

1. 当用户加入协议的时候，是遵守协议内容的
2. 用户加入是比较低频率的，如一周接入协议一次，提前给予适当通知，在参与时可以收到出块奖励


---
其他相关工作：

另一篇论文:作者： Bentov, Pass and Shi，同样是采用Byzantine aggrement，用哈希函数选择leaders。但是这个算法需要准入，且在系统开始时已经确定，恶意善意节点是哪些，是固定的

---
## 4 挑战

1. The Quantity \\(Q^r\\) ->a seperate block quantity
上一个区块\\(B^{r-1}\\)决定了第r轮的验证者集合，leader决定了新的区块\\(B^r\\),如果只是根据上一轮的slightly different payment选择第r轮的leader，则Adversary有很大可能可以猜到并控制next leader.
2. Secret Credentials(秘密凭证)：仅仅只是随机并unambiguously（清楚地）挑选验证者集合和leader负责出块是不够的，leader和verifiers必须秘密的达成共识，但是提供相应的证明（为了防止被Adversary 攻击）。当用户意识到自己是出下一个块的leader时，他秘密的构建好块，并广播出去，同时要广播他自己的credential，此时如果Adversary意识到谁是新区块的leader，可以立刻对leader发起攻击，但是因为区块\\(B^r\\)已经被广播出去，Adversary也无法阻止消息的传播。 **无法保证leader的唯一性，不能保证所有人都知道leader是谁，包括leader本身，但系统的step是非常明确的**
3. Replaceable-Player Protocols（节点可替换协议）：leader广播一个区块然后die（可能是被攻击者攻击），系统是可以正常运行的，但是如果\\(SV^r\\)中的验证者节点宕机，就有点麻烦。验证者节点在收到leader广播的区块后必须运行BA协议，BA协议有许多step，传统BA协议由单个步骤组成的子协议迭代，每个迭代要有1/3的概率达成一致（假设有大于2/3的节点是诚实的），但是在本系统中不一样，我们假设的Adversary非常强大，虽然不能corrupt所有用户中的1/3，但是可以corrupt\\(SV^r\\)中的所有节点，因为\\(SV^r\\)中的节点是所有用户的一个小子集。**然而，本系统at each step, a totally new set of players can take over, and yet the protocol continues to work properly.**
每一step，都会随机且独立的挑选出一个新的节点集合，和前一个步骤的players没有交集，节点数量也每个step不同，此step的节点不知道下一个step的节点集合。


## 5 Model, Discussion, and New Block Structures
 

### 5.2 理想化的公共账本

Algorand模仿的是下述支付系统，based on an idealized public ledger.

1. 初始状态：钱与个人的公钥绑定起来，initial status:![](https://i.imgur.com/xJKtNjw.gif)，其中pk代表公钥，a是相应余额
2. Payment:用户1的公钥pk，余额\\(a \geq 0\\),用户2的公钥pk'和余额a'，\\(a \geq a'\\),一个**有效的payment**用\\(\wp\\)表示，pk给pk'转账，这笔payment的表示方式:![](https://i.imgur.com/CENjDq4.png),\\(I\\)表示附加的非敏感信息，如时戳和交易号，L表示敏感信息，如发生交易的事由，pk和pk'的身份等。pk->payer,pk'->payee,a'代表交易金额
3. The Magic Ledger:账本列表\\(L=PAY^1,PAY^2,...\\),理想系统里，每一笔支付都是有效的，账本是tamper-proof的，账本完全公开，任何人可见，\\(PAY^{r+1}\\)里记录的payment是自从\\(PAY^r\\)出现后所有的payment。理想系统里，区块是固定时间段生成的。
4. Algorand也是公钥代表地址，一个用户可以多个公钥，每个公钥有自己的amount。（类似比特币UTXO模型）
5. Current Status:从账本追溯得到
6. 安全和隐私：数字签名保证没法伪造交易，payment中，公钥和amount没有被隐藏起来，但是有一部分敏感信息通过hash被隐藏起来，
### 5.3 基本术语

**Keys,Users,Owners**: User和key绑定，二者同义

**Permissionless** : 公钥可以随时被添加进系统，用户可以有多个公钥。

Algorand中的每一个物品，都有唯一的表示。

**Clock**假设有一个任何人可读到的全局时钟，实际上，假设每个密钥读取自己的时钟的时间差别最大不超过，比如从全局时钟的一秒钟。

**Rounds**:r=0,1,...。使用上标来表示round，如果Q为非数字值（如Q是一个字符串、公钥、集合、数字签名等），表示某轮的Q时，采用\\(Q^r\\)表示，只有当Q是一个真正的数字（而不是一个可解释为数字的二进制串），写为\\(Q^{(r)}\\).

r>0,所有公钥集合为\\(PK^r\\),系统状态为![](https://i.imgur.com/8FvQAGg.gif).

\\(a_i^{(r)}\\)表示公钥i的可用余额，请注意，\\(PK^r\\)可以从\\(S^r\\)中推导出来，\\(S^r\\)也可以为每个公钥i指定其他组件。(??)

第0轮，\\(PK^0\\)是initial public keys，\\(S^0\\)是initial status，整个系统都知道\\(PK^0\\)和\\(S^0\\)。

**Payments**：用户不断产生payment（并按照5.7所述进行传播），一个用户i的一笔payment：\\(\wp\\)，表示为![](https://i.imgur.com/0kXAMKr.png)

**Paysets**：第r轮的payset \\(P\\)是第r轮的payment集合，每一个 \\(P\\)里的payment都是有效的，所有轮的payset表示为\\(\mathbb{P}\mathbb{A}\mathbb{Y}\\)\\((r)\\),payset是第r轮最大的payset

**Official Paysets**:每一轮，Algorand选择出一个payset（可能为空）\\(PAY^r\\),是本轮的*official payset(官方payset)*。在一个理想系统（如比特币中）

1. 唯一让一个新用户j加入到系统中的方式，是让他成为一个official payset\\(PAY^r\\)中某笔交易的接收方
2. \\(PAY^r\\)决定了下一轮的系统状态\\(S^{r+1}\\)
3. the set of public keys of round r+1 \\(PK^{r+1}\\)，包括了\\(PK^r\\)和第一次出现在 \\(PAY^r\\)的payment的接收方（payee）

总之，\\(S^{r+1}\\)可以从交易历史推导出来.

**每一轮的目标：1、得到新一轮的系统状态 ； 2、挑选每一轮的official payset**

### 5.4 Blocks and Proven Blocks

在\\(Algorand_0\\)中，第r轮的区块\\(B^r\\)：1、\\(PAY^r\\)代表第r轮的payments集合 ； 2、a quantity \\(Q^r\\),后面进行解释；3、 前一区块的hash \\(H(B^{r-1})\\)

![](https://i.imgur.com/uk3LO3T.png)

在Algorand中，一个区块的真实性由"block certificate"\\(CERT^r\\)决定，\\(\overline{B^r}\\)表示**a proven block**;

Megic Ledger 由一系列proven block组成$$\overline{B^1}，\overline{B^2},...$$

**Discussion**:\\(CERT^r\\)包含了\\(H(B^r)\\)的数字签名集合（验证者集合\\(SV^r\\)中大多数verifiers的签名，还有这些验证者属于验证者集合的证据），\\(CERT^r\\)没有包含在区块中.

**Each Algorand block \\(B^i\\) is final and safely**

### 5.5 Acceptable Failure Probability(可接受的失误概率)

可以接受的失误概率（如验证者集合并不是honest majority）   
$$F=10^{-12}$$

**Discussion**:F小于万亿分之一；10^-12不是Adversary伪造诚实用户的签名的失误率，成功伪造一笔经过签名的交易的概率远小于10^-12，近似为0。 可以忍受分叉可能性为10^-12，一分钟一轮的情况下，Algorand区块链的分叉概率大概为190万年一次

### The Adversarial Model

**Adversary**：是一个有效的（多项式时间）算法，在任何时间同化他想要破坏的用户（不超过可破坏用户数量的上限）. Adversary完全控制和协调所有恶意用户，可以代表所有恶意用户，比如代表恶意用户发生和接收消息，让恶意用户做出不符合协议的行为，可以孤立某个节点不让其收发消息。没有人能知道某个用户i是恶意的，尽管由于Adversary影响i做出了恶意的行为。

Adversary没有无限的计算力，无法伪造任意诚实用户的数字签名（非常小概率事件）；不能以任何方式干扰诚实用户之间的消息交换。

Adversary攻击诚实用户的能力受限于以下假设中的一个：

**Honesty Majority of Money:**对每个非负整数k和实数\\(h>1/2\\),$$HHM_k > h$$每一轮的诚实用户在r>k时都拥有系统中超过h的总额。

**Discussion**假设有一个强大的Adversary可以perfectly coordinate所有恶意的用户是很难实现的，假设Adversary可以secretly、dynamically、immediately corrupt 用户也是悲观的，因为完全控制某个用户需要花费时间。\\(HHM_k > h\\),例如如果一轮平均1分钟，k=120时大多数的钱会被诚实的一方控制至少2小时，k=10000时，至少一周

### 5.7 The Communication Model 通信模型

1. **Abstracting Away Message Propagation抽象消息传播**：任意\\(\rho  \in \left ( 0,1 \right ]  ,\mu \in \mathbb{Z} _+ \\),存在\\(\lambda\\),若一个诚实用户在时间\\(t\\)广播\\(\mu\\)-字节的消息\\(m\\)，那么在时刻\\(t+\lambda\\),至少有\\(\rho\\)比例的诚实用户收到了消息\\(m\\)

仅有上述这个特性是不够的，不仅需要足够多的用户及时收到第r轮的消息，还需要了解之前的区块，否则无法确认\\(B^r\\)里交易的有效性，因此需要足够多的人接收一系列消息去了解\\(B^{r-1}\\)

2. **Message Propagation (MP) Assumption**诚实用户所占比\\(h \in \left ( 0.5,1 \right ] \\),用户收到比例（reachability） \\(\rho \in \left ( 0,1 \right ] \\),消息长度\\(\mu \in \mathbb{Z} _+ \\),存在一个latency常量\\(\lambda\\)，\\(for j=1,2,...\\)一个诚实用户j在时刻\\(j\lambda\\) 广播\\(\mu\\)字节的消息![](https://i.imgur.com/Ib3t578.gif),对于任何整数\\(k>1\\)，到时间\\(k\lambda\\) ,至少有\\(\rho\\)比例的诚实用户收到了消息![](https://i.imgur.com/Z5j637y.gif)

> 引用原文：even in the presence of a very powerful adversary, to implement a public ledger,the **real bottleneck** for implementing a public ledger is **message latency**, not computation.

**An Adversarial Approach**如果不违反MP的假设，Adversary完全控制所有消息的传递。 特别是在没有被诚实用户注意的情况下，对手可以随意决定哪位玩家收到哪个消息，并且随意加速发送他想要的任何消息。

### 5.8 Discussion: The Worst That Can Happen 最坏情况

- 分叉可能性太低，若真的发生，将冻结作恶人的所有资金，使得作恶成本太高
- 若Algorand的假设都成立，最坏状况：选举出来的leader\\(l^r\\)是恶意且不理智的（不顾忌所有激励），强制将\\(PAY^r\\)设为空，且不留下他作恶的证据。由于每轮的leader是随机、独立的选择出来的，下一个leader是诚实的概率至少为1/2，在第r轮没有被包含进去的交易，会在r+1等轮里重新被提交和处理。
- 若Algorand的假设不成立，最坏状况：选出来的验证者大多数都是恶意的：分叉产生，但是恶意的验证者可能将暴露自己 
- BA协议中，如果恶意的是大多数，也只能包含有效的交易集合 。**By totally controlling the BA protocol,the Adversary may, at most, choose which legitimate payments will go through.**


### 5.9 Alternative Block Structures（替代块结构）

**blocktree**:efficient prove the content of an individual past block,without having to exhibit the entire blockchain. eg：证明某笔交易属于某个区块，可以通过blocktree快速验证。


## 6 Intuition （直觉?

本节主要针对\\(Algorand_0\\),是一个简化版更理想化的协议，有许多强假设，与最终的Algorand'的底层逻辑结构一致，在第8章强假设会被移除。

### 6.1 暂时性假设

1. Long Corruption Time ：Adversary需要至少40轮去破坏(corrupt)诚实用户，如果恶意节点在第r轮破坏善意节点i，则i在第r+40轮之前一直保持为善意的
2. Honest Majority of **Users**.在permissioned（不能动态增删节点）的系统中更有用，每个人投票权重一致。后面会用Honest Majority of **Money**替换，在permissioned和permissionless（公钥可以随时被添加进系统，用户可以有多个公钥）中均有用。
3. No Sybil Attacks:作恶节点不能向系统中引入恶意用户，但是这个假设不能保证系统中原始节点被恶意节点同化。
4. Full Reachability:在时间π内所有传播的消息抵达所有用户.这个假设当然是错误的，除非π是无限大。
5. Continual Participation:Honest users run the Algorand protocol in the background，除非他暂时与网络断开连接，否则会持续的收发消息。后面会改成lazy honesty

### 6.2 Objectives 目标

第r轮开始于\\(r.\pi\\),结束于\\((r+1).\pi\\),产生payset \\(PAY^r\\).在时刻\\(r.\pi\\)，每个用户广播他收到的第r轮的payment，到\\((r+1).\pi\\)时刻所有用户都收到了所有在广播的payment（即为\\(PAY^r\\)）。满足以下两个特性

1. 完美的正确性，所有诚实节点都同意\\(PAY^r\\)
2. 完整性，\\(PAY^r\\)包括所有诚实节点在第r轮的payments

恶意用户试图破坏上述2个特性。但是他不能伪造任意一个善意用户第r轮的payment，因为无法伪造数字签名。恶意用户可以广播自己的无效payment，但是善意节点不会认可这笔交易；他最多只能愚弄一部分用户，用户收到恶意用户的一笔合法交易。或者是恶意用户产生两个不同的payment，广播出去，这两个交易不会都有效，而是仅有一个有效（payment的总额超过了恶意用户的账户余额）

h代表诚实用户的百分比，h>1/2，Algorand的目标是：保证正确性和完整性h。Guaranteeing, with overwhelming probability, perfect correctness and completeness h.（即所有诚实节点都同意\\(PAY^r\\)，\\(PAY^r\\)包含的payment的比例诚实节点所有payment的h（大于50%））

**正确性的优先级高于完整性**，若上一轮的payment没有被包含在\\(PAY^r\\)里，在第r+1轮里也照样可以包括进\\(PAY^{r+1}\\)。但是如果正确性没有得到保证，则会发生分叉。

**Reliance on selected verifiers**:最重要的一点就是**verifier selection **。selected verifiers / round-r verifiers有能力决定第r轮的\\(PAY^r\\)。Algorand指出了其他重要的方面，如何保证正确的信息抵达selected verifiers、验证者做出决策的过程、如何和系统中all users交流

Algorand会从大集合\\(PK^r\\)中不断挑选出小的验证者集合\\(SV^r\\)





**如何从众多节点中随机选出部分节点作为验证者节点，十分重要**：

1. 如果全节点大多数（如超过80%)的节点是诚实节点，则选出的验证者节点也是大多数为诚实节点
2. 选出的验证者节点直到最后一刻前，都是无法预测的
3. 选出验证者集合的这个过程，全部节点都是清楚如何做的，第r轮的验证者集合至少要在r轮马上开始之前被知情
4. 验证者选择过程不能太慢。
Algorand的选择过程不是交互式的，并且计算速度是有效的

###6.3 理想化的 **Alogorand0**协议

**verifier selection**:\\(R\\)是一个足够长的字符串，在第r轮的时候随机、独立的挑选出来，可以被all users立马知晓，通过下面的步骤，挑选出\\(SV^r\\)

![](https://i.imgur.com/MlilPVr.gif)是按照字母排序的\\(PK^r\\)的公钥，在\\(Algorand_0\\)的**One-key-per-owner**的假设下，每个公钥代表一个用户。![](https://i.imgur.com/k05SOmh.gif)bits足以唯一标识\\(PK^r\\)中的一个密钥,![](https://i.imgur.com/k05SOmh.gif)的首位和\\(R\\)的第一个字符一致的即为\\(SV^r\\)的第一个验证者（如果所有公钥的第一位字符都大于\\(R\\)的首字符，丢弃这一位（丢的是R？）则考虑下一个![](https://i.imgur.com/k05SOmh.gif)位）。与\\(R\\)的接下来一位相等则为挑选出的第二个验证者（if different from the one already chosen, or else these bits, too, are discarded？？）直到\\(SV^r\\)的所有n个验证者都被选择出来

这样不仅选择出n个验证者，还对他们指定了顺序，但是谁来选择\\(R\\)呢？ **no one.**字符串\\(R\\)是由hash函数产生的，hash函数的输入是与第r轮相关的值。

1. 最简单的想法，hash函数的输入为r（即当前的轮数） \\(H(r)\\)为256位的随机字符串。但是这样易于预测到后续的验证者集合都有谁，在第0轮就可以计算出后续轮数的验证者
2. 为了保证不可预测性，选择\\(H(Q^{r-1})\\),\\(Q^{r-1}\\)是只有第r-1轮才能得到的一个值，是区块\\(B^{r-1}\\)的一部分。选择出一个合适的\\(Q^r\\)
	1. 想法1：\\(H(Q^{r-1})\\) -> \\(H(PAY^{r-1})\\).缺点：恶意用户会从这种选择机制中获益，如果恶意用户是第r-1轮的leader，他能选出\\(PAY^{r-1}\\),他可以精心挑选出一个payment，使得下一轮的leader还是一个恶意节点（提前计算hash，找到一个恰好的payment，使得下一轮的leader还是恶意的）。
	2. 更安全的选择方法见8.4节

**基本的Round结构**：在第r轮的开始，所有用户都知道：系统状态\\(S^r\\)、其他用户的公钥\\(PK^r\\)、（8.4节再具体讨论的）\\(Q^{r-1}\\)、\\(SV^r\\)。

第r轮的目标：第r轮的验证者们，选择official payset \\(PAY^r\\)、计算 \\(Q^r\\)。然后，每个用户独立计算新的系统状态\\(S^{r+1}\\)（根据\\(S^r\\)和\\(PAY^r\\)）,计算第r+1轮的验证者集合\\(SV^{r+1}\\)(根据\\(Q^r\\)),根据\\(PK^r\\)和\\(PAY^r\\)计算出\\(PAY^{r+1}\\)

用户发起一笔交易并广播，等待这笔payment成为某一轮的official payset。假设诚实用户的比例为\\(h\\)，在0时刻开始计算轮，则至少应该在\\(\Lambda_h\\)时刻广播到所有诚实节点，\\(\pi\\)时刻时，保证所有用户收到第r轮诚实用户发出的payment。恶意用户为了在诚实用户之间产生异议，他们的传播的支付可以被一些但不是全部的诚实用户接收。

若所有的验证者都构造payment，可能会产生不一致的结果，因此采用Byzantine Agreement协议,去解决上述问题。

**Byzantine Agreement**

协议被多个players运行，每个players都知道彼此，并且可以交换消息。players可以是善意的或恶意的。

协议\\(P\\)是一个\\((n,t)-BA\\)协议，共n个players，其中至多有t个是恶意的，每个player i有一个初始值\\(v_i\\)，每个诚实节点都是可以百分百halt的（暂停？），输出\\(out_i\\).满足以下两个条件

1. Agreement :有一个值 v（可能为空值\\(\bot\\)）,对所有的诚实节点i来说\\(out_i = v\\)，v是协议\\(P\\)的全局输出
2. Consistency:有一个值v，若对所有诚实节点i来说\\(v_i=v\\)，则\\(out_i = v\\)

BA：BA协议需要players进行密切沟通，每一步都有节点需要发送信息\\(m_i\\)给所有其他的players。认为巨大的通信量不会成为问题，有以下3个原因：

1. 不是所有节点都运行BA协议，会选择出一部分作为验证者，只有验证者节点需要运行BA协议
2. 设计了一个新的加密BA协议，比已知的BA协议要快
3. Algorand应用中，节点间通信通过广播消息，新的BA协议在几秒内即可执行，每个区块只运行一次BA协议。几秒即可产生一个即时生效的块，没有分叉的困扰，是可以接受的。

**Choosing and Using the BA Protocol**：诚实节点所占比例为h，1.选择n和t（\\(t<n\\)）,随机选择出n个验证者，为\\(SV^r\\),验证者集合中超过t个节点为恶意的概率小于\\(F=10^{-12}\\) ->即为\\((n,t)-BA\\)协议

第r轮，在验证者集合被选择好之后，每个验证者i都收集他自己收到的第r轮的payment，为第r轮的payset \\(PAY_{i}^{r}\\).paysets可能会不同，\\(SV^r\\)中的验证者们要运行BA协议，每个验证者的初始值为 \\(PAY_{i}^{r}\\)。有很大的可能性，验证者集合中至多t个是恶意的，诚实的验证者会达成一个共识output，out或者为第r轮的payset，或者是空值\\(\bot\\),不论out是二者中的哪个，每个诚实的验证者都会对out进行签名并广播，因此out会被至少n-t个\\(SV^r\\)里的验证者签名。 如果out是第r轮的payset，则out为official payset->\\(PAY^r\\)；否则out为空。

**A Completeness Problem**：运行BA协议后，第r轮的验证者会输出同样的值out，这个值很可能是空值\\(\bot\\)，当诚实节点的初始值不同的时候，最终的输出将会是\\(\bot\\)，因此，仅有一个恶意的验证者都会使得初始值\\(PAY_{i}^{r}\\)不同，使得最终的out为空。

**Round leader**:为避免上述问题，每一轮需要从\\(PK^r\\)中随机挑选一个leader\\(l^r\\),将\\(SV^r\\)的第一个验证者作为leader

![](https://i.imgur.com/MWbCcuV.png)

leader的作用：当所有用户广播第r轮的payments后，\\(l^r\\)组织自己的交易列表\\(PAY_{l^r}^{r}\\),如果\\(l^r\\)是诚实的，则\\(PAY_{l^r}^{r}\\)将包括所有leader收到的有效的第r轮的payments。当组织\\(PAY_{l^r}^{r}\\)完毕后，\\(l^r\\)签名后广播区块

![](https://i.imgur.com/oVxYAj3.png)

所有第r轮的验证者把leader提出的区块，作为initial values、

**The new block && proved block**：如果leader\\(l^r\\)是诚实的，则BA协议的输出一定是leader提出的区块\\(B_{l^r}^{r}\\)。若输出是空值\\(\bot\\)，也是有效的第r轮的区块，只是payset为空而已，被解释为\\((r,\varnothing,Q^{r-1},H(B^{r-1}))\\)。不论是否为空，每个诚实的验证者都会对\\(OUT\\)进行签名并广播

> OUT可能会很长，若有一个用户，帮忙进行消息的转发，若之前已经广播过![](https://i.imgur.com/HGQ4hMK.gif)(i属于验证者集合)给邻居，当user又收到其他人（i不同于j，都属于\\(SV^r\\)）签名的out消息![](https://i.imgur.com/W7tA3Cp.gif)，可以只发送\\(SIG_j(PAY^r)\\)给邻居

至少n个第r轮验证者中的**v**个对\\(B^r\\)签名，则所有user将会认可这个区块。**proven block**为\\((B^r,CERT^r)\\).\\(CERT^r\\)是**v**个签名者的数字签名。

总结：当本轮的leader是诚实的，新区块一定是他提出的，新区块的payset一定包括所有诚实用户的payments。

**Ephemeral Keys**：\\(Algorand_0\\)在第6.1节的强假设下，在第r轮的执行中，Adversary作恶的概率小于\\(F = 10^{-12}\\)

同样需要保证在过去的一段时间不能分叉。（We would like, however, also to guarantee that the Adversary cannot generate a fork in a past round, if during that round he did not control a sufficient percentage of the users ）采用如下策略：在第r轮的每一个步骤对消息签名都使用不同的公钥，密钥只会使用一次，私钥会立刻被销毁。

**5个步骤**：

1. 第r轮的验证者和leader都是根据第r-1轮的相关量\\(Q^{r-1}\\)从全节点\\(PK^r\\)中选出来的，有大概率保证验证者集合中大多数节点是可信的，大概率保证leader是诚实节点
2. leader维护着一个pair（x,y）,x代表他选出来并签名的第r轮有效的交易，y是leader对\\(Q^{r-1}\\)的签名
3. 选择出来的验证者节点运行BA协议，所有诚实节点达成一致 \\(OUT^r\\)（如果leader是诚实的，则\\(OUT^r\\)与leader发出的消息对一致）
4. 所有的honest节点对\\(OUT^r\\)签名，可以根据\\(OUT^r\\)得到\\(PAY^r\\) 和\\(Q^r\\) 
5. All verifiers and the leader propagate payments transferring all the money (in their verifier keys) to no new keys, and then delete the old secret keys.

\\(PAY^r\\)是第r轮有效payment的集合，当leader是诚实的时，Algorand保证perfect正确性和一定程度的完整性

## 7. New Byzantine Agreement Protocols
Byzantine Agreement协议时用在有恶意节点的同步网络中的，参与方被称为players

### 7.1 同步网络和Matching Adversaries
同步网络有一个同步时钟

Honest player：follow his prescribed instructions.
所有的恶意player可以被强大的恶意组织(Adversary)统筹管理（eg：Adversary可以立刻收到发给恶意节点的消息、可以选择发送malicious消息）。Adversary可以在任何时刻攻击善意节点，但是不能超过恶意节点个数的上限，但是Adversary不能干涉已经由诚实节点发送出去的消息。Adversary可以即刻看到当前诚实节点发送的消息，也可以选择（分辨？）出来哪些是恶意节点发送的消息

Adversary在时钟r看到诚实节点i发送的消息后，可以从网络中抹去这个消息，将i节点变成一个恶意节点，选择发送变成恶意节点的i新组织好的message，Adversary可以传输这个新的message （as usual）

### 7.2 BA的概念

**定义7.1**：同步网络，协议\\(P\\)是一个\\(n-player\\) protocal，所有参与的节点都知道彼此，t是一个正整数\\(n \geq 2t+1\\)。\\(P\\)是一个arbitrary-value（respectively,binary）(n,t)-BA协议。系统中players至多t个是恶意的，每一个players的initial value\\(v_i\\)不为空，每个诚实的节点最终会得到一致的![](https://i.imgur.com/dCtZWsp.gif)。至少\\(\sigma\\)可能性(\\(\sigma \in (0,1)\\))，下述两个条件成立:

1. Agreement:存在![](https://i.imgur.com/dCtZWsp.gif) 对所有诚实的players i，都有\\(out_i = out\\);
2. consistency:对所有诚实的players，有\\(v_i=v\\)，则\\(out=v\\)

\\(out\\)即为\\(P\\)的输出，\\(out_i\\)即为player i的输出

### 7.3 The BA notation 

在本BA协议中，player需要计算在指定步骤，有多少个players发送给他了指定消息

![](https://i.imgur.com/SYbr8Sz.png)

i在第s步时收到的（j发来的）消息v的数量

### 7.4 二进制BA协议 BBA

新的binaryBA协议：

* 超过2/3的players是诚实的
* 十分快：k次重复执行一个单一的步骤，每个步骤有1/3的概率达成一致
* 基于数字签名：每个player有自己的公钥
* 需要一个随机的字符串r（在Algorand中，用block quantity \\(Q^r\\)代替），与player的公钥独立不相干。

> t是恶意player的个数上界，r是common random string，x是二进制表示的整数（可能有前导0），\\(lsb(x)\\)表示x的最低有效位

![](https://i.imgur.com/nAT1d9i.png)

For s=1 to m Do:
(Propagation传播？广播？)Step s. 每个player i都发送![bi](https://i.imgur.com/GNSI6Qc.gif)和![sig r s](https://i.imgur.com/eztSEZe.gif)给all player（包括自己）

- **s.1** 如果![](https://i.imgur.com/olSJHJQ.png),player i 设置 \\(b_i=0\\)
- **s.2** 如果![](https://i.imgur.com/0mQqne3.png)，player i设置 \\(b_i=1\\)
- **s.3** i收到player j发送的![](https://i.imgur.com/8krYL4n.gif)，\\(S_i\\)是j的集合，i设置![](https://i.imgur.com/gyF285G.png)(i对阶段s进行签名，并且计算hash，取出S_i小的位，取最低有效位，即为b_i应设置的值)

**定理7.1**如果\\(n \geq 3t+1\\),BBA 是一个\\(binary(n,t)-BA\\)协议，可靠性![](https://i.imgur.com/cVcREug.png)

证明：

1. 首先证明，在step s开始时还没达成共识，等s结束时达成共识的概率是\\(\frac{n-t}{2n}\\)

哈希值是随机的，因此![](https://i.imgur.com/VHwAC74.gif)都是独立、随机的256 bit长的字符串。选出来hash值中最小的一个（字典序排序），表示为![](https://i.imgur.com/5AYK3Gu.gif) ，m是一个诚实player的概率至少为2/3.因此，在step s刚开始时，m发送![](https://i.imgur.com/5ee2mSf.gif)给所有player。定义：![](https://i.imgur.com/JnKfYkZ.png)

1. c是一个随机值
2. 在step s，c会被所有诚实的节点计算

注意，一个诚实的节点，在BBA协议的每一个step的开始，都会发送同样的bit给all players.由于系统中至多有t个恶意节点，在step s要结束时，对所有诚实的节点i和j收到的b来说![](https://i.imgur.com/j3Paa4E.png) 有以下4种情况：

1. 所有诚实的节点i都根据**s.1 /s.2**更新bi：若根据s.1，则达成共识bi=0
2. 所有诚实的节点i都根据**s.3**来更新bi：达成共识bi=c
3. 一部分诚实节点设置\\(b_i=0\\)，其他节点设置等于c：H1表示根据s.1更新的诚实节点集合，H3表示根据s.3更新的诚实节点集合.若c=0，且是被诚实节点设置的，则所有的H1集合里的节点重置bi=0,H3中的节点也设置bi=0。c=0的概率为1/2，是诚实节点的概率为\\(\frac{n-t}{n}\\),因此达成共识为0的概率是\\(\frac{n-t}{2n}\\)
4. 一部分诚实节点设置\\(b_i=1\\)，其他节点设置等于c：同上

在step x<m时，b的值可能有多个，但在x+1 step的结尾，会达成一个共识b值。在step x+1的开始，所有的诚实节点都广播b，当i节点收到超过2t+1个相同的b值时，i节点会在step结束时设置自己的bi=b。b值在每个步骤结束时，都会达成一致。使用BBA协议，在协议的最终都会remain in agreement.(remian n agreement with probability :![](https://i.imgur.com/z2tZd0p.png)

### 7.5 The "Graded-Consensus" Protocol GC 梯度共识？

**定义7.2** P协议，all players彼此知道，公开，每个player i自己知道一个arbitrary initial value \\(v_{i}^{'}\\)

n个player，至多t个是恶意的，每个诚实的节点i halt在一个value-grade对![](https://i.imgur.com/BrHuqFl.gif)上,gi取值为0,1,2.满足以下条件，说P是一个\\((n,t)-GC\\)协议：

1. 对所有的诚实节点i和j，有![](https://i.imgur.com/iuP9u2W.png)
2. 对所有的诚实节点i和j，![](https://i.imgur.com/cGSYTca.png)
3. 如果![](https://i.imgur.com/WyQZybx.png)，则对所有诚实节点i来说，vi=v,gi=2

使用的是two-step的GC协议，源自于gradecast的后两个阶段（原文有引）

![](https://i.imgur.com/HUZHpKG.png)

- **STEP 2** 每个节点i发送\\(v_{i}^{'}\\)给all players
- **STEP 3**  当![](https://i.imgur.com/y3ZuDJQ.png)每个节点i给所有的player发送字符串x
- **Output Determination**每个player i输出的值对(vi,gi)按照如下方法计算：
	![](https://i.imgur.com/nLNpsmh.png)

**定理7.2** 当\\(n \geq 3t+1\\)是，GC是一个\\((n,t)-Graded Broadcast\\)协议

**如果诚实节点输出(x,2),则至少t+1个诚实节点的初始值\\(v_{i}^{'}\\)=x**

> x这个字符串到底是什么值？GC这一步有什么用 ？ 解释：在区块链场景，初始值v'是区块哈希，如果收到超过2t+1个对相同的区块哈希投票的消息，则vi=区块哈希，gi=2

## 8 Algorand' 的结构和区块构造

- 第r轮的构造：如何从验证者集合挑选机制中挑出来第r轮的leader->leader-selection 机制
- 如何决定quantity -> \\(Q^r\\)   （ **\\(Q^r\\)决定了第r+1轮的leader和第r+1轮每一步的验证者集合**，\\(Q^{r-1}\\)决定了第r轮的leader和第r轮每一个步骤s的验证者
- 临时密钥的使用（ephemeral keys）
- 区块的构造和BBA协议已经在前面讲过 


### 8.1 概念性的Round Structure
Algorand’，第r轮有**3+m**个步骤

1. **STEP 1** ：leader据他本身收到的第r轮的Payment，构建并广播区块B（区块B的payset \\(PAY ^r\\), is a maximal one for round r.）
2. **STEP 2 AND 3** ：用户执行**two-step graded-consensus protocal** 。用户设置他的初始value 为他从leader处收到的区块值，如果用户收到了一个有效的区块，GC协议的最终结果是每个用户输出一个value-grade pair(vi,gi). 如果leader是诚实的，每一个诚实的user i会输出（output）（vi = B(区块)，gi = 2）
3. **THE NEXT m STEPS**：用户执行**m-step的binary BA协议**，
	1. 用户i的初始bit bi = 0，如果Graded-consensus的输出gi = 2;
	2. 若![gi！=2,则bi=1](https://i.imgur.com/ENopzpJ.gif)
	3. **BBA协议的终点**:如果output为0（bi=0?），诚实节点设置新的区块即为STEP3结束时输出的pair里的vi，vi即为新的区块。

Algorand'的第r轮，用户通过达成Byzantine agreement协议，确认这个区块是本轮的leader提出的，然后才是接受leader提出的区块作为下一个区块\\(B^r\\). 挑战是：上述步骤，不是全部用户参与，而是选择出来的验证者集合执行。 

### 8.2 Verifier Selection
 
根据前几轮的users（如前r-k轮）独立的挑选验证者集合，s：每个step，r代表round \\(SV^{r,s}\\)。

**SVS**（secretive verifier selection mechanism）:让每个用户知道自己是否属于\\(SV^{r,s}\\)，并给他证明的方式。定义了Algorand'使用的SVS机制，\\(SVS^{-k,p}\\)。通过quantity \\(Q^r\\)决定验证者集合，唯一的和第r-1轮联系起来。
![verifier selection](https://i.imgur.com/DdL2bPE.png)

备注:k是一个正整数（look-back parameter,代表向前参考k个区块），p是一个[0,1]区块内的实数（代表概率），第r轮的一个步骤s的验证者集合如公式所示（with look-back parameter k and probability p）
举例说明：取k=100，概率p为0.7:则选择第r-100轮时的all user集合，当时所有的用户都用自己的签名签(r,s,Q^r-1),然后对签名结果进行hash，如节点i的hash结果为23456...(256bit)，则0.23456<=p,i节点被选择成为验证者。i成为了\\(SV^{r,s}\\)中的验证者之一，\\(SIG_i(r,s,Q^{r-1})\\)为i的r-s credential（凭证）,压缩表示为\\(\sigma _{i}^{r,s}\\)

**定理8.1** 对所有的k,p,r,s和用户i

1. \\(PK^{r-k}\\)的用户有p比例成为验证者（如p=0.3，则有30%的r-k轮时的user成为第r轮的验证者）
2. 用户i可以清楚的知道，他是否属于验证者集合
3. 每一个\\(H(\sigma _{i}^{r,s})\\)对i来说是独立、随机且易于计算的，没有i的私钥，没法伪造出i的结果。

证明：

1. 数字签名的独特属性保证了签名是唯一的，.H(SIG)也是唯一的，hash函数的计算方式决定了，每一个bit都是随机独立的从[0,1]选择的，所以小于等于p的概率为p
2. 给定一个短的字符串，用户可以轻易的对其签名。
3. 可以从签名推出来r,s等值，但是没法逆向伪造一个SIG，因为私钥难以被暴力破解

### 8.3 Leader Selection

Algorand'不保证每一轮有唯一的一个leader，也不保证当leader唯一时，他能意识到自己是唯一的leader。只保证用户可以秘密的知道自己是潜在的leader（potential leader）,当潜在的leader的集合不为空时，隐含的存在一个唯一的leader.

在Algorand'里，第r轮潜在的leader同样是用\\(Q^{r-1}\\)去决定的。回忆一下验证者i属于\\(SV^{r,s}\\)的证据是\\(SIG_i(Q^{r-1})\\)，被简单表示为\\(\sigma _{i}^{r,s}\\)
![](https://i.imgur.com/tJ8FX7i.png)

**定义8.2** 一个用户 \\(i\in PK^{r-k}\\)

- 如果![](https://i.imgur.com/yOZRPTQ.png),则用户i是第r轮的一个潜在leader
- 并且，如果![](https://i.imgur.com/0LTLvci.png)（即i的.H是所有潜在leader里最小的，则i是第r轮真正的leader 

如果没有potential leader，则第r轮时没有leader的。否则，就有唯一的leader。leader的凭证为![](https://i.imgur.com/cxsjWti.gif),被压缩表示为![](https://i.imgur.com/goLrevJ.gif) 或者简写为\\(\tau^r\\)

尽管\\(SV^{r,1}\\)的验证者被定义好了，但是Algorand并没有赋予他们任何义务。然而，对第r轮的潜在的leader来说，他们其实是step1的验证者，要去验证potential leader的凭证\\(\tau^r\\)，找到最小的（确认谁才是unique leader）

**定理8.2** 在Algorand'里，对于所有的round r、look-back parameter k、probability p来说(h是每一轮诚实user所占比例)

1. \\(l^r\\)存在的概率约等于\\(1-\frac{1}{e}\\)
2. \\(l^r\\)存在时，他是随机从\\(PK^{r-k}\\)里选出来的
3. \\(l^r\\)存在且是诚实用户的概率约等于\\(h(1-\frac{1}{e})\\)

> ?? e是什么

### 8.4 The Quantity \\(Q^r\\)

如果在计算\\(SV^{r,s}\\)时没有加入\\(Q^{r-1}\\)，恶意节点能通过精心设计第r-k-1轮的payment，在第r-k轮时引入一个user i，这个user i的\\(x=H(SIG_i(r,s))\\),\\(.x \leq p\\), user i属于\\(PK^{r-k}\\),在k轮之后，这个user i可以成为第k轮的验证者。

**挑选\\(Q^r\\)的必要条件**：  quantity \\(Q^r\\) 应该满足以下要求：

- R1：\\(Q^r\\) 唯一由r（round）决定，在r轮结束的时候被广泛知悉
- R2：\\(Q^r\\) 是一个（short）随机、独立选择出来的值
- R3：\\(Q^r\\) 在r轮结束之前，只有leader知道\\(Q^r\\)

**Intuition**为了满足R1，计划将\\(Q^r\\)包含在区块\\(B^r\\)里，\\(Q^r\\)不取决于step，Adversary不能在一轮的中间引入一个新用户，原因很简单，用户进入系统只能是作为包含在一轮的final official payment里的收款人，在本轮结束的时候才真正确定.

为了满足R2，设计\\(Q^r\\)为一个理想哈希函数H的输出（特意选择过input），设置\\(Q^r=H(r)\\)能同样满足R1和R2，但是无法满足R3的需求，每个人都能predict Q是什么。

为了满足这3点，似乎有难度，下面是一个比较weak的实现版本。

**定义** 在区块\\(B^r\\)里，\\(Q^r\\)的取值规则如下。\\(Q^0\\)是一个随机数，写进系统里，完全公开.\\(\tau_{l^r}^{r}\\)是第r轮leader的credential

![](https://i.imgur.com/0Se8N1Z.png)

以\\(Q^1\\)为例:

1. 如果\\(l^1\\)存在且及时发出了credential（证明自己是leader），并且\\(SIG_{l^1}(Q^0)\\)，则![](https://i.imgur.com/Vsmjb36.gif)
2. 否则 \\(Q^1=H(Q^0,0)\\)

**定理8.3** 对于每一轮r来说，20字节的字符串\\(Q^r\\)是随机且独立被\\(l^r\\)选出来的，不可预测.

证明：首先假设每一轮都有一个leader，无论是恶意与否的，都计算\\(Q^r\\)并及时暴露自己的credential和\\(SIG_{l^r}(Q^{r-1})\\).

![](https://i.imgur.com/4Hd5Yf6.png)

\\(SIG_{l^r}\\) (\\(Q^{r-1}\\))唯一由\\(Q^{r-1}\\)决定,即使leader是存在且是恶意的，他也不能伪造\\(Q^{r-1}\\)的一系列有效签名，他没法创造一个对自己有利的\\(Q^r\\)(??)

同时，产生\\(Q^r\\)的输入不可能与\\(Q^j\\)的输入一样，因为产生\\(Q^r\\)的第二个参数是r-1，会不断增加，因此\\(Q^r\\)本身是随机、独立选择出来的256bit的串。（无论恶意的leader如何去选择自己的公钥对\\(Q^{r-1}\\)进行签名）

\\(l^r\\)**存在且为诚实节点**的概率是\\(\frac{h}{e}\\)（前提：每一轮有一个leader，**leader是善意的概率是h**,是\\(PK^{r-k}\\)中的一员）。诚实leader不会在第r轮结束之前泄露\\(SIG_{l^r}(Q^{r-1})\\),总结出，一个恶意用户能在r-1轮预测到\\(Q^r\\)的概率至多为1-h.

因此，恶意节点在第r轮的时候，预测到\\(Q^{r+k}\\)的概率不会大于\\((1-h)^k\\)。预测成功的情况有如下两种可能：1.预测到一个256位的hash值（不太可能，难度太高）；2. 或者 所有k个leader都是恶意的（第r+1轮到第r+k轮的leader都是恶意的）-> 概率为\\((1-h)^k\\)。针对第2中可能，Adversary在第r轮的时候，设计构造第\\(Q^{r+k}\\)的值。（??）

为了确保恶意节点构造出\\(Q^{r+k}\\)的可能性小于\\(F\\)，必须选择一个**足够大的k**（k:look-back parameter，在**SVS验证者选择机制**中使用的k，\\(SVS^{-k,p}\\)）。因此在第r-k轮，尽管Adversary可以引入新的user(新的user是payment中的接收方payee),但是他是盲目的，想要使得引入的新user成为leader，还是很难的。

之前都假设每一轮都有一个leader，并且leader会及时表明成为leader的证明和![](https://i.imgur.com/Baj3Jcr.gif)，如果第r轮没有leader，或这个leader是恶意的，或者拒绝向其他人出示![](https://i.imgur.com/Baj3Jcr.gif)和自己是leader的证据，避免暴露自己的行径，如果遇到上述问题，即**没有leader**并且**在特定时间内一直没有收到**\\(SIG_{l^r}(Q^{r-1}\\)) ，则将\\(Q^r = H(Q^{r-1},r-1)\\),避免系统阻塞。下面分析一下，如果这样设置后面会发生什么：

恶意的leader \\(l^r\\)可能的行为：

- 制造一个空的payset\\(PAY^r\\)（无视系统所有的激励和惩罚措施）
- 隐藏需要的\\(SIG_{l^r}(Q^{r-1})\\)
- 替换\\(Q^{r-1}\\)

因此恶意leader可以影响到下一轮验证者集合\\(SV^{r+1}\\)的决策和下一个leader的挑选\\(l^{r+1}\\) ,可能会影响到下一个leader，让\\(l^{r+1}\\)也成为恶意的。但是验证者集合和leader本身是随机挑选的，新leader存在且为诚实的概率是\\(\frac{h^2}{e}\\)

**结论：**当look-back parameter k足够大时，Adversary有很小的概率影响到下一轮的leader的决策。即\\(Q^r\\)是随机并且独立的挑选出来的

> 如果第r轮的leader\\(l^r\\)是诚实的，他会在第r轮的合适时间发出 ![](https://i.imgur.com/Baj3Jcr.gif)，但是不会提前发，因此![](https://i.imgur.com/inaBOCz.gif) 不仅是random的，且直到leader暴露![](https://i.imgur.com/Baj3Jcr.gif)之前，\\(Q^r\\)都是不可预测的

诚实的leader保证\\(PAY^r\\)包含所有leader自己观察到的第r轮的payment。**The verifier-selection mechanism \\(SVS^{-k,p}\\) has completeness at least \\(\frac {h^2}{e}\\)**.例如，若\\(h>0.9\\),completeness约为0.3.（保证30%的payment被包含在第r轮的payset中）

第r轮的leader 如何 “reveals” ![](https://i.imgur.com/Baj3Jcr.gif)。通过广播这个方式不行（恶意的leader可以很晚才发出![](https://i.imgur.com/Baj3Jcr.gif)，导致只有部分诚实节点看到了![](https://i.imgur.com/Baj3Jcr.gif)，诚实的节点们收到的消息不同，导致\\(Q^{r+1}\\)、下一轮的验证者集合和leader不同。因此决定采用**Byzantine Agreement**协议（同样采用决定\\(PAY^r\\)的那个BA协议去reveal ![](https://i.imgur.com/Baj3Jcr.gif)

> 当验证者集合\\(SV^r\\)和leader\\(l^r\\)被选择好，\\(l^r\\)会构造payset，leader会发出消息对![](https://i.imgur.com/YhCYzvz.png)，第r轮的验证者集合会开始BA协议，所有诚实的验证者 \\(i\epsilon SV^r\\) 签名并广播结果\\(OUT\\)（是pair），如果足够多的验证者都签名了相同的\\(OUT\\)消息，则认为达成了共识


 * if \\(OUT\\)消息对的第二个元素是![](https://i.imgur.com/Baj3Jcr.gif)，则all users都设置![](https://i.imgur.com/4Hd5Yf6.png)
 * else，当\\(OUT\\)为空时\\(Q^r = H(Q^{r-1},r-1)\\)

**注：BA协议用来决定\\(PAY^r\\)和\\(SIG_{l^r}(Q^{r-1})\\)**

-----------------------------------------------------------------
### 8.5 Ephemeral Key （临时密钥）

在Algorand'，数字签名有很多作用：如认证交易、认证在协议中广播的消息，后一种用法十分重要。在广播的网络里，用户必须对他发出的消息进行签名，否则没有别的方式证明这个消息是自己发出的。

限制key owner i的\\(pk_i\\) 只被用来

1. authenticating i's **payments**
2. generating i's **credentials** 
3. authenticating i's **ephemeral keys**

**ephemeral keys**:当用户属于\\(SV^{r,s}\\)时，他用临时密钥签协议中需要他发出的message

特别的，在第r轮的s阶段，用户i使用它的签名密钥\\(pk_i\\):

1. 检查他自己是否属于验证者集合\\(SV^{r,s}\\)，如果属于则计算相应的credential： ![\sigma_i^{r,s}](https://i.imgur.com/Kfz6od7.gif)
2. 认证![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)，他用这个密钥只签名了一个消息，![m_i^{r,s}](https://i.imgur.com/NY7bntf.gif)，这个消息是他作为验证者集合中的一员，按协议需要发送的，他用\\(pk_i\\)证明这个只用了一次的私钥确实是自己产生的。

当一个诚实节点成为\\(SV^{r,s}\\)的一员

- 首先，他计算![sig_i^{r,s}](https://i.imgur.com/a7qV3Er.gif)-> 利用![](https://i.imgur.com/w7yg7iX.gif)签![](https://i.imgur.com/NY7bntf.gif)消息
- destroys 私钥\\(sk_i^{r,s}\\)
- 广播![\sigma_i^{r,s}](https://i.imgur.com/Kfz6od7.gif),![sig_i^{r,s}](https://i.imgur.com/a7qV3Er.gif)和![](https://i.imgur.com/bEsFVH2.gif).   ![](https://i.imgur.com/bEsFVH2.gif)为一个字符串，认证![](https://i.imgur.com/w7yg7iX.gif)确实是i在第r轮第s步时使用（用来签署第r轮第s步的消息）的公钥。

![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)不仅是临时的，还只用一次。可以防止Adversary进行ex post，可能会corrupt 验证者集合中的大多数节点。Adversary不能阻止验证者集合的节点发出的消息（with credential）被传播，但是他可以立刻corrupt验证者节点，并要求验证者节点发出（立刻或在将来某时刻）发出一个矛盾的消息（with credential），这样会导致系统的不一致。一次一密可以解决上述问题。（因为私钥在消息发出前就被destroy了，所以没办法再产生同一个签名的不同message）

![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)不仅是一个i从来没有用过的公钥，还必须保证在第r轮第s步，i没有另一个与这个阶段绑定的密钥。

**A New Status Quantity **：对第r轮的每一个user，状态\\(S^r\\)和一个量息息相关：i在第r轮时的余额。下面会定义另一个量![](https://i.imgur.com/P1bjpEK.gif)

R是一个固定且足够大的正整数，每一轮有m步，\\(cR\\)轮是指在![](https://i.imgur.com/N2mtTVC.gif)，c是非负数。用户i创造一个20字节长的![c_i^{cR}](https://i.imgur.com/b8zxjfR.gif).r轮的每一步，都会产生一对公私钥，![](https://i.imgur.com/b8zxjfR.gif)是(r,s)到![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)的一个vector。如果i是第r轮第s步的验证者，i用![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)签名![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif),\\(C^{cR}\\)可以证明在![](https://i.imgur.com/YgNJWyP.gif)的第s步，确实有相应的![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif)存在，可以产生存在性的proof。

当前轮接近\\((c+1)R\\)时，i安装同样方式产生下一个commitment![](https://i.imgur.com/7c1g7JI.gif),计算并广播![](https://i.imgur.com/khclx5c.gif)，这个签名也被当做i的一笔payment，放在\\(PAY^r\\)里（如果round leader是诚实的），会使得![](https://i.imgur.com/P1bjpEK.gif)更新。

**总结:\\(COM_i(r)\\)包含的值或者为\\((cR,C^{cR})\\)，或者是![](https://i.imgur.com/lOcQFHH.gif)**

**Implementation Details and Efficiency** 如果look-back parameter 为k，则quantity ![](https://i.imgur.com/ulgHIDA.gif)成为i某轮的status至少是在\\((c+1)R\\)（？？）

有多种实现commitment\\(C^{cR}\\)的方法，如Merkel tree：

1. 构建一个树，叶子对应的值为可能的（r,s）pair
2. store public key ![pk_i^{r,s}](https://i.imgur.com/w7yg7iX.gif) in leaf (r,s)
3. Merkle 化这棵树
4. ![c_i^{cR}](https://i.imgur.com/b8zxjfR.gif)是root的值

## 9 Protocol Algorand' with Majority of *Honest Users*

本节Algorand'的版本基于下述假设：

**Honest Majority of Users Assumption**:\\(PK^r\\)中，超过2/3的users是诚实的

使用了BA协议，BA协议有要求超过2/3的players是诚实的，因此在Algorand中，诚实players的比例为\\(h>2/3\\)

**Parameter Choices**

1. ？？诚实user的比例 \\(h>1/2\\)，是先验的
2. 可接收的失败率: \\(F=10^{-12}\\)
3. 为了保证\\(Q^r\\)在第r-k轮时是不可预测出来的，look back parameters: \\(k \geq 100\\)
4. selection probability: \\(p\\)
5. reachability: \\(\rho\\)
6. 验证者集合中恶意players的上限：\\(t\\)
7. 概率大于\\(1-F\\)，在round \\(r\\),step \\(s\\)有下式成立：\\(\rho(n-t)>2t+1\\) 其中，\\(n\\)是验证者集合中实际的user个数，\\(t\\)是在第\\(s\\)步执行时，验证者集合中恶意的user的个数。[第r轮第s步的消息，\\(\rho\\)比例（eg 被80%）的诚实用户收到，这些比例的用户数量 >2t+1  （大概率事件）]
8. 延迟参数：
	1. \\(\Lambda\\):取决于区块大小的限制（即：step-1的message的大小）
	2. \\(\lambda\\)：取决于step-s的消息最长字节数(s>1)

**Tight Scheduling 严格的时长**Algorand'实际包括3+m step，每一步都有精确的耗时。Step 1的时长是\\(\Lambda\\)，这一步涉及到区块的广播。其他steps的时长都是\\(\lambda\\),因此每一轮耗时:\\(T=\Lambda+(2+m)\lambda\\)

实际上，Algorand'每一轮都是严格按时间执行的（tightly scheduled），每一轮的每一步开始于![a_{r,s}](https://i.imgur.com/cEzphFQ.gif)，结束于![b_{r,s}](https://i.imgur.com/hVJADBD.gif).

假设第一轮的执行开始于时刻\\(T\\),第r轮的第一步开始于![a_{r,1}=rT](https://i.imgur.com/sDlIZYW.gif)，结束于![b_{r,1}=rT+\Lambda](https://i.imgur.com/cMzYqUE.gif)，其他步骤s开始于时间![a_{r,s}=rT+\Lambda+(s-1)\lambda](https://i.imgur.com/YWP4pR7.gif),结束于![b_{r,s}=rT+\Lambda+s\lambda](https://i.imgur.com/IMbJ6MD.gif)

![](https://i.imgur.com/SgK57mo.png)

**Remark**： 因为每个区块\\(B^j\\)的certificate \\(CERT^j\\)都包括第r轮最后一个step的至少\\(d\\)个验证者，和他们的凭证（credentials）。因为\\(Algorand'\\)是tightly scheduled的，user可以基于自己的时钟判断自己是否属于\\(know^r\\)

\\(Know^r\\)包括\\(\rho\\)比例的\\(SV^{r,s}\\)验证者，这些验证者可以计算系统状态\\(S^r\\)和quantity \\(Q^r\\)

**Forwarding Instructions**：a user \\(u\\)不会forward user \\(i\\)的一个\\(round-r-step-s\\)的消息\\(m\\)，**除非**：

1. 根据\\(u\\)自己的时钟，在时间间隔![](https://i.imgur.com/MbIM7jl.gif)内收到了\\(m\\)
2. \\(m\\)包含\\(i\\)的credential，证明\\(i\\)第r轮第s步的验证者
3. \\(u\\)第一次收到\\(m\\)
4. \\(u\\)已经forward过\\(i\\)的另一个\\(round-r-step-s\\) message

**存储** 每一个user \\(i\\)保存收到的所有的第r轮的消息，直到计算出\\(B^r\\)才删除。

### 9.1 Step 1

>  \\(Know^r\\) is the set of all users who know the entire block chain so far,\\(B^0,...B^{r-1}\\) : 是一部分user，他们存储所有历史区块 

如果![i \in Know^r](https://i.imgur.com/FvJ7N3o.gif)是第r轮的potential leader,则他计算:

1. 基于\\(i\\)收到的payments,构造一个最大的第r轮payset ![PAY_i^r](https://i.imgur.com/FeNBjKL.gif)
2. 构造候选区块 ![](https://i.imgur.com/r7KyVIo.gif)
3. ![sig_{pk_i^{r,1}}(H(B_i^r))](https://i.imgur.com/U48EWWS.gif):用临时公钥![](https://i.imgur.com/971TpSl.gif)签名候选区块，并进行hash

然后，i destroy临时私钥![sk_i^{r,1}](https://i.imgur.com/ubRgShZ.gif),广播message ![m_i^{r,1}](https://i.imgur.com/F3QBZrD.gif):

![m1](https://i.imgur.com/dS2uTim.png)

![m_i^{r,1}=(\tau_i^r,pk_i^{r,1},auth(pk_i^{r,1}),B_i^r,sig_{pk_i^{r,1}}(H(B_i^r)))](https://i.imgur.com/cIj2vm9.gif)

## 9.2 Step 2

如果![i \in SV^{r,2} \cap Know^r](https://i.imgur.com/YSu3xcF.gif)，i计算:


1. \\(L_i^r\\)是一系列potential leader，\\(i\\)收到了他们发送的第一轮消息![m_{l_i^r,i}^{r,1}](https://i.imgur.com/Ld6QRep.gif)
2. ![l_i^r](https://i.imgur.com/7eXITUZ.gif)是\\(L_i^r\\)按字典序排序的第一个
3. ![v_i^{'}=H(B)](https://i.imgur.com/g1fkOAw.gif),如果B是第\\(r\\)轮的区块，并且![sig_{pk_{l_i^r}^{r,1}}(H(B))](https://i.imgur.com/dSNJhug.gif)是![](https://i.imgur.com/Ld6QRep.gif)的\\(e\\)字段(是leader里排第一个的true leader发送的第一步的消息，![l_i^r](https://i.imgur.com/7eXITUZ.gif)构造的候选区块)。如果potential leader为空，则![v_i^{'}=H(B_{\varepsilon }^r)](https://i.imgur.com/wFrv5qh.gif)
4. ![](https://i.imgur.com/LlFhXtq.gif) 实际为用i的临时密钥的私钥签名H(B)

> 注意：\\(L_i^r\\)是第r轮all potential leader的子集，但是可能会fail，有以下几种原因：1)恶意的potential leader选择不发送自己的credentials、不发有效的候选区块等等字段.2) 第r轮的诚实的potential leader可能不属于\\(know^r\\)，他们缺乏一些数据去构造候选区块 . 3)第r轮的诚实节点发送了自己的credentials，但是消息没有及时达到i节点。
> 
\\(i\\) destroy 临时私钥，广播message ![m_i^{r,2}](https://i.imgur.com/6lMo8uv.gif)

![](https://i.imgur.com/SXtJVWz.png)

![m_i^{r,2}=(\sigma_i^{r,2},pk_i^{r,2},auth(pk_i^{r,2}),v_i^{'},sig_{pk_i^{r,2}}(v_i^{'}),\left \langle \tau_{l_i^r}^r,pk_{l_i^r}^{r,1},auth(pk_{l_i^r}^{r,1}),sig_{pk_{l_i^r}^{r,1}}(H(B_{l_i^r}^r)) \right \rangle)](https://i.imgur.com/4hDQcMQ.gif)

\\(\left \langle  \right \rangle\\)括起来的部分为![l_i^r](https://i.imgur.com/7eXITUZ.gif)在第一步发送给i的消息![](https://i.imgur.com/Ld6QRep.gif)的a、b、c、e部分。

**Remark**:

- Step 2对应的是**GC**协议的Step 2（见7.5节），intial value ![v_i^{'}=H(B)](https://i.imgur.com/g1fkOAw.gif)(而非\\(v_i=B\\)),为了效率，区块(minute)的传播时延远大于一个控制消息(second)的时延.但是这会使得最终达成共识的是一个hash后的区块（如果不为空的话），得讨论确保最终区块\\(B^r\\)总是易于计算的
- 因此在[m_i^{r,2}](https://i.imgur.com/6lMo8uv.gif)中包括了![m_{l_i^r,i}^{r,1}](https://i.imgur.com/Ld6QRep.gif)的\\(a,b,c,e\\)部分，验证者\\(i\\)防止leader ![l_i^r](https://i.imgur.com/7eXITUZ.gif)是恶意的，在网络中用合法的message在网络中洪泛。只要确保有credential，恶意的potential leader可以在第一步中创造多个想要发送的消息并广播，尽管诚实节点只会forward其中的一条。


### 9.3 Step 3

如果![i \in   Know^r \cap SV^{r,3}](https://i.imgur.com/bi31Uyd.gif)并且，对\\(x\\),  ![\#_i^2(x)\geq 2t+1](https://i.imgur.com/kE6jXuQ.gif)（\\(i\\)收到大于等于2t+1个相同的第2轮消息），则\\(i\\)用自己的临时密钥![pk_i^{r,3}](https://i.imgur.com/OSP0KdP.gif)计算![SIG_{pk_i^{r,3}}(x)](https://i.imgur.com/PjlixV7.gif).然后，\\(i\\) destroy临时私钥，广播\\(m_i^{r,3}\\)消息

![](https://i.imgur.com/VMiDykZ.png)

![m_i^{r,3}=(\sigma_i^{r,3},pk_i^{r,3},auth(pk_i^{r,3}),SIG_{pk_i^{r,3}}(x))](https://i.imgur.com/p8Alt6x.gif)

**For all other verifiers**:All other ![j \in   Know^r \cap SV^r](https://i.imgur.com/F1xV2ob.gif),对收到的20-Byte的\\(x\\)计算![\#_i^2(x)](https://i.imgur.com/z0bTUMO.gif)

**Remark** 这一步对应**GC的step 3**

### 9.4 Step 4

若![i \in   Know^r \cap SV^{r,4}](https://i.imgur.com/ZpoXP7O.gif)，则节点i计算 \\(v_i\\) ，![g_i \in \left \{ 0,1,2 \right \}](https://i.imgur.com/s4caZW7.gif), \\(b_i\\) 为0或1：

![](https://i.imgur.com/WEUo8px.png)

然后i destroy本轮的临时密钥，广播\\(m_i^{r,4}\\)

![](https://i.imgur.com/6S6uSSy.png)

![m_i^{r,4}=(\sigma_i^{r,4},pk_i^{r,4},auth(pk_i^{r,4}),SIG_{pk_i^{r,4}}(b_i))](https://i.imgur.com/lb6uPSO.gif)

**For all other verifiers**All other ![j \in   Know^r \cap SV^r](https://i.imgur.com/F1xV2ob.gif)根据同样的1-4 step，计算他们的![v_j,g_j,b_j](https://i.imgur.com/qR4maOv.gif)

**Remark**这一步和下面的m-1步对应BBA协议，initial bits bi代表了GC协议的执行是否以grade=2结束。

### 9.5 Step s=5 through 2+m（应该都是重复这个步骤，直到达成共识，BBA协议由3步组成，会不断循环，依概率结束 5<= s <=m+2

若![i \in   Know^r \cap SV^{r,s}](https://i.imgur.com/1iYcbFb.gif)，则i按下述计算并对bi进行签名

![](https://i.imgur.com/pdHUmGW.png)

![](https://i.imgur.com/NC8zKbR.png)

#### 9.5.1 Step 3+m (Block-Signing Step，final step)

![](https://i.imgur.com/Vo9b4Eo.png)

**Remark**all users in \\(Know^r\\)，收到了从step 1到step 3+m的消息，在第3+m步快结束的时候，计算official new block \\(B^r\\)和certificate \\(CERT^r\\).在reconstructing出区块后，链接在\\(B^{r-1}\\)后面，这些user会保存第r轮从第一步到第3+m步收到的所有message。

> 理解：第一步是potential leader打包区块，第2、3步是GC协议，GC协议的第一步（实际是step 2），是通过收到的一堆potential leader的消息，计算谁才是真正的那个leader（![](https://i.imgur.com/0LTLvci.png)最小的.H），将真正的leader发来的第一轮消息里的区块进行hash，即为GC协议的initial value，GC协议是在对leader和区块的双重共识，如果收到的initial value大于等于2t+1，则将initial value进行签名。广播
> 后面进行m轮BBA协议，是为了对区块的共识，BBA协议的输入是GC协议的输出，输出是（v，g）值对，按照相应的输出，决定bi的初始值是什么，接下来做m-1轮的BBA协议，如果收到大于2t+1个 0，则区块不为空，否则区块为空。BBA协议的输出即为区块哈希

### 9.6 Reconstruction of the Round-r Block

![i \in Know^r](https://i.imgur.com/FvJ7N3o.gif)，for some x,区块B和user Set \\(X\\)

- x是一个20字节的值
- B是第r轮的区块，或者为空，或者为step 1收到的消息的一部分，H(B)=x
- X是第r轮3+m个step的验证者集合的子集，至少为2t+1

![](https://i.imgur.com/MOw643a.png)


### 9.7 快速分析

第r轮leader存在且为诚实的概率是h(1-1/e),在考虑到消息传播时的assumption（在固定时间内，能传播给\\(\rho\\)比例的user），leader存在、为诚实的并且属于\\(Know^r\\)的概率至少为\\(\rho h(1-1/e)\\)。在时刻rT，leader计算自己的credential，意识到自己是第r轮的potential leader，基于知道rT时刻自己收到的payment，构建了区块\\(B_{l^r}^r\\)，在rT时刻leader发出message。

\\(B_{l^r}^r\\)有\\(1-10^{-12}\\)这么大的概率为official block，无论Adversary做什么。

1. 假设在rT时刻，Adversary立刻收到了step-1的消息，马上意识到\\(l^r\\)是第r轮唯一的leader，他能做的只是corrupt leader，但是没法阻止message的传播。在时刻\\(rT+\lambda\\)，根据传播时延的assumption，有十分大的概率，至少有2t+1个Step-2诚实的验证者收到了所有potential leader发来的消息，诚实的验证者中的全节点（属于\\(Know^r\\),知道\\(Q^{r-1}\\)的值），可以得到谁是第r轮唯一的leader。all honest Step-2 验证者节点们会验证并在时刻\\(rT+\Lambda\\)广播v'
2. 到时刻\\(rT+\Lambda+\lambda\\)，Step-3诚实的验证者节点有\\(\rho\\)属于\\(Know^r\\)->2t+1，至少2t+1诚实的Step-3 Verifiers会验证v'。
3. 到\\(rT+\Lambda+2\lambda\\)，消息会被至少2t+1的Step-4 honest verifiers收到，每个验证者计算![](https://i.imgur.com/eLBFD78.png)
4. 接下来是m-step的BBA协议的（a strange execution）执行。BBA协议的initial bits bi的值与gi相关，gi=2则bi=0.之所以说执行是"strange"的，因为每一步的player set是完全不同的，player set是随机挑选出来的。这些集合的基数也不一定相等，但是集合中的user大于2/3是诚实的。
5. BBA协议支持player-interchangeable。BBA协议不依赖于individual players（?）

### 9.8 Discussion

\\(Algorand'\\)两种应用场景依赖于"(足够普遍的随机选择
)sufficiently common random selection":

1. Step1 选择出potential round leader
2. step s=5~2+m中的每一步，都选择出一部分的验证者节点。

保证：

1. each of at most **a handful** of users is called to propagate a long message, or
2. each of at most **a few hundred** users is called to propagate a short message.


### 9.9 Continual Praticipation to Lazy Honesty

Algorand'选择的look-back parameter至少为100，下面介绍如何通过选择足够大的k来去掉Continual Praticipation假设。

Continual Praticipation假设能保证BA协议honest majority。如果大量的诚实节点离线，是十分危险的，Honest Majority of Users->实际上的user是"currently active"的，可能会有目前在线的user大多数是恶意的情况。

通过“激励”解决上述问题。

**Lazy Honesty**：一个user i是lazy-but-honest的，如果

1. 当他被要求参与协议时，他完全按照协议要求工作
2. 他被要求加入协议的频率是很低的，如一周一次，参加协议会收到相应的奖励。

为了允许lazy honsety,look-back parameter k必须足够大。一周大约是10000分钟，假设一轮约为1分钟（平均），在第r轮，user i希望知道在 接下来的一周，他会不会成为验证者，他已经知道了\\(Q^{r-10000},...Q^r\\)，m在1~10000之间，如果满足下式，i会成为第r+m轮的验证者之一：

![](https://i.imgur.com/f0Lnfwl.png)

计算接下来的10000轮，耗时大约是10s，如果他没有被选为验证者，他可以离线。如果被选为这些round的verifier，他必须时刻准备（接收message），act as a honest verifier。


## 10 Algorand with Honest Majority of Money

用Honest Majority of Money代替Honest Majority of Users假设。基本思想是（基于POS的）：在挑选i是否成为第r轮第s步的验证者节点的时候，考虑权重——user i在r-100（如果look-back parameter k=100）轮的时候，一共有多少钱

**Simplest Implementation**:每个key至多有一个单位(unit)的money,随机从\\(PK^{r-100}\\)里挑出来n个user，n个user的钱总额都为1(unit)

**The Next Simplest Implementation**:每个公钥有一个fixed的最大总额为M，M是一个key是否属于验证者集合的概率。一个key \\(i \in PK^{r-100}\\)，在第r轮时余额为\\(a_i^{r}\\),满足下式，则i被挑选属于验证者集合：

![hmm](https://i.imgur.com/0wUkQXM.png)

**一个更复杂的实现**

系统中更富有的参与者有更多的keys

user i有k+1个copies（i,v）（这个k不是look-back parameter，而是copy份数的一个参数），每一个copy都可以独立的被选择为验证者，k取决于第r轮i拥有的总余额。

**A more general quantity \\(H_i^{r}\\)**:\\(H_i^{r}\\)

**Copy的份数**：n是预想的验证者数量，\\(a_j^{(r)}\\)是user j在第r轮时拥有的总钱数，\\(A^r\\)是r-100轮的all user在“第r轮”时拥有的总钱数，![](https://i.imgur.com/qsLfTKk.png)。

如果i属于\\(PK^{r-100}\\)且属于\\(Know^r\\)，i的copy为(i,1),...(i,k+1),其中，![](https://i.imgur.com/X6faeDp.png)。

举例：n=1000，\\(A^r=1o^9\\),\\(a_i^{(r)}=3.7millions\\),则k=3，共有4份copies。

**Verifiers && Credentials**如果![](https://i.imgur.com/PU4GyBV.gif)且共有k+1个copies。

对每个v=1,...k（v的取值范围[1,k+1]），copy(i,v)都自动属于\\(SV^{r,s}\\)。广播消息的时候，会广播相应的公钥及其auth。

对于copy(i,k+1)，第r轮的每一步，k+1这个user i都要检查是否满足下式：![](https://i.imgur.com/z9F1jiR.png)。如果满足，copy(i,k+1)属于\\(SV^{r,s}\\)。为了证明，i需要广播credential：![](https://i.imgur.com/Hpy94mA.png)。

例如：n=1000，\\(A^r=1o^9\\),\\(a_i^{(r)}=3.7millions\\),则k=3，共有4份copies。则前3个copies自动属于\\(SV^{r,s}\\)，第4个，类比扔硬币（硬币是不规则的），头像面朝上的概率为0.7，如果头像面朝上，则(i,4)被选择成为验证者

**Business as Usual**：解释了验证者如何被选择、credential如何计算，其他执行规则都与之前一致。

## 一些思考

根据google

1. 都会提到Algorand是一个具有**可扩展性**的共识算法
2. Micali凭借其在伪随机数和零知识证明方面的成绩，名声大噪，(是zk-SNARKS的基础。
3. cryptographic sortition 随机选择players去创造块
4. 不会分叉
5. 原始的BA协议：两个特性.1.如果player以同一个值开始，则最终他们会agree on that value 。2.如果players以不同的值开始，所有诚实的players会agree on one value。 （在区块链中，value是候选区块和leader？（原文写players））。原始BA协议的问题，players的交互需要轮，能支持的user数目有限。
6. Player replaceability :
7. 区块大小：1MB，real latency 被区块在网络中的传播速度影响，因此先设置成1MB，如果未来网络变得更快，可以增加区块大小。