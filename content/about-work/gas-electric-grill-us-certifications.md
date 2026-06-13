---
title: "Gas + Electric BBQ Grill: US Certification Guide"
date: 2026-06-13T07:55:00+08:00
draft: false
tags:
  - compliance
  - manufacturing
  - ansi-standards
  - ul-certification
summary: "US certification guide for hybrid grills."
description: "A comprehensive guide to US certifications for gas BBQ grills with electric components (LED, motor, ignition), covering ANSI Z21, UL, NEC, Prop 65, FCC, and NFPA requirements."
---

## The Hybrid Challenge

A gas BBQ grill with electric components — LED lighting, rotisserie motor, electronic ignition — is not just a gas appliance. It is a **hybrid appliance** requiring dual certification: gas safety standards (ANSI Z21/CSA) AND electrical safety standards (UL).

This is more complex and expensive than either pure gas or pure electric certification. After researching 30+ sources, here is the complete certification landscape.

## Product Parameters

| Parameter | Value |
|---|---|
| Target Market | United States |
| Product Type | Gas BBQ grill (propane/natural gas) + electric components |
| Electric Components | LED lighting, rotisserie motor, electronic ignition, controls |
| End Use | Residential |
| Primary Gas Standard | ANSI Z21.58 / CSA 1.6 |
| Primary Electric Standard | UL 1026 |

## Gas Safety Standards (ANSI Z21 / CSA)

### Primary Standard: ANSI Z21.58 / CSA 1.6

ANSI Z21.58 / CSA 1.6 — "Outdoor Cooking Gas Appliances" — is the primary certification standard for residential gas BBQ grills. Current edition: 5th Edition (2022), with Amendment issued March 2024.

### All Applicable Gas Standards

| Standard | Scope | When Required |
|---|---|---|
| ANSI Z21.58 / CSA 1.6 | Outdoor Cooking Gas Appliances | PRIMARY — Gas grills, outdoor cookers |
| ANSI Z21.1 / CSA 1.1 | Household Cooking Gas Appliances | Indoor cooking appliances only |
| ANSI Z21.89 / CSA 1.18 | Outdoor Cooking Specialty Gas | Smokers, pizza ovens |
| ANSI Z21.15 | Manually Operated Gas Valves | Gas valve components |
| ANSI Z21.21 | Automatic Valves for Gas Appliances | Automatic gas shutoff |
| ANSI Z21.24 | Thermocouples | Flame safety devices |
| ANSI Z21.80 | Gas Pressure Regulators | Regulator components |
| NFPA 54 / ANSI Z223.1 | National Fuel Gas Code | Installation requirements |
| NFPA 58 | Liquefied Petroleum Gas Code | LP-gas storage and handling |

### Key Gas Safety Testing Requirements

| Test | Purpose | Acceptance Criteria |
|---|---|---|
| Gas Leak Test | Detect fuel gas leaks | No detectable leaks at joints/connections |
| Combustion Test | Measure CO emissions | CO ≤ specified limits per burner |
| Flame Stability Test | Verify flame doesn't lift/blow out | Stable flame under rated conditions |
| Delayed Ignition Test | Test ignition sequence safety | No dangerous delayed ignition |
| Input Rating Test | Verify gas input matches nameplate | Within ±5% of rated input |
| Venting Test | Verify proper exhaust | Adequate ventilation |
| Flame Failure Device | Test automatic gas shutoff | Shuts off gas within 60 seconds if flame lost |
| Cloth Ignition Test | Test flammability of nearby materials | No ignition under test conditions |

### LP-Gas vs Natural Gas: Critical Distinction

| Parameter | Propane (LP-Gas) | Natural Gas |
|---|---|---|
| Operating pressure | 11 in wc (2.74 kPa) | 7 in wc (1.74 kPa) |
| Orifice size | Smaller | Larger |
| Regulator | High-pressure | Low-pressure |
| Connection | Quick-disconnect | Permanent pipe |
| BTU per cubic foot | ~2,500 | ~1,000 |
| Certification | Requires separate LP certification | Requires separate NG certification |

**Critical:** LP-gas and natural gas models require separate certifications — different orifices, regulators, and pressure settings. You cannot use a single certification for both fuel types.

## Electric Component Standards

### Standards Matrix for Each Component

| Component | Standard | Key Requirements |
|---|---|---|
| LED Lighting | UL 153 + UL 8750 | High-temp rated, outdoor, grease-resistant; IP44+ rating |
| Rotisserie Motor | UL 982 / CSA 22.2 No. 68 | Locked-rotor protection; rated for 70°C ambient near burners |
| Electronic Ignition | UL 197 (part of gas standard) | Must ignite 4/5 attempts within 4 seconds |
| Temperature Controls | UL 60730 | Automatic controls safety; temperature sensing accuracy |
| WiFi/Bluetooth | FCC Part 15 Subpart C | FCC Certification (FCC ID required) |
| Digital Display | FCC Part 15 Subpart B | SDoC for unintentional radiator |
| Power Supply | UL/CSA 62368-1 | ITE/AV equipment safety |
| Power Cord | UL 817 | Appliance cord safety; outdoor rated |

### LED Lighting Specifics

- **Temperature rating:** Must withstand ambient temperatures up to 60-70°C near burners
- **IP rating:** Minimum IP44 for outdoor use; IP65 recommended
- **Grease resistance:** Must function in grease-rich environment
- **Voltage:** Typically 12V DC (transformer required from 120V AC)
- **Transformer:** Must be separately certified (UL 1585 or equivalent)

### Rotisserie Motor Specifics

- **Locked-rotor protection:** Must not overheat if motor jams
- **Temperature rating:** Rated for ambient temperatures near burners (up to 70°C)
- **Torque rating:** Must handle specified load with safety margin
- **Speed control:** If variable speed, additional UL 60730 requirements

### Electronic Ignition Specifics

Good news: Electronic ignition is typically covered under the gas safety standard (ANSI Z21.58 / CSA 1.6), not as a separate electrical certification. The gas standard includes requirements for ignition systems.

- **Ignition success rate:** Must ignite on 4 out of 5 attempts
- **Ignition time:** Must complete within 4 seconds
- **Flame failure device:** Must shut off gas within 60 seconds if flame is lost
- **Hot surface ignition:** Additional requirements for hot surface igniters

### WiFi/Bluetooth: Cost-Saving Recommendation

| Feature | Requirement | Cost |
|---|---|---|
| Basic Bluetooth | FCC Certification (FCC ID) | $5,000-$10,000 |
| WiFi module | FCC Certification (FCC ID) | $5,000-$10,000 |
| Pre-certified module (ESP32, etc.) | SDoC only (reference module FCC ID) | $2,000-$4,000 |

**Recommendation:** Use a pre-certified WiFi/Bluetooth module (e.g., ESP32) to avoid full RF certification. This saves $8,000-$20,000 and 4-6 weeks of timeline. You only need to do SDoC testing and reference the module's existing FCC ID.

## NEC & GFCI Requirements

### GFCI Protection (Mandatory for Outdoor)

NEC 2023 Section 210.8(F): All outdoor outlets on circuits ≤150V to ground and ≤50A require GFCI protection. A gas grill with electric components plugged into an outdoor outlet definitely requires GFCI.

### Circuit Requirements

| Load | Current Draw | Recommended Circuit |
|---|---|---|
| LED lighting only (~50W) | ~0.4A | Shared 15A circuit acceptable |
| LED + motor (~200W) | ~1.7A | Shared 15A circuit acceptable |
| LED + motor + ignition (~300W) | ~2.5A | Shared 15A circuit acceptable |
| WiFi + all electronics (~350W) | ~2.9A | Shared 15A circuit acceptable |

## NFPA & Clearance Requirements

### Applicable NFPA Standards

| NFPA Standard | Scope | Requirement |
|---|---|---|
| NFPA 1 | Fire Code | Outdoor cooking appliance clearance |
| NFPA 54 | National Fuel Gas Code | Gas appliance installation |
| NFPA 58 | LP-Gas Code | Propane storage, handling, installation |
| NFPA 70 (NEC) | National Electrical Code | Electrical installation |

### Propane Tank Storage (NFPA 58)

- **Maximum tank size:** 100 lb (45 kg) for residential outdoor storage
- **Storage location:** Outdoor only, at ground level, away from ignition sources
- **Distance from building:** Minimum 10 feet from doors/windows/vents
- **Under-deck storage:** Prohibited for tanks >2.2 lb

## CPSC & Proposition 65

### Prop 65 Chemicals for Gas Grills

| Chemical | Gas Grill Source | Category |
|---|---|---|
| Carbon Monoxide (CO) | Combustion byproduct | Reproductive toxicant |
| Lead (Pb) | Solder, brass fittings, old paint | Carcinogen + Reproductive toxicant |
| Nickel | Stainless steel, alloys | Carcinogen |
| Chromium VI | Chrome plating, stainless steel | Carcinogen |
| Benzene | Combustion byproduct | Carcinogen |
| Formaldehyde | Combustion byproduct | Carcinogen |

**Requirement:** Products sold in California must carry Prop 65 warnings if they expose users to listed chemicals above safe harbor levels.

### Carbon Monoxide Safety

- Never use gas grills indoors — CO poisoning risk
- Ventilation: Use only in well-ventilated outdoor areas
- CO detection: Consider including CO warning in manual
- CPSC data: ~420 CO deaths/year from consumer products; gas grills are a known source

## Certification Process & Costs

### Dual Certification Path

| Phase | Scope | Duration | Cost |
|---|---|---|---|
| 1. Gas Safety | ANSI Z21.58 / CSA 1.6 testing | 8-16 weeks | $12,000-$30,000 |
| 2. Electrical Safety | UL/CSA testing for electric components | 4-8 weeks | $5,000-$15,000 |
| 3. FCC (if WiFi/BT) | Part 15 testing and certification | 4-8 weeks | $3,000-$15,000 |
| 4. Factory Inspection | Initial factory audit | 1-2 weeks | Included in above |
| 5. Documentation | Manuals, labels, marking | 2-4 weeks | $2,000-$5,000 |
| **TOTAL** | | **12-24 weeks** | **$25,000-$65,000** |

### Certification Bodies Comparison

| Body | Gas+Electric Cost | Timeline | Best For |
|---|---|---|---|
| CSA Group | $15,000-$35,000 | 8-16 weeks | Gas expertise, US+Canada |
| ETL (Intertek) | $12,000-$30,000 | 6-14 weeks | Speed, cost-effective |
| UL | $20,000-$50,000 | 10-20 weeks | Brand recognition |

**Recommendation:** For a gas grill with electric components, CSA Group is often the best choice — they have deep gas appliance expertise and can handle both gas and electrical certification under one roof. This is more efficient than using separate labs for gas and electric.

### Annual Ongoing Costs

| Item | Cost |
|---|---|
| Factory inspection fee | $3,000-$8,000/year |
| Listing maintenance fee | $2,000-$5,000/year |
| Follow-up testing | $1,000-$3,000/year |
| **Total Annual** | **$5,000-$13,000/year** |

## Key Recommendations

1. **Use pre-certified WiFi/Bluetooth modules** — Saves $8,000-$20,000 and 4-6 weeks
2. **Choose CSA Group for dual certification** — Handles gas + electric under one roof
3. **Budget $25,000-$65,000 for initial certification** — 12-24 weeks timeline
4. **Plan $5,000-$13,000/year for ongoing costs** — Factory inspections, listing maintenance
5. **Separate LP and NG certifications** — Cannot use single certification for both fuels
6. **Include Prop 65 warnings** — Required for California market
7. **Design for GFCI compliance** — Mandatory for outdoor electrical outlets

---

*What certification challenges have you faced with hybrid appliances?*

---

## 燃氣+電動燒烤爐：美國認證指南

## 混合電器的挑戰

帶有電氣組件（LED照明、旋轉烤架馬達、電子點火）的燃氣燒烤爐不只是燃氣器具。它是**混合電器**，需要雙重認證：燃氣安全標準（ANSI Z21/CSA）和電氣安全標準（UL）。

這比純燃氣或純電動認證更複雜且更昂貴。在研究30多個來源後，以下是完整的認證全景。

## 產品參數

| 參數 | 值 |
|---|---|
| 目標市場 | 美國 |
| 產品類型 | 燃氣燒烤爐（丙烷/天然氣）+ 電氣組件 |
| 電氣組件 | LED照明、旋轉烤架馬達、電子點火、控制裝置 |
| 最終用途 | 住宅 |
| 主要燃氣標準 | ANSI Z21.58 / CSA 1.6 |
| 主要電氣標準 | UL 1026 |

## 燃氣安全標準（ANSI Z21 / CSA）

### 主要標準：ANSI Z21.58 / CSA 1.6

ANSI Z21.58 / CSA 1.6——「戶外烹飪燃氣器具」——是住宅燃氣燒烤爐的主要認證標準。當前版本：第5版（2022年），2024年3月發布修正案。

### 所有適用的燃氣標準

| 標準 | 範圍 | 適用場景 |
|---|---|---|
| ANSI Z21.58 / CSA 1.6 | 戶外烹飪燃氣器具 | 主要 — 燃氣燒烤爐、戶外炊具 |
| ANSI Z21.1 / CSA 1.1 | 家用烹飪燃氣器具 | 僅室內烹飪器具 |
| ANSI Z21.89 / CSA 1.18 | 戶外特殊烹飪燃氣器具 | 燻製器、披薩爐 |
| ANSI Z21.15 | 手動燃氣閥 | 燃氣閥組件 |
| ANSI Z21.21 | 自動燃氣閥 | 自動燃氣切斷 |
| ANSI Z21.24 | 熱電偶 | 火焰安全裝置 |
| ANSI Z21.80 | 燃氣調壓器 | 調壓器組件 |
| NFPA 54 / ANSI Z223.1 | 國家燃氣規範 | 安裝要求 |
| NFPA 58 | 液化石油氣規範 | LP-gas儲存和處理 |

### 關鍵燃氣安全測試

| 測試 | 目的 | 驗收標準 |
|---|---|---|
| 燃氣洩漏測試 | 檢測燃料氣體洩漏 | 接頭/連接處無可檢測洩漏 |
| 燃燒測試 | 測量CO排放 | CO ≤ 每個燃燒器規定限值 |
| 火焰穩定性測試 | 驗證火焰不會脫落/熄滅 | 額定條件下火焰穩定 |
| 延遲點火測試 | 測試點火序列安全性 | 無危險延遲點火 |
| 輸入額定測試 | 驗證燃氣輸入與銘牌匹配 | 在額定輸入的±5%以內 |
| 排煙測試 | 驗證正確排氣 | 通風充分 |
| 火焰故障裝置 | 測試自動燃氣切斷 | 火焰熄滅後60秒內切斷燃氣 |
| 布料點火測試 | 測試附近材料可燃性 | 測試條件下無點火 |

### 液化石油氣 vs 天然氣：關鍵區別

| 參數 | 丙烷（LP-Gas） | 天然氣 |
|---|---|---|
| 工作壓力 | 11英寸水柱（2.74 kPa） | 7英寸水柱（1.74 kPa） |
| 孔口尺寸 | 較小 | 較大 |
| 調壓器 | 高壓 | 低壓 |
| 連接 | 快速接頭 | 永久管路 |
| BTU/立方英尺 | ~2,500 | ~1,000 |
| 認證 | 需要單獨LP認證 | 需要單獨NG認證 |

**關鍵：** LP-gas和天然氣型號需要單獨認證——不同的孔口、調壓器和壓力設定。您無法使用單一認證涵蓋兩種燃料類型。

## 電氣組件標準

### 各組件標準矩陣

| 組件 | 標準 | 關鍵要求 |
|---|---|---|
| LED照明 | UL 153 + UL 8750 | 高溫額定、戶外、抗油脂；IP44+等級 |
| 旋轉烤架馬達 | UL 982 / CSA 22.2 No. 68 | 鎖定轉子保護；燃燒器附近70°C環境額定 |
| 電子點火 | UL 197（燃氣標準的一部分） | 4次中必須點燃4次；4秒內完成 |
| 溫度控制 | UL 60730 | 自動控制安全；溫度感測精度 |
| WiFi/藍牙 | FCC Part 15 Subpart C | FCC認證（需要FCC ID） |
| 數位顯示 | FCC Part 15 Subpart B | SDoC用於非有意輻射體 |
| 電源 | UL/CSA 62368-1 | ITE/AV設備安全 |
| 電線 | UL 817 | 電器電線安全；戶外額定 |

### WiFi/藍牙：節省成本建議

| 功能 | 要求 | 費用 |
|---|---|---|
| 基本藍牙 | FCC認證（FCC ID） | $5,000-$10,000 |
| WiFi模組 | FCC認證（FCC ID） | $5,000-$10,000 |
| 預認證模組（ESP32等） | 僅SDoC（引用模組FCC ID） | $2,000-$4,000 |

**建議：** 使用預認證WiFi/藍牙模組（如ESP32）以避免完整RF認證。這可節省$8,000-$20,000和4-6週的時間。您只需進行SDoC測試並引用模組現有的FCC ID。

## NEC & GFCI要求

### GFCI保護（戶外強制要求）

NEC 2023第210.8(F)條：所有≤150V對地、≤50A的戶外插座需要GFCI保護。插入戶外插座的帶電氣組件的燃氣燒烤爐絕對需要GFCI。

## NFPA & 間距要求

### 適用的NFPA標準

| NFPA標準 | 範圍 | 要求 |
|---|---|---|
| NFPA 1 | 消防規範 | 戶外烹飪器具間距 |
| NFPA 54 | 國家燃氣規範 | 燃氣器具安裝 |
| NFPA 58 | LP-Gas規範 | 丙烷儲存、處理、安裝 |
| NFPA 70 (NEC) | 國家電氣規範 | 電氣安裝 |

### 丙烷罐儲存要求（NFPA 58）

- **最大罐尺寸：** 住宅室外儲存100磅（45公斤）
- **儲存位置：** 僅室外、地面層、遠離點火源
- **距建築物距離：** 距門/窗/通風口最少10英尺
- **甲板下儲存：** 禁止儲存>2.2磅的罐體

## CPSC & Proposition 65

### 燃氣燒烤爐的Prop 65化學物質

| 化學物質 | 燃氣燒烤爐來源 | 類別 |
|---|---|---|
| 一氧化碳（CO） | 燃燒副產品 | 生殖毒物 |
| 鉛（Pb） | 焊料、黃銅配件、舊塗料 | 致癌物+生殖毒物 |
| 鎳 | 不鏽鋼、合金 | 致癌物 |
| 六價鉻 | 鍍鉻、不鏽鋼 | 致癌物 |
| 苯 | 燃燒副產品 | 致癌物 |
| 甲醛 | 燃燒副產品 | 致癌物 |

**要求：** 在加州銷售的產品如果使使用者暴露於列名化學物質超過安全港水平，必須攜帶Prop 65警告。

## 認證流程與成本

### 雙重認證路徑

| 階段 | 範圍 | 時間表 | 成本 |
|---|---|---|---|
| 1. 燃氣安全 | ANSI Z21.58 / CSA 1.6測試 | 8-16週 | $12,000-$30,000 |
| 2. 電氣安全 | UL/CSA電氣組件測試 | 4-8週 | $5,000-$15,000 |
| 3. FCC（如有WiFi/BT） | Part 15測試和認證 | 4-8週 | $3,000-$15,000 |
| 4. 工廠檢查 | 初始工廠審核 | 1-2週 | 包含在上述 |
| 5. 文件 | 手冊、標籤、標記 | 2-4週 | $2,000-$5,000 |
| **總計** | | **12-24週** | **$25,000-$65,000** |

### 認證機構比較

| 機構 | 燃氣+電氣成本 | 時間表 | 最適合 |
|---|---|---|---|
| CSA Group | $15,000-$35,000 | 8-16週 | 燃氣專業知識，美國+加拿大 |
| ETL (Intertek) | $12,000-$30,000 | 6-14週 | 速度，成本效益 |
| UL | $20,000-$50,000 | 10-20週 | 品牌認知度 |

**建議：** 對於帶電氣組件的燃氣燒烤爐，CSA Group通常是最佳選擇——他們擁有深厚的燃氣器具專業知識，可以在同一屋簷下處理燃氣和電氣認證。這比使用單獨的實驗室進行燃氣和電氣認證更有效率。

### 年度持續成本

| 項目 | 成本 |
|---|---|
| 工廠檢查費 | $3,000-$8,000/年 |
| 列表維護費 | $2,000-$5,000/年 |
| 後續測試 | $1,000-$3,000/年 |
| **年度總計** | **$5,000-$13,000/年** |

## 關鍵建議

1. **使用預認證WiFi/藍牙模組** — 節省$8,000-$20,000和4-6週
2. **選擇CSA Group進行雙重認證** — 在同一屋簷下處理燃氣+電氣
3. **預算$25,000-$65,000用於初始認證** — 12-24週時間表
4. **計劃$5,000-$13,000/年用於持續成本** — 工廠檢查、列表維護
5. **LP和NG認證分開** — 無法使用單一認證涵蓋兩種燃料
6. **包含Prop 65警告** — 加州市場需要
7. **設計符合GFCI要求** — 戶外電氣插座強制要求

---

*您在混合電器方面面臨過哪些認證挑戰？*

---

**References:**
- ANSI Z21.58 / CSA 1.6: Outdoor Cooking Gas Appliances (5th Edition, 2022)
- UL 1026: Electric Household Cooking Appliances
- UL 982: Motor-Operated Kitchen Machines
- NFPA 54: National Fuel Gas Code
- NFPA 58: Liquefied Petroleum Gas Code
- NEC 2023 Section 210.8(F): GFCI Protection
- FCC Part 15: Radio Frequency Devices
- California Proposition 65: Safe Drinking Water and Toxic Enforcement Act
- CPSC: Consumer Product Safety Commission
