<div align="center">

# drain&apos;t

**AI security agent that stops wallet drainers — EIP-7702 delegations, Permit/Permit2 phishing, and malicious approvals — _before your funds move._**

_Wallet drain? Didn&apos;t happen._

[Live app](https://draint.vercel.app) · [Backend](https://api-draint.vercel.app) · [Snap on npm](https://www.npmjs.com/package/@draint/snap)

</div>

---

## The problem
Since Pectra, **~97%** of EIP-7702 delegations in the wild point at malicious sweeper contracts (the **CrimeEnjoyor** family) — **450k+** wallets drained. You sign once; your wallet becomes the attacker&apos;s. And if you have no ETH, you can&apos;t even defend yourself.

## How drain&apos;t works — detect → reason → act
- 👁️ **Detect** — a MetaMask Snap intercepts signatures & transactions in the wallet&apos;s main flow (Permit/Permit2 spenders, EIP-7702 delegation targets, malicious call targets).
- 🧠 **Reason** — a heuristic engine + **Venice AI** score each target and return human-readable reasoning.
- ✋ **Act** — an autonomous agent runs a **gasless rescue** via the **1Shot Permissionless Relayer** (fee in USDC, zero ETH). Verified live on Arbitrum One: [tx](https://arbiscan.io/tx/0x401874cc3404083dca0010684931604dd9a1a944129d8653646a3882e0f02d35).

## Repos
| Repo | What |
|---|---|
| [draint-sc](https://github.com/DraintAi/draint-sc) | Foundry contracts — `DraintCuratedTargetsEnforcer` (ICaveatEnforcer, MetaMask delegation framework v1.3.0), deployed + verified on Sepolia |
| [draint-be](https://github.com/DraintAi/draint-be) | Hono/Bun API — Venice AI classifier, autonomous agent, 1Shot gasless rescue ([SUBMISSION.md](https://github.com/DraintAi/draint-be/blob/main/SUBMISSION.md)) |
| [draint-fe](https://github.com/DraintAi/draint-fe) | Next.js app + MetaMask Snap (`@draint/snap`) |
| [draint-skills](https://github.com/DraintAi/draint-skills) | Composable Agent Skill for AI coding envs (Claude Code, Cursor) |

## Smart Accounts Kit Usage

### Advanced Permissions
Not used — drain't integrates via **Delegations (ERC-7710)**, not ERC-7715 Advanced Permissions.

### Delegations
- **Create delegation:** [draint-be/src/agent/oneshot7710.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/oneshot7710.ts) — `toMetaMaskSmartAccount` (Stateless-7702) + `createDelegation` (ERC-20 transfer scope) + `signDelegation` + `signAuthorization`
- **Redeem delegation:** [draint-be/src/agent/oneshot7710.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/oneshot7710.ts) — signed `permissionContext` submitted to the 1Shot relayer for redemption; on-chain caveat enforced by [draint-sc/src/DraintCuratedTargetsEnforcer.sol](https://github.com/DraintAi/draint-sc/blob/main/src/DraintCuratedTargetsEnforcer.sol) (ICaveatEnforcer, delegation framework v1.3.0, verified on Sepolia)
- Snap reads EIP-7702 delegation targets in the wallet's main flow: [draint-fe/snap/src/index.tsx](https://github.com/DraintAi/draint-fe/blob/main/snap/src/index.tsx) · [eip7702.ts](https://github.com/DraintAi/draint-fe/blob/main/snap/src/eip7702.ts)

### Redelegation
Not used — drain't uses a single-hop delegation (user → 1Shot redeemer).

### x402
Not used.

## 1Shot API Usage
- Full EIP-7710 gasless flow — `relayer_getCapabilities` → `relayer_getFeeData` → `relayer_estimate7710Transaction` → `relayer_send7710Transaction` → `relayer_getStatus`: [draint-be/src/agent/oneshot7710.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/oneshot7710.ts)
- Rescue routing / modes: [draint-be/src/agent/rescue.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/rescue.ts)
- Runnable CLI: [draint-be/scripts/oneshot-rescue.ts](https://github.com/DraintAi/draint-be/blob/main/scripts/oneshot-rescue.ts)
- Verified on-chain (Arbitrum One, gas paid in USDC, zero ETH): [arbiscan tx](https://arbiscan.io/tx/0x401874cc3404083dca0010684931604dd9a1a944129d8653646a3882e0f02d35)

## Venice AI Usage
- Venice client + classification prompt: [draint-be/src/lib/classifier/venice.ts](https://github.com/DraintAi/draint-be/blob/main/src/lib/classifier/venice.ts)
- Classifier orchestration (heuristic → Venice escalation on borderline): [draint-be/src/lib/classifier/index.ts](https://github.com/DraintAi/draint-be/blob/main/src/lib/classifier/index.ts)

## Feedback
<!-- If applying for the Feedback track: link your submitted feedback / opened issues here. -->

## Social Media
<!-- If applying for Best Social Media: link your X post tagging @MetaMaskDev (showcasing the drain't journey), and confirm criteria met. -->

> Also applying for **Best Agent** — autonomous monitor→classify→rescue loop: [loop.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/loop.ts) · [monitor.ts](https://github.com/DraintAi/draint-be/blob/main/src/agent/monitor.ts) · [routes/agent.ts](https://github.com/DraintAi/draint-be/blob/main/src/routes/agent.ts)

## Built with
MetaMask Smart Accounts Kit · Venice AI · 1Shot Permissionless Relayer · EIP-7702 / ERC-7710 · Foundry · Next.js · Hono on Bun

<div align="center"><sub>MetaMask Smart Accounts Kit × 1Shot API × Venice AI — Dev Cook Off</sub></div>
