# 实时结算支付网络 / Real-Time Settlement Payment Network

> 面向商户的即时结算方案，交易确认即到账，无 T+2 延迟

## 解决什么问题

传统支付网络的结算周期为 T+1 到 T+2，商户需要等待 1-2 天才能收到资金，影响现金流。跨境支付结算周期更长，可能需要 5 天以上。实时结算支付网络利用 Sui 区块链的快速确认（亚秒级），让商户在交易确认后即时收到资金，无需等待传统结算周期，极大改善商户的现金流管理。

## 核心功能

- 即时结算：支付交易在区块确认后资金立即到账，延迟 < 1 秒
- 多渠道收款：支持线上 API、线下 QR 码和移动端 SDK 三种接入方式
- 自动资金路由：商户可设置自动将收入分配到不同用途（运营/税务/储备）
- 结算证明：每笔结算生成链上可验证的结算凭证

## 使用的 Sui 原语

| 原语 | 用途 |
|------|------|
| PTB | 原子化执行"支付 → 结算 → 路由分配 → 生成凭证" |
| Coin 标准 | 处理多种支付代币 |
| 对象所有权 | MerchantAccount 对象绑定商户配置 |
| 动态字段 | 存储路由规则、结算记录和凭证 |
| 事件 (Events) | 发出结算事件触发通知和对账 |

## 实现方案

### 智能合约 (Move)
- `payment_processor` 模块：处理支付请求并执行即时结算
- `fund_router` 模块：按商户配置的路由规则分配资金
- `settlement_proof` 模块：生成链上结算凭证
- `merchant_registry` 模块：管理商户注册和配置
- 关键数据结构：`SettlementRecord { merchant: address, payment_id: ID, amount: Coin<T>, routed_to: vector<RoutingDestination>, settled_at: u64, proof_hash: vector<u8> }`

### 前端 / 后端
- 技术栈：Next.js + @mysten/dapp-kit + Sui TypeScript SDK
- 关键页面：Merchant Dashboard（商户总览）、Settlement Records（结算记录）、Fund Routing（资金路由配置）、Integration Guide（接入指南）
- 后端提供 REST API 供商户系统集成和结算通知

## 技术架构

商户注册后获得 MerchantAccount 对象，配置接受的代币和资金路由规则。用户付款时通过 PTB 原子化执行：从用户钱包扣除代币 → 按路由规则分配资金（如 80% 运营账户、10% 税务账户、10% 储备）→ 生成 SettlementRecord 凭证。Sui 的快速确认确保结算延迟 < 1 秒。后端通过事件监听实时通知商户结算完成。商户可通过 API 查询历史结算记录和导出对账文件。

## 难度评估

| 维度 | 难度 | 说明 |
|------|------|------|
| Move 合约 | ⭐⭐⭐ | 支付处理和路由逻辑较直接 |
| 前端 | ⭐⭐⭐ | 商户管理后台需要丰富的功能 |
| 集成复杂度 | ⭐⭐⭐ | 需要多渠道接入和 API 支持 |

## 官方参赛要求检查

- [ ] 项目名称和描述
- [ ] 项目 Logo (1:1)
- [ ] 公开 GitHub 仓库
- [ ] Demo 视频 (≤5min)
- [ ] 测试网部署 + Package ID
