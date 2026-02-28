# 加密货币到底是怎么运作的：一本被忽略的技术手册

> 原书：*How Crypto Actually Works: The Missing Manual* by Larry Cermak
>
> 合著：Igor Igamberdiev / Bohdan Pavlov (Wintermute) | 审校：Wintermute Research, The Block Research | 编辑：Tim Copeland

---

## 这本书是什么

一本 **9 万字、15 章**的开源技术书，从 Bitcoin 的 UTXO 模型一路讲到量子抗性密码学。不是白皮书的堆砌，不是交易教程，不是项目软文——而是一份试图回答"这些东西到底怎么跑起来的"的工程手册。

作者 Larry Cermak 曾任 The Block 研究总监。他发现市面上找不到一本既全面又诚实的 Crypto 技术读物：学术论文太抽象，YouTube 太水，研究报告分散在付费墙后面，大多数书出版时已经过时。这本书就是为了填这个空缺。

**当前状态：** 第一版定稿前的社区审校阶段。全文开源，最终将以免费电子书形式发布。

---

## 你能从这里学到什么

读完这本书，你会建立起一套**从底层到应用层的完整认知框架**：

| 层次 | 你将理解的内容 |
|------|-------------|
| **密码学基础** | 椭圆曲线签名 (ECDSA/EdDSA)、哈希函数、公私钥体系、助记词与 HD 钱包推导 |
| **共识机制** | PoW / PoS / PoH / BFT 各自的工作原理、安全模型与取舍 |
| **链架构** | UTXO vs 账户模型、EVM 执行原理、Solana 的并行执行、L1 间的设计差异 |
| **扩容方案** | Rollup（Optimistic / ZK）、数据可用性 (EIP-4844)、闪电网络、跨链桥 |
| **DeFi 机制** | AMM 常数乘积公式、借贷清算逻辑、无常损失、收益聚合、预言机依赖 |
| **市场微观结构** | 交易所撮合引擎、订单簿、做市商运作、永续合约与清算、价格发现 |
| **MEV** | 三明治攻击、Flashbots 架构、Builder 中心化危机、跨域 MEV |
| **资产托管** | 硬件钱包、多签、机构托管架构、密钥管理的威胁模型 |
| **稳定币与 RWA** | USDC/USDT 的储备机制、链上国债代币化、合规与风险 |
| **治理与代币经济** | DAO 治理机制、代币分配设计、激励对齐、时间锁与升级 |
| **前沿话题** | 量子计算对区块链的威胁、后量子密码学迁移、DePIN、预测市场 |

**一句话总结：** 认真读完，你对 Crypto 技术栈的理解会超过大多数全职从业者。

---

## 章节导读

### 序言：[为什么这些东西值得了解](Chapters/_preface.md)

Crypto 是一个**对抗性极强的环境**——没有客服、没有回滚、犯错即损失。序言讨论了主权自治与便利性的权衡、传统金融系统的审查风险、无许可系统的双刃剑效应，以及如何在噪音中辨别信号。

### 第 1 章：[Bitcoin 全面解析](Chapters/ch01_bitcoin.md)

从创世区块和密码朋克运动讲起。覆盖 PoW 挖矿机制（哈希、nonce、难度调整、ASIC、矿池）、中本聪共识与链重组、货币政策与减半、UTXO 模型、Bitcoin Script、四种地址类型（Legacy/P2SH/SegWit/Taproot）、交易费市场（RBF/CPFP）、隐私模型、SegWit 与 Taproot 升级，以及闪电网络和 Ordinals。

**核心收获：** 理解 Bitcoin 为什么是"数字黄金"的技术基础——从共识安全到货币政策的硬编码。

### 第 2 章：[Ethereum 生态](Chapters/ch02_ethereum.md)

EVM 作为可编程"世界计算机"的执行模型——栈式字节码、Gas 计量、EIP-1559 的基础费燃烧机制。账户模型（EOA vs 合约账户）、ERC-20 标准与可组合性、从 PoW 到 PoS 的合并、验证者与质押、Slot/Epoch 结构、Rollup 扩容方案（Optimistic vs ZK）、数据可用性 (EIP-4844)、账户抽象 (EIP-7702)、以及 Restaking。

**核心收获：** 理解以太坊为何成为 DeFi 和 L2 生态的结算层。

### 第 3 章：[Solana 生态](Chapters/ch03_solana.md)

并行执行架构、Account 模型与 Program Derived Addresses、跨程序调用 (CPI)、交易结构与 Ed25519 签名、本地费市场、Proof of History 时钟、Tower BFT 共识、Gulf Stream 交易转发、Turbine 数据传播、验证者经济学、Rust 开发栈与 Anchor 框架、速度与去中心化的取舍，以及 Memecoin 交易等高频场景。

**核心收获：** Solana 在性能上做了哪些工程取舍，这些取舍带来了什么样的应用场景。

### 第 4 章：[L1 区块链横向对比](Chapters/ch04_l1_blockchains.md)

跳出单条链的视角，横向比较不同 L1 的架构选择：共识与终局性（经济终局 vs 绝对终局）、虚拟机与编程模型、不可能三角的实践权衡、分片与 Rollup 等扩容路径、跨链互操作与桥的安全问题、以及网络效应的注意力竞争。

**核心收获：** 建立评估任何新 L1 链的分析框架。

### 第 5 章：[资产托管基础](Chapters/ch05_custody.md)

密钥即控制权。ECDSA/EdDSA 签名、BIP-39 助记词、BIP-32/44 层级确定性推导、25th word 密语。自托管方案（软件钱包的风险模型、硬件钱包的安全元件、气隙冷存储）到机构方案（多签钱包、门限签名、托管服务商）。

**核心收获：** 理解"Not your keys, not your coins"的完整技术含义。

### 第 6 章：[加密市场结构与交易](Chapters/ch06_market_structure.md)

全书最长的一章（12,000 字）。中心化交易所的撮合引擎架构、现货/永续/期权产品、订单类型与执行、做市商的角色与价差管理、保证金与清算机制、违约基金、价格发现过程、波动率分析，以及机构/企业的 Bitcoin 国库趋势。

**核心收获：** 理解 Crypto 市场"表面之下"的管道系统——价格是怎么形成的，流动性从哪来。

### 第 7 章：[DeFi](Chapters/ch07_defi.md)

无许可金融的核心理念与"Money Legos"的可组合性。AMM 机制的深度解析（Uniswap v1→v4 的演进、常数乘积公式 x*y=k、集中流动性、Hooks）、Curve 的 StableSwap 曲线、超额抵押借贷与清算、利率模型、无常损失、收益耕作与聚合，以及预言机等基础设施依赖。

**核心收获：** 理解 DeFi 协议之间如何像乐高一样组合，以及这种组合性带来的系统性风险。

### 第 8 章：[MEV（最大可提取价值）](Chapters/ch08_mev.md)

交易排序权如何变成一门生意。MEV 的类型（三明治攻击、清算抢跑、套利）、Flashbots 的 MEV-Boost 架构与加密内存池、Proposer-Builder 分离带来的中心化问题、跨域 MEV 的扩展。

**核心收获：** 理解区块链"暗森林"中的价值博弈。

### 第 9 章：[稳定币与 RWA](Chapters/ch09_stablecoins_rwas.md)

法币稳定币（USDC/USDT）的储备机制与链上稳定性、真实世界资产（RWA）的代币化——链上国债、固定收益产品的托管与抵押模型。

**核心收获：** 理解 Crypto 与传统金融之间的接口层。

### 第 10 章：[Hyperliquid](Chapters/ch10_hyperliquid.md)

作为 L1 链上永续合约 DEX 的崛起路径。HyperBFT 共识与 EVM 兼容性、HLP（混合流动性提供者）设计、治理代币经济学、去中心化路线图，以及新兴竞争者。

**核心收获：** 理解链上衍生品交易所如何挑战中心化交易所。

### 第 11 章：[NFT](Chapters/ch11_nfts.md)

数字所有权的技术基础。ERC-721/ERC-1155 标准、元数据存储与 IPFS 内容寻址、版税与创作者经济、市场交易动态与地板价。

**核心收获：** 剥离炒作后，NFT 的技术实质与适用场景。

### 第 12 章：[治理与代币经济学](Chapters/ch12_governance.md)

链上治理的机制设计——投票权重与质押加权、从 Discord 吵架到链上民主的成熟过程、代币分配与释放计划、激励对齐、多签/时间锁/升级的三支柱结构。

**核心收获：** 理解去中心化协议如何在"无老板"的条件下做决策。

### 第 13 章：[DePIN（去中心化物理基础设施）](Chapters/ch13_depin.md)

用代币激励真实世界资源供给的新模式。贡献证明机制、验证与信誉系统、数据/存储/计算/无线等细分方向（Filecoin、Arweave 等）、女巫攻击与经济可持续性的挑战。

**核心收获：** 理解区块链激励机制如何从纯数字世界扩展到物理基础设施。

### 第 14 章：[量子抗性](Chapters/ch14_quantum_resistance.md)

量子计算对当前密码学的威胁评估——Shor 算法对 ECDSA 的破解、地址复用的风险放大、后量子密码学标准（格密码等）、迁移挑战与时间线。

**核心收获：** 了解区块链面临的下一个根本性安全威胁及应对路径。

### 第 15 章：[预测市场](Chapters/ch15_prediction_markets.md)

概率定价的核心机制、去中心化的价值、Gnosis 和 Augur 的早期教训、Polymarket 的突破——技术架构、2024 大选带来的网络效应、信息市场的未来。

**核心收获：** 理解预测市场如何将"群体智慧"转化为概率信号。

---

## 阅读建议

- **从头读到尾：** 章节之间有递进关系，前 5 章是后续内容的基础
- **如果时间有限：** 第 1 章 (Bitcoin) → 第 2 章 (Ethereum) → 第 7 章 (DeFi) → 第 6 章 (市场结构) 是最核心的路径
- **详细目录：** 见 [table_of_contents.md](table_of_contents.md)，可以按小节精准跳转

---

## 关于本 Fork

本仓库 Fork 自 [lawmaster10/howcryptoworksbook](https://github.com/lawmaster10/howcryptoworksbook)，添加了中文导读说明。原书内容为英文，章节文件未做修改。

## 许可证

<a rel="license" href="https://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a>

本书采用 [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/) 许可证。个人贡献采用 CC-BY 许可。全书免费开源——基础教育不应该被锁在付费墙后面。
