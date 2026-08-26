# 🎧 IEM & Headphone Offline Discovery Database

A production-grade, audited, and strictly normalized JSON database of In-Ear Monitors (IEMs), Earbuds, and Over-Ear Headphones. Built for offline audio discovery applications, parametric EQ engines, target auto-tuning algorithms, and acoustic recommendation systems.

---

## 🌟 Key Features

* **Strict Canonical Schema:** Standardized metadata across audio devices with zero missing specifications on wired hardware.
* **Direct Measurement File Mapping:** Pre-linked paths to raw frequency response measurements (`.txt` curves) from major community acoustic archives.
* **Curated Acoustic Taxonomy:** Exactly 31 mutually exclusive, non-contradictory sonic, use-case, and price-tier tags.
* **Model/Variant Normalization:** Systematic separation of root product families from hardware revisions, tuning editions, collaborations, and retail DSP variants.
* **Multi-Source Trust Hierarchy:** Prioritizes Tier 1A manufacturer documentation (direct spec sheets/manuals) over third-party marketplace listings.

---

## 📐 Database Schema

Every product record in `database.json` strictly adheres to the following JSON object structure:

```json
{
  "id": "moondrop_chu_ii_dsp",
  "brand": "Moondrop",
  "model": "Chu",
  "variant": "II DSP",
  "year": 2024,
  "price_usd": 25,
  "driver_type": "DD",
  "driver_config": "1DD",
  "impedance": 18,
  "sensitivity": 119,
  "connector": "2-pin",
  "form_factor": "IEM",
  "tags": [
    "Budget",
    "Balanced",
    "Smooth",
    "Fun"
  ],
  "files": [
    "data/SUPER REVIEW/MOONDROP CHU II DSP.txt"
  ]
}
```

### Field Definitions & Validation Constraints

| Field | Type | Description | Strict Validation Rules |
|---|---|---|---|
| `id` | `string` | Unique Canonical Key | Lowercase alphanumeric with underscores only (`brand_model_variant`). If variant is empty, `brand_model`. No trailing underscores or punctuation. |
| `brand` | `string` | Manufacturer Name | Canonical official capitalization (e.g., `Audio-Technica`, `64 Audio`, `Sennheiser`). |
| `model` | `string` | Root Product Family | Base product family name only (e.g., `Chu`, `Aria`, `K240`, `Performer 8`). |
| `variant` | `string` | Hardware Revision / Edition | Official revision, DSP edition, or collaboration (e.g., `II`, `MKII`, `Pro`, `DSP`, `Red`, `Snow Edition`). Empty string `""` if base product. |
| `year` | `integer` | Launch Year | Verified commercial launch year. **Must never be `0`**. |
| `price_usd` | `integer` | Launch MSRP | Original launch price in USD rounded to the nearest $5. **Must never be `0`**. |
| `driver_type` | `string` | Transducer Class | One of: `DD`, `BA`, `BC`, `Planar`, `Hybrid`, `Tribrid`, `EST`, `MEMS`, `PZT`. |
| `driver_config` | `string` | Driver Configuration | Exact count without spaces around `+` (e.g., `1DD+2BA`, `1Planar`, `1DD+4BA+2EST`). |
| `impedance` | `integer` | Rated Impedance ($\Omega$) | Rated input impedance. **Must be $> 0$ for all wired devices**. `0` is strictly reserved for TWS. |
| `sensitivity` | `integer` | Rated Sensitivity (dB) | Rated sensitivity ($\text{dB/mW}$ or $\text{dB/Vrms}$). **Must be $> 0$ for all wired devices**. `0` is strictly reserved for TWS. |
| `connector` | `string` | Termination Socket | One of: `2-pin`, `MMCX`, `QDC`, `A2DC`, `Fixed Cable`, `Detachable Cable`, `Bluetooth`, `Proprietary`, `Electrostatic`. |
| `form_factor` | `string` | Physical Form Factor | One of: `IEM`, `Earbuds (Wired)`, `Wireless Earbuds (TWS)`, `Over-Ear Headphones (Wired)`, `Wireless Over-Ear Headphones`. |
| `tags` | `array[string]` | Metadata & Sonic Tags | 4 to 12 curated tags selected exclusively from the approved 31-tag taxonomy. |
| `files` | `array[string]` | Measurement Paths | Relative paths pointing to raw acoustic measurement data files (`.txt`). |

---

## 🎛️ Transducer & Connector Compatibility Matrix

### 1. Driver Technology Rules
* **Single-Technology:** Any quantity of a single driver technology is categorized under that base technology (`1DD` $\rightarrow$ `DD`, `5BA` $\rightarrow$ `BA`, `2Planar` $\rightarrow$ `Planar`).
* **Hybrid:** Exactly two distinct driver technologies combined (e.g., `1DD+1BA`, `1DD+4Planar`, `1Planar+1PZT`).
* **Tribrid:** Three or more distinct driver technologies combined (e.g., `1DD+2BA+2EST`, `2DD+5BA+2BC+4EST`, `1DD+1BA+1PZT`).

### 2. Form Factor vs. Connector Matrix

| Form Factor | Permitted Connectors | Forbidden Connectors |
|---|---|---|
| `IEM` | `2-pin`, `MMCX`, `QDC`, `A2DC`, `Fixed Cable`, `Proprietary` | `Bluetooth`, `Detachable Cable`, `Electrostatic` |
| `Earbuds (Wired)` | `2-pin`, `MMCX`, `Fixed Cable` | `Bluetooth`, `Detachable Cable`, `Electrostatic` |
| `Wireless Earbuds (TWS)` | `Bluetooth` | All wired socket types |
| `Over-Ear Headphones (Wired)` | `Detachable Cable`, `Fixed Cable`, `Electrostatic` | `Bluetooth` |
| `Wireless Over-Ear Headphones` | `Bluetooth` | All shell socket types |

---

## 🏷️ Curated Tagging Taxonomy

The database enforces a closed taxonomy of **31 approved tags**:

```
[Basshead, Sub-Bass, Punchy Bass, Warm, Neutral, V-Shaped, U-Shaped, Balanced, Bright, Treblehead, Dark, Vocal-Focused, Detailed, Resolving, Technical, Wide-Stage, Good-Imaging, Smooth, Reference, Analytical, Fun, Relaxed, Gaming, Competitive-Gaming, Studio-Monitoring, Budget, Mid-Tier, Premium, Flagship, Collab, Limited-Edition]
```

### Core Tagging Constraints
1. **Tag Count:** Every entry must contain between **4 and 12 tags**.
2. **Primary Tonal Restraint:** At most **one** primary tonal descriptor from `{Neutral, Balanced, V-Shaped, U-Shaped}` is permitted per entry.
3. **Mandatory Price Tier Tag:** Exactly one price tier tag matching `price_usd`:
   * `Budget`: $\$0 - \$99$
   * `Mid-Tier`: $\$100 - \$499$
   * `Premium`: $\$500 - \$1,499$
   * `Flagship`: $\$1,500+$
4. **Forbidden Contradictory Pairs:**
   * ❌ `V-Shaped` + `U-Shaped`
   * ❌ `Neutral` + `V-Shaped`
   * ❌ `V-Shaped` + `Vocal-Focused`
   * ❌ `Dark` + `Bright`
   * ❌ `Dark` + `Treblehead`
   * ❌ `Warm` + `Bright`
   * ❌ `Warm` + `Analytical`
   * ❌ `Basshead` + `Treblehead`

---

## 🔬 Data Authority & Verification Hierarchy

When cross-referencing conflicting data across community sources, data updates follow a strict authority hierarchy:

```
┌────────────────────────────────────────────────────────┐
│  Tier 1A: Direct Manufacturer Sources                  │
│  (Official Spec Sheets, User Manuals, Press Releases)  │
└───────────────────────────┬────────────────────────────┘
                            │ (Overrides if conflicting)
┌───────────────────────────▼────────────────────────────┐
│  Tier 1B: Verified Measurement Databases               │
│  (Consensus across 2+ Repositories: Crinacle, Oratory, │
│   Super Review, RTINGS, InnerFidelity)                 │
└───────────────────────────┬────────────────────────────┘
                            │ (Overrides if unverified)
┌───────────────────────────▼────────────────────────────┐
│  Tier 2: Third-Party Marketplaces / Resellers          │
│  (Amazon, AliExpress, Secondary Forums)                │
└───────────────────────────┘
```

---

## 💻 Integration Examples

### Python (Filtering & Recommendations)

```python
import json

def load_database(filepath: str = "database.json") -> list[dict]:
    with open(filepath, "r", encoding="utf-8") as f:
        return json.load(f)

db = load_database()

# Find all mid-tier hybrid IEMs suited for gaming and monitoring
recommendations = [
    item for item in db
    if item["form_factor"] == "IEM"
    and item["driver_type"] == "Hybrid"
    and "Mid-Tier" in item["tags"]
    and any(tag in item["tags"] for tag in ["Gaming", "Studio-Monitoring"])
]

for rec in recommendations:
    print(f"[{rec['brand']}] {rec['model']} {rec['variant']} - ${rec['price_usd']} | {rec['driver_config']}")
```

### TypeScript / JavaScript

```typescript
interface AudioProduct {
  id: string;
  brand: string;
  model: string;
  variant: string;
  year: number;
  price_usd: number;
  driver_type: "DD" | "BA" | "BC" | "Planar" | "Hybrid" | "Tribrid" | "EST" | "MEMS" | "PZT";
  driver_config: string;
  impedance: number;
  sensitivity: number;
  connector: "2-pin" | "MMCX" | "QDC" | "A2DC" | "Fixed Cable" | "Detachable Cable" | "Bluetooth" | "Proprietary" | "Electrostatic";
  form_factor: "IEM" | "Earbuds (Wired)" | "Wireless Earbuds (TWS)" | "Over-Ear Headphones (Wired)" | "Wireless Over-Ear Headphones";
  tags: string[];
  files: string[];
}

import database from "./database.json";

const db = database as AudioProduct[];
const flagshipPlanars = db.filter(
  (item) => item.driver_type === "Planar" && item.tags.includes("Flagship")
);
```

---

## 🛠️ Contribution Guidelines

We welcome contributions for missing models, newly released gear, and measurement alignments. Please verify the following checklist before submitting a Pull Request:

- [ ] `id` follows `brand_model_variant` in all lowercase with underscores only.
- [ ] `year` reflects the original commercial launch year (not re-release dates).
- [ ] `price_usd` reflects launch MSRP in integer format rounded to nearest $5.
- [ ] No `0` values for `impedance` or `sensitivity` on wired devices.
- [ ] `driver_config` contains no spaces around the `+` sign.
- [ ] `tags` array contains 4–12 items, includes exactly one price tier tag, at most one primary tonal tag, and no conflicting pairs.

## 🚀 Using with IEM Tool

This repository directly powers **[IEM Tool](https://github.com/MyLittlePrimordia/IEM-Tool)**. You do not need to redownload or reinstall the entire application to get newly added models and target curves. To update your local catalog and measurement files:

1. Click **Code** → **[Download ZIP](https://github.com/MyLittlePrimordia/Database/archive/refs/heads/main.zip)** on this repository (or `git pull` if cloned).
2. Extract the archive and copy the following into your **IEM Tool** application folder (overwrite when prompted):
   * `database.json`
   * `database.json.gz`
   * `data/` *(folder containing all raw `.txt` measurement curve files)*
3. Relaunch **IEM Tool** — all new models, measurement graphs, and EQ targets will load automatically.