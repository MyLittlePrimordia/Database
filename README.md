# 📦 IEM & Headphone Audio Database

**The open-source dataset of IEM specs, prices, and measurement curves.**

This repository powers **[IEM Tool](https://github.com/MyLittlePrimordia/IEM-Tool)**. It provides clean, standardized metadata and thousands of raw frequency response measurement curves (`.txt`) for earphones and headphones.

[📥 **Download Latest Database (.zip)**](https://github.com/MyLittlePrimordia/Database/archive/refs/heads/main.zip)

---

## ⚡ Updating Your IEM Tool (Quick Guide)

You don't need to reinstall **IEM Tool** to get newly added IEMs and measurement curves:

1. Click [**Download ZIP**](https://github.com/MyLittlePrimordia/Database/archive/refs/heads/main.zip).
2. Extract the files and copy these into your **IEM Tool** folder (replace existing files):
   * `database.json`
   * `database.json.gz`
   * `data/` *(folder with raw measurement curves)*
3. Relaunch **IEM Tool** — all new gear and target curves will appear immediately!

---

## 📊 What's Inside

- **`database.json` / `database.json.gz`:** Clean, verified specs (MSRP, driver setups, impedance, sensitivity, socket types, sound signature tags).
- **`data/`:** Standardized 2-column frequency response measurement files (`.txt`), pre-mapped to each model.

### Sample Entry
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
  "tags": ["Budget", "Balanced", "Smooth", "Fun"],
  "files": ["data/SUPER REVIEW/MOONDROP CHU II DSP.txt"]
}
```

---

## 🤝 Contributing New Gear

Want to add missing IEMs or updated measurements? The easiest way is using the visual **[🛠️ Database Tool](https://github.com/MyLittlePrimordia/Database-Tool)** desktop editor.

If opening a Pull Request manually, please ensure:
- `id` is lowercase with underscores (`brand_model_variant`).
- `price_usd` is rounded to the nearest $5 launch MSRP.
- Wired gear has valid non-zero `impedance` and `sensitivity`.
- Includes 4–12 approved tags from the tag taxonomy (at most one primary sound profile like *Neutral* or *V-Shaped*).

---

<details>
<summary><b>📐 Full Database Schema & Fields</b></summary>

| Field | Type | Description | Rules |
|---|---|---|---|
| `id` | `string` | Unique key | `brand_model_variant` (lowercase, underscores only) |
| `brand` | `string` | Brand name | Canonical official casing (e.g. `Moondrop`, `Sennheiser`) |
| `model` | `string` | Base model | Root product family name |
| `variant` | `string` | Revision / Edition | E.g. `II`, `Pro`, `DSP`, `Red`. Empty `""` if base model |
| `year` | `integer` | Launch year | Verified commercial launch year |
| `price_usd` | `integer` | Launch price | Launch MSRP rounded to nearest $5 |
| `driver_type` | `string` | Transducer type | `DD`, `BA`, `Planar`, `Hybrid`, `Tribrid`, `EST`, `BC` |
| `driver_config` | `string` | Driver layout | E.g. `1DD`, `1DD+2BA`, `1Planar` (no spaces) |
| `impedance` | `integer` | Impedance ($\Omega$) | Rated input impedance (`0` reserved for TWS) |
| `sensitivity` | `integer` | Sensitivity (dB) | Rated sensitivity (`0` reserved for TWS) |
| `connector` | `string` | Connection type | `2-pin`, `MMCX`, `QDC`, `Fixed Cable`, `Bluetooth`, etc. |
| `form_factor` | `string` | Form factor | `IEM`, `Earbuds (Wired)`, `Wireless Earbuds (TWS)`, etc. |
| `tags` | `array` | Sound & category tags| 4 to 12 approved tags |
| `files` | `array` | Measurement paths | Relative paths to `.txt` measurement curves |
</details>

<details>
<summary><b>🏷️ Approved Tag Taxonomy</b></summary>

Every entry uses 4 to 12 tags chosen strictly from this list:

```
[Basshead, Sub-Bass, Punchy Bass, Warm, Neutral, V-Shaped, U-Shaped, Balanced, Bright, Treblehead, Dark, Vocal-Focused, Detailed, Resolving, Technical, Wide-Stage, Good-Imaging, Smooth, Reference, Analytical, Fun, Relaxed, Gaming, Competitive-Gaming, Studio-Monitoring, Budget, Mid-Tier, Premium, Flagship, Collab, Limited-Edition]
```

- **Price tags:** Must include exactly one: `Budget` ($0–$99), `Mid-Tier` ($100–$499), `Premium` ($500–$1,499), or `Flagship` ($1,500+).
- **Sound signature:** At most one primary profile: `Neutral`, `Balanced`, `V-Shaped`, or `U-Shaped`.
- **No contradictions:** (e.g., cannot be both `Dark` and `Bright`, or `Warm` and `Analytical`).
</details>

<details>
<summary><b>💻 Code Examples (Python & TypeScript)</b></summary>

### Python
```python
import json

with open("database.json", "r", encoding="utf-8") as f:
    db = json.load(f)

# Find budget gaming IEMs
gaming_iems = [
    item for item in db
    if item["form_factor"] == "IEM"
    and "Budget" in item["tags"]
    and "Gaming" in item["tags"]
]
```

### TypeScript
```typescript
import database from "./database.json";

interface IEM {
  brand: string;
  model: string;
  price_usd: number;
  tags: string[];
}

const db = database as IEM[];
const budgetSets = db.filter((iem) => iem.tags.includes("Budget"));
```
</details>

---

## 🔗 Related Projects

* **[🎧 IEM Tool](https://github.com/MyLittlePrimordia/IEM-Tool):** The offline desktop audio workspace powered by this database.
* **[🛠️ Database Tool](https://github.com/MyLittlePrimordia/Database-Tool):** The visual desktop app for editing, auditing, and adding new curves to this dataset.