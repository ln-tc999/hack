# Context — Casper Agentic Buildathon 2026

> Dokumen konteks proyek untuk **Casper Agentic Buildathon 2026 — Qualification Round**.
> Bisa dibaca manusia DAN di-feed ke AI agent sebagai konteks awal sebelum membangun.
> Pendamping: dokumen Eclipse (`eclipse/context-stellar.md`) — sebagian riset ZK di-recycle ke sini.

---

## 1. Hackathon — inti yang harus diingat

- **Penyelenggara:** Casper Association · Platform: DoraHacks · Event partner: Istanbul Blockchain Week.
- **Tema:** **Agentic AI** di persimpangan **DeFi + RWA (Real-World Assets)** di **Casper Network**.
- **Periode:** 1–30 Juni 2026 (Qualification Round). Hari ini **2026-06-23** → deadline **30 Juni 2026**.
- **Hadiah:** total $150K ($30K cash + $100K x402 ecosystem credits + $20K in-kind).
- **Struktur 2 fase:** Qualification Round → Final Round (3 minggu setelahnya, juri profesional).

### Syarat submission (wajib)
1. **Prototype jalan di Casper Testnet** dengan komponen on-chain yang **menghasilkan transaksi**.
2. **Repo open-source** (GitHub) + README dengan dokumentasi & instruksi pakai.
3. **Video demo** publik (penjelasan + walkthrough).

### Mekanisme lolos Qualification (hybrid)
- **Community Voting Path:** top 3 voting via **CSPR.fans** langsung ke Final tanpa juri.
- **Builder Merit Path:** sisanya cukup penuhi syarat teknis (prototype on-chain tx-producing) untuk lanjut ke Final.

### Kriteria juri Final (kompas desain)
| Kriteria | Catatan |
|---|---|
| Technical Execution | kualitas kode, arsitektur, kelengkapan |
| Innovation & Originality | kebaruan pendekatan |
| **Use of AI / Agentic Systems** | integrasi agen otonom yang **bermakna** (bukan chatbot) |
| Real-World Applicability | kegunaan, khususnya DeFi & RWA |
| User Experience & Design | kualitas UI/interaksi |
| Working Smart Contracts | kontrak fungsional terdeploy di Testnet |
| Long-Term Launch Plans | proyek nyata, ada sosial, rencana deploy |
| Potential for Long-Term Impact | kontribusi ke pertumbuhan ekosistem Casper |

### Bias penyelenggara (dari riset — pakai sebagai kompas)
1. **Machine economy / "trust layer for the agent economy"** — visi resmi Casper Manifest (Mei 2026).
2. **Regulated RWA** — pilar utama Manifest; EVM-compat & compliant token menyusul akhir 2026.
3. **x402 baru live (4 Juni 2026)** — Casper = **L1 WASM-native pertama dengan x402 micropayment live**. Ekosistem masih kosong → memamerkan x402 nyata = keunggulan voting & juri.

---

## 2. Keputusan tooling

| Pilihan | Status | Alasan |
|---|---|---|
| **Odra Framework** (Rust) | ✅ smart contract | Toolkit kontrak Casper, ada `llms.txt` (AI-friendly), agen bisa tulis/test/deploy sendiri |
| **x402 Facilitator** | ✅ load-bearing | Micropayment HTTP-native antar-agen — fitur unggulan Casper yang baru live |
| **MCP (Casper / CSPR.trade)** | ✅ perceive | Agen discover state chain & bertindak di DeFi |
| **CSPR.click** | ✅ identity & signing | Tiap agen punya wallet & sign tx sendiri |
| **CSPR.cloud APIs** | ✅ data layer | REST/Streaming/Node middleware |
| **LLM = Claude** | ✅ reasoning | Keputusan agen + reasoning log |
| **ElizaOS** (opsional) | cadangan | Framework runtime swarm agen, percepat dev |
| **Noir / ZK** | ✅ untuk Compliance | Recycle riset Eclipse untuk credential KYC ZK |

---

## 3. Ide final — 🏛️ Syndicate

> *Nama kerja, bisa diganti.*

**One-liner:** Dana kredit dunia-nyata yang dijalankan **bukan oleh manusia, tapi oleh sekumpulan agen AI** yang saling menyewa & membayar lewat **x402** — mereka *sourcing, underwrite, mendanai, dan memantau* pinjaman RWA secara otonom, sementara LP cukup deposit modal dan semua keputusan terekam & dipertaruhkan lewat **reputasi on-chain**.

### Asal: gabungan 3 ide (saling mengunci, bukan tumpukan fitur)
- **#3 Self-Driving RWA Fund** → **wadahnya** (LP deposit, swarm mengelola).
- **#1 Autonomous RWA Credit Desk** → **strateginya** (private credit RWA dunia nyata).
- **#2 Machine Economy Marketplace** → **mekanisme koordinasinya** (agen = aktor ekonomi, saling hire & bayar via x402, diatur reputasi).

```
┌─────────────────────────────────────────────────────────┐
│  LAPIS FUND (#3)   LP deposit modal → terima yield        │
│  Vault + governance otonom oleh swarm agen                │
├─────────────────────────────────────────────────────────┤
│  LAPIS STRATEGI (#1)   Private credit RWA                  │
│  originate → underwrite → fund → monitor → exit/default   │
├─────────────────────────────────────────────────────────┤
│  LAPIS EKONOMI MESIN (#2)   agen sbg aktor ekonomi         │
│  saling discover/hire/bayar via x402 · identity+reputasi  │
└─────────────────────────────────────────────────────────┘
```
**Insight kunci:** fund itu sendiri adalah ekonomi mesin kecil. Tiap agen tidak "dipanggil sebagai fungsi" — ia **dibayar per-jasa via x402** dan reputasinya naik/turun sesuai kinerja. Itu yang membuat #2 jadi *tata kelola internal* dana, bukan tempelan.

### Kenapa kombinasi ini kuat
- **Fit misi Casper maksimum:** menutup **dua pilar Manifest sekaligus** — regulated RWA (#1, compliance ZK) **dan** machine economy (#2).
- **Agentic tak terbantahkan:** bukan satu bot — sebuah *organisasi agen* yang berdeliberasi, bertransaksi, saling menilai → "autonomous coordination on-chain".
- **x402 load-bearing:** pembayaran antar-agen = urat nadi produk.
- **Reputasi = pembeda:** menjawab "kenapa percaya agen?" (underexplored di x402).
- **Berguna nyata:** private credit RWA = pasar besar yang belum terpecahkan.
- **Votable:** "dana kredit yang dijalankan sepenuhnya oleh AI yang saling membayar."

---

## 4. Roster agen (swarm — tiap agen = aktor ekonomi mandiri)

| Agen | Tugas | Dibayar untuk (x402) | Reputasi naik kalau |
|---|---|---|---|
| **Scout** | Sourcing borrower/aset RWA (invoice, treasury, receivable) | tiap lead valid | lead-nya lolos underwriting |
| **Underwriter** | Risk model, tentukan terms & bunga | tiap penilaian | pinjamannya tidak default |
| **Compliance** | KYC/AML + terbitkan credential ZK, monitor & revoke | tiap clearance | tak ada pelanggaran lolos |
| **Oracle** | Verifikasi data dunia nyata (harga aset, status bayar) | tiap data feed | datanya akurat |
| **Treasurer** | Alokasi modal pool, jaga likuiditas | peran inti vault | return/risk seimbang |
| **Collector** | Pantau pembayaran, tangani tunggakan/default | tiap recovery | recovery rate tinggi |

Agen **saling membeli jasa** (Underwriter bayar Oracle + Compliance via x402) → pasar internal. Agen pihak ketiga juga bisa menjual jasa ke fund → marketplace terbuka.

---

## 5. Mekanika ekonomi mesin (x402 + reputasi)

- **Pembayaran:** tiap permintaan jasa antar-agen = HTTP 402 → bayar on-chain via x402 → terima hasil. Tercatat di `x402-settlement`.
- **Identity:** tiap agen punya wallet/identity sendiri (CSPR.click) terdaftar di `AgentRegistry`.
- **Reputasi:** skor on-chain per agen (ala ERC-8004), naik/turun berdasar outcome (default rate, akurasi data, recovery rate). Dipakai untuk routing pekerjaan (agen reputasi tinggi dapat lebih banyak job/fee).
- **Terbuka:** agen pihak ketiga bisa daftar & jual jasa → marketplace agen sungguhan.

---

## 6. Lifecycle otonom (satu pinjaman dari lahir sampai lunas)

1. **Scout** temukan borrower RWA → ajukan ke fund.
2. **Underwriter** beli data dari **Oracle** (x402) + clearance dari **Compliance** (ZK credential, x402) → tentukan terms.
3. **Treasurer** setujui & **danai dari Vault** → tx on-chain (Odra).
4. **Oracle + Collector** pantau; pembayaran masuk → yield ke LP.
5. Kalau menunggak → **Collector** eksekusi recovery; reputasi tiap agen di-update.
6. Semua keputusan + alasan + pembayaran antar-agen **terekam on-chain** → transparan & auditable.

---

## 7. Arsitektur

```
Agent swarm (TS / ElizaOS runtime, LLM=Claude)
  ├─ MCP        → perceive state Casper (CSPR.trade MCP)
  ├─ x402       → pembayaran antar-agen & ke layanan eksternal
  ├─ CSPR.click → sign & submit tx tiap agen (identity per-agen)
  └─ reasoning logs → ditampilkan di dashboard

On-chain (Odra, Casper Testnet):
  ├─ Vault            → modal LP, pendanaan & repayment (tx-producing ✅)
  ├─ LoanRegistry     → posisi kredit RWA (terms, status)
  ├─ AgentRegistry    → identity + reputation tiap agen (ERC-8004 style)
  ├─ ComplianceZK     → credential ZK (recycle riset Eclipse)
  └─ x402Settlement   → catat pembayaran antar-agen

Frontend (Next.js)
  ├─ LP view          → deposit, yield, posisi fund
  ├─ Swarm view       → agen apa sedang ngapain + reasoning live (votable!)
  ├─ Marketplace      → jasa agen, harga x402, reputasi
  └─ Loan book + audit trail + link explorer
```

---

## 8. Demo flow (votable, ~2 menit)

> "Ini Syndicate — dana kredit tanpa satu pun manusia di dalamnya. **Scout** menemukan invoice dunia nyata. **Underwriter** membeli data dari **Oracle** dan clearance KYC dari **Compliance** — **dia membayar mereka via x402, di depan mata kalian**. LLM-nya memutuskan terms — **alasannya tampil di layar**. **Treasurer** mendanai dari modal LP — **transaksi on-chain, nyata**. Bulan depan borrower bayar, yield mengalir ke LP, dan reputasi tiap agen naik. Kalian baru saja menyaksikan ekonomi mesin menjalankan keuangan dunia nyata, sendirian."

Menyentuh kriteria juri dalam 1 alur: agentic ✓ x402 ✓ DeFi/RWA ✓ working contract ✓ UX ✓.

---

## 9. Daftar fitur definitif (full scope)

### 9.1 Fund / LP
1. LP deposit modal ke Vault
2. LP terima yield proporsional
3. Dashboard posisi fund (AUM, utilisasi, yield, default rate)
4. Withdraw / redeem share
5. Audit trail seluruh keputusan & arus dana

### 9.2 Agen & swarm
6. 6 agen spesialis (Scout, Underwriter, Compliance, Oracle, Treasurer, Collector)
7. Tiap agen punya identity/wallet sendiri (CSPR.click)
8. Reasoning log per keputusan (LLM) ditampilkan live
9. Loop otonom berjalan terjadwal/triggered
10. Agen pihak ketiga bisa daftar & jual jasa (marketplace terbuka)

### 9.3 Ekonomi mesin (x402)
11. Pembayaran antar-agen per-jasa via x402
12. Endpoint berbayar (HTTP 402) untuk tiap jasa agen
13. Ledger pembayaran on-chain (`x402Settlement`)
14. Routing pekerjaan berdasarkan reputasi & harga

### 9.4 Reputasi & identity
15. `AgentRegistry` — identity on-chain tiap agen (ERC-8004 style)
16. Reputation score naik/turun berdasar outcome
17. Riwayat kinerja per agen (default rate, akurasi, recovery)
18. Portable agent ID

### 9.5 Strategi kredit RWA
19. Sourcing borrower/aset (Scout)
20. Underwriting & risk model (Underwriter)
21. Terms & bunga otomatis
22. Pendanaan dari Vault → tx on-chain
23. Monitoring pembayaran berkelanjutan (Oracle/Collector)
24. Penanganan tunggakan & default/recovery
25. Multi-jenis aset RWA (invoice, treasury, receivable)

### 9.6 Compliance (AI + ZK)
26. KYC/AML otomatis per borrower
27. Terbitkan compliance credential ZK (tanpa expose data)
28. Monitoring berkelanjutan + revoke/update otonom
29. Selective disclosure untuk auditor/regulator

### 9.7 On-chain (Odra contracts)
30. `Vault` — modal LP, pendanaan, repayment
31. `LoanRegistry` — posisi kredit (terms, status)
32. `AgentRegistry` — identity + reputation
33. `ComplianceZK` — credential ZK
34. `x402Settlement` — pembayaran antar-agen
35. Event/log on-chain untuk seluruh aksi

### 9.8 Frontend / UX
36. LP view (deposit/yield/posisi)
37. Swarm view (agen + reasoning live)
38. Marketplace view (jasa agen, harga x402, reputasi)
39. Loan book + audit trail
40. Deep-link ke Casper explorer (bukti tx nyata)
41. Onboarding/penjelasan peran tiap agen

### 9.9 Infra / dokumentasi
42. Deploy semua contract ke Casper Testnet
43. Script setup toolchain (Odra, CSPR CLI, fund testnet account)
44. README + dokumentasi + usage
45. Video demo 2–3 menit
46. Akun sosial proyek (kriteria "long-term launch plans")

---

## 10. Struktur monorepo

Dikelola dengan **pnpm workspaces** (TS: agen, web, packages) + **Cargo workspace** (Rust/Odra contracts). Circuit ZK pakai workspace Noir sendiri (recycle dari Eclipse).

```
syndicate/
├─ contracts/                     # Odra (Rust) — Cargo workspace
│  ├─ Cargo.toml
│  ├─ vault/src/lib.rs            # modal LP, pendanaan, repayment
│  ├─ loan-registry/src/lib.rs    # posisi kredit RWA
│  ├─ agent-registry/src/lib.rs   # identity + reputation (ERC-8004 style)
│  ├─ compliance-zk/src/lib.rs    # verifikasi credential ZK
│  ├─ x402-settlement/src/lib.rs  # ledger pembayaran antar-agen
│  └─ shared/src/lib.rs           # tipe bersama, events
│
├─ agents/                        # swarm runtime (TS / ElizaOS)
│  ├─ shared/                     # base agent, LLM client, mcp, x402, signer
│  │  └─ src/index.ts
│  ├─ scout/src/index.ts
│  ├─ underwriter/src/index.ts
│  ├─ compliance/src/index.ts
│  ├─ oracle/src/index.ts
│  ├─ treasurer/src/index.ts
│  └─ collector/src/index.ts
│
├─ circuits/                      # Noir — ZK compliance (recycle Eclipse)
│  ├─ Nargo.toml
│  └─ kyc_credential/src/main.nr
│
├─ packages/                      # TS shared libs
│  ├─ casper-sdk/src/index.ts     # CSPR.cloud, bindings contract, tx builder
│  ├─ x402/src/index.ts           # client + server (HTTP 402) helpers
│  └─ mcp/src/index.ts            # wrapper MCP client (Casper/CSPR.trade)
│
├─ web/                           # Next.js dashboard
│  ├─ app/
│  │  ├─ page.tsx                 # landing
│  │  ├─ lp/page.tsx              # LP view
│  │  ├─ swarm/page.tsx           # agen + reasoning live
│  │  ├─ marketplace/page.tsx     # jasa agen + reputasi
│  │  └─ loans/page.tsx           # loan book + audit trail
│  ├─ components/
│  │  ├─ AgentFeed.tsx            # reasoning log live
│  │  ├─ ReputationCard.tsx
│  │  ├─ X402PaymentTrace.tsx     # visualisasi pembayaran antar-agen
│  │  └─ LoanLifecycle.tsx
│  ├─ lib/{casper.ts,config.ts}
│  └─ public/
│
├─ scripts/
│  ├─ setup.sh                    # install Odra, CSPR CLI, fund testnet account
│  ├─ deploy.sh                   # deploy semua contract → tulis alamat ke config
│  ├─ run-swarm.sh                # jalankan agen swarm
│  └─ seed.sh                     # borrower & loan contoh untuk demo
│
├─ docs/
│  ├─ CASPER-brief.md             # brief asli buildathon
│  ├─ ARCHITECTURE.md
│  ├─ AGENTS.md                   # spesifikasi tiap agen (input, keputusan, output)
│  └─ DEMO.md                     # skrip demo 2 menit
│
├─ context-casper.md              # dokumen ini
├─ pnpm-workspace.yaml            # packages: ["web","agents/*","packages/*"]
├─ package.json
├─ .env.example
└─ README.md
```

### Catatan struktur
- **3 toolchain berdampingan:** `cargo`/`odra` (contracts), `pnpm`/`node` (agen + web + packages), `nargo` (circuits ZK).
- **`packages/casper-sdk`** = satu sumber alamat contract + bindings → dipakai agen, web, scripts.
- **`packages/x402`** = helper client (bayar) + server (endpoint 402) yang dipakai semua agen.
- **`agents/shared`** = kelas dasar agen (perceive→reason→act→record) yang di-extend tiap agen spesialis.

---

## 11. Toolchain & dependencies

| Layer | Tool | Catatan |
|---|---|---|
| Smart contract | **Odra (Rust)** | target wasm Casper, `llms.txt` AI-friendly |
| Contract tooling | **Casper CLI / casper-client** | deploy & query Testnet |
| Agent runtime | **TypeScript (Node)** / **ElizaOS** opsional | swarm agen |
| LLM | **Claude** | reasoning + keputusan |
| Payments | **x402 Facilitator (Casper)** | HTTP 402 micropayment |
| Chain access | **MCP (Casper / CSPR.trade)** | perceive & act |
| Signing/identity | **CSPR.click** | wallet & sign per agen |
| Data/middleware | **CSPR.cloud APIs** | REST/Streaming/Node |
| ZK | **Noir (`nargo`) + `bb`** | credential KYC (recycle Eclipse) |
| Frontend | **Next.js + TypeScript** | dashboard |
| Monorepo | **pnpm workspaces + Cargo workspace** | |
| Network | **Casper Testnet** | |

### Env vars (`.env.example`)
```
CASPER_NETWORK=testnet
CASPER_NODE_URL=
CSPR_CLOUD_API_KEY=
X402_FACILITATOR_URL=
ANTHROPIC_API_KEY=
DEPLOYER_SECRET_KEY_PATH=
VAULT_CONTRACT_HASH=
LOAN_REGISTRY_HASH=
AGENT_REGISTRY_HASH=
COMPLIANCE_ZK_HASH=
X402_SETTLEMENT_HASH=
```

### Root scripts (`package.json`)
- `pnpm setup` → install toolchain + fund akun testnet
- `pnpm build:contracts` → build Odra contracts
- `pnpm deploy` → deploy ke Testnet, tulis alamat ke `packages/casper-sdk`
- `pnpm swarm` → jalankan agen swarm
- `pnpm dev` → frontend dashboard

---

## 12. Resources kunci

### Casper core
- AI Toolkit: https://www.casper.network/ai
- Developer Docs: https://docs.casper.network/
- GitHub: https://github.com/casper-network
- Odra Framework: (lihat AI Toolkit / docs, `llms.txt` support)

### Buildathon
- Brief asli: `docs/CASPER-brief.md` (dari `CASPER.md`)
- Platform: DoraHacks · Voting: CSPR.fans
- Workshop IBW (2 Juni): https://luma.com/casper-bzn7

### Agentic / x402 referensi (prior art global)
- x402 di Base — 100M+ tx (Chainalysis): https://www.chainalysis.com/blog/x402-agentic-payments-adoption/
- x402 guide 2026: https://calmops.com/web3/x402-protocol-programmable-payments-ai-agents-2026/
- ClawRouter (Circle USDC, agent-owned wallets + per-request pay): https://blockchain.news/news/circle-usdc-openclaw-hackathon-winners-ai-agents
- Bob Is Alive / verifiable agents (Synthesis): https://blockchain.news/news/verifiable-ai-agents-synthesis-hackathon-winners
- AI agents in crypto — Fetch.ai, Virtuals, Autonolas, ElizaOS, ERC-8004 (KuCoin): https://www.kucoin.com/learn/web3/what-are-ai-agents-in-crypto-and-top-projects
- Casper Manifest (regulated RWA + machine economy): https://dailyhodl.com/2026/05/12/casper-network-publishes-the-casper-manifest-a-multi-year-roadmap-to-power-regulated-real-world-assets-and-the-machine-economy/
- Casper AI Toolkit launch (first WASM-native L1 w/ live x402): https://chainwire.org/2026/06/04/casper-network-launches-ai-toolkit-becoming-first-webassembly-native-blockchain-with-live-x402-payments/

### ZK (recycle dari Eclipse)
- `eclipse/context-stellar.md` — riset ZK/Noir/Poseidon
- Noir docs: https://noir-lang.org/docs/

---

## 13. Status & next steps

- [x] Pilih hackathon: Casper Agentic Buildathon
- [x] Riset prior art (agentic, x402, Casper ecosystem)
- [x] Kunci ide final: Syndicate (gabungan #1+#2+#3)
- [x] Tooling: Odra + x402 + MCP + CSPR.click + Claude + Noir(ZK)
- [ ] Rencana implementasi detail (milestone + urutan kerja)
- [ ] Scaffold monorepo `syndicate/`
- [ ] Contract inti: Vault + LoanRegistry (tx-producing) → deploy Testnet
- [ ] `agents/shared` base + Scout + Underwriter + Treasurer
- [ ] Integrasi x402 antar-agen (Underwriter ↔ Oracle/Compliance)
- [ ] AgentRegistry + reputation
- [ ] ComplianceZK (recycle Noir)
- [ ] Oracle + Collector + marketplace terbuka
- [ ] Dashboard (LP / Swarm / Marketplace / Loans)
- [ ] Demo video + README + sosial proyek
