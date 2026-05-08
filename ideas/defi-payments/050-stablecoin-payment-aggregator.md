# 稳定币支付聚合器 / Stablecoin Payment Aggregator

> 聚合多种稳定币支付，商户只需接受一种，自动路由兑换

## 解决什么问题

Sui 生态中有多种稳定币（USDC、USDT、 AUSD、BUCK 等），用户持有的稳定币种类与商户接受的种类往往不一致。用户需要手动在 DEX 兑换后才能支付，增加了操作步骤和滑点损失。稳定币支付聚合器让用户用任意稳定币支付，系统自动通过最优路径兑换为商户接受的稳定币，商户只需处理一种稳定币，用户体验无缝。

## 核心功能

- 多稳定币支付：用户可选择任意稳定币支付，系统自动处理兑换
- 最优路径选择：比较多个 DEX 的汇率，选择滑点最小的兑换路径
- 商户统一结算：无论用户用什么稳定币支付，商户收到的都是其指定的稳定币
- 自动流动性聚合：聚合各 DEX 的稳定币池流动性，确保大额支付也能低滑点完成

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行"扣款 → 路径选择 → 兑换 → 结算到商户" |
| Coin 标准 | 统一处理多种稳定币 |
| DeepBook 集成 | 通过 DEX 实现稳定币间兑换 |
| 动态字段 | 存储支持的稳定币列表、费率和兑换路径 |
| 事件 (Events) | 发出支付和兑换事件供对账使用 |

## 实现方案

### 智能合约 (Move)
- `payment_aggregator` 模块：统一入口处理稳定币支付请求
- `route_optimizer` 模块：比较各 DEX 汇率选择最优兑换路径
- `stablecoin_registry` 模块：注册和管理支持的稳定币列表
- `merchant_settlement` 模块：将兑换后的稳定币结算到商户地址
- 关键数据结构：`PaymentRoute { from_token: Type, to_token: Type, amount: u64, path: vector<SwapStep>, expected_output: u64, fee: u64 }`

### 前端 / 后端
- 技术栈：React + @mysten/dapp-kit + Sui TypeScript SDK
- 关键页面：Pay（支付界面）、Merchant Setup（商户配置）、Transaction History（交易历史）、Route Analytics（路由分析）
- 后端提供实时汇率查询和最优路径计算

## 技术架构

商户配置时选择接受的稳定币（如 USDC）和手续费率。用户在商户网站发起支付时选择自己的稳定币（如 USDT），后端实时查询各 DEX 的 USDT/USDC 汇率，route_optimizer 选择最优路径。用户确认后构建 PTB：从用户钱包扣除 USDT → 通过 DeepBook 最优路径兑换为 USDC → 扣除手续费 → 结算到商户地址。整个过程原子化完成，用户无需手动兑换。前端展示实际汇率和到账金额。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 聚合和路由逻辑需要多 DEX 适配 |
| 前端 | ⭐⭐⭐ | 支付界面需要展示多种稳定币选项 |
| 集成复杂度 | ⭐⭐⭐⭐ | 需要对接多个 DEX 获取实时汇率和流动性 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
