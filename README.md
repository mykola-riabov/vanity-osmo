# 🧪 custom-cosmos-address (CPU-only)

**custom-cosmos-address** is a fast custom address generator for **Cosmos SDK-based blockchains** (`osmo1...`, `cosmos1...`, `inj1...`, etc.) that runs **entirely on the CPU**, with optional multiprocessing and BIP39 mnemonic support.

---

## 🚀 Features

- ✅ Generate Cosmos addresses with custom prefix and/or suffix
- 🔐 Configurable entropy strength (128–256 bits)
- 🧵 Optional CPU multiprocessing for address filtering
- 🧠 Optional BIP39 mnemonic phrase generation (`--mnemonic`)
- 🧭 Custom derivation path support (e.g. `m/44'/118'/0'/0/0`)
- 🔥 Real-time speed and CPU temperature display
- 💾 Save results as JSON (with optional mnemonic)

---

## 🧰 Requirements

- Python 3.8+
- Install dependencies:

```bash
pip install -r requirements.txt
```

---

## 💻 Example Usage

```bash
# Fast generation using random private key (default mode)
python3 main.py --prefix osmo1aaa --batch 100000 --pool --pool-workers 4

# Slower generation using BIP39 mnemonic phrase
python3 main.py --prefix cosmos1gpt --mnemonic

# With custom derivation path (e.g., Ethereum-style)
python3 main.py --prefix inj1zzz --mnemonic --path "m/44'/60'/0'/0/0"
```

---

## ℹ️ Mnemonic vs. Random Private Key

By default, the tool generates addresses using random private keys via `os.urandom`, which is very fast and suitable for bulk searching.

If you use the `--mnemonic` flag:
- A BIP39-compliant mnemonic phrase is generated (e.g., 12 or 24 words).
- From the mnemonic, a seed is created, and then a private key is derived **using the given `--path`** (default: `m/44'/118'/0'/0/0` — used by Keplr and Cosmos SDK).
- The corresponding address is calculated from the derived key.
- This process is significantly **slower** than direct key generation, but it allows you to **back up your address using the mnemonic**.

⚠️ You **can create a private key from a mnemonic**, but you **cannot regenerate a mnemonic from a private key**.

📌 If you do **not** use `--mnemonic`, the `--path` parameter is ignored — it only applies to HD-wallet key derivation.

---

## 🧭 What is a derivation path?

A derivation path defines **how an address is generated from a mnemonic**. It is used in HD wallets like Keplr, Ledger, and Metamask to organize multiple addresses from a single seed.

The default path in Cosmos is:

```
m/44'/118'/0'/0/0
```

| Segment          | Meaning                          |
|------------------|----------------------------------|
| `44'`            | BIP44 standard                   |
| `118'`           | Cosmos coin type                 |
| `0'`             | Account index (0 = first account)|
| `0`              | External chain (0 = receive)     |
| `0`              | Address index (0 = first address)|

You can change the path to generate addresses compatible with other networks (e.g., `60'` for Ethereum-based chains like Injective).

---

## 🔧 Command-Line Arguments

| Option            | Description |
|-------------------|-------------|
| `--prefix`        | Address must start with this string (e.g. `osmo1`, `cosmos1`, `inj1`) |
| `--suffix`        | Address must end with this string |
| `--batch`         | Keys per CPU batch |
| `--count`         | Number of matching addresses to find |
| `--strength`      | Key entropy strength: 128–256 bits |
| `--output`        | Output file (default: `osmo_cpu_found.json`) |
| `--pool`          | Enable multiprocessing for filtering |
| `--pool-workers`  | Number of CPU processes (default: 2) |
| `--mnemonic`      | Generate from BIP39 mnemonic (instead of random key) |
| `--path`          | Derivation path for mnemonic (default: `m/44'/118'/0'/0/0`) |
| `--version`       | Print version and exit |

---

## 📈 Example Output

```
✅ Found!
🔗 Address     : cosmos1gptx8g...
🔐 Private Key : 1f29a3...
🧠 Mnemonic    : curious airport vintage filter exhibit ...
🔁 Attempts    : 1,000,000
⚡ Speed       : 12,345.67 addr/sec
🧊 CPU         : 54.2°C
💾 Saved 1 result(s) to osmo_cpu_found.json
```

---

## 🔐 Entropy Strength and Mnemonic Word Count

When using `--mnemonic`, the entropy strength directly determines the number of words in the generated phrase:

| Entropy Bits | Mnemonic Words |
|--------------|----------------|
| 128          | 12             |
| 160          | 15             |
| 192          | 18             |
| 224          | 21             |
| 256          | 24             |

For example, `--strength 256 --mnemonic` will generate a 24-word phrase.

If `--mnemonic` is **not** used, the entropy still defines the size of the raw private key in bits.

---

## 🔎 Osmosis Address Scanner (`scan.py`)

This script scans all generated JSON files and checks each address for a **non-zero OSMO balance**.

- ✅ Works only with `osmo1...` addresses
- 🔍 Queries the Osmosis LCD endpoint for each address
- 💾 Saves found addresses with OSMO to `found_wallets/`

### 🔧 Example:

```bash
python3 scan.py
```

The script will:
- Find all `*.json` files with generated addresses
- Query each address via `https://lcd.osmosis.zone`
- Save matches in files like `found_wallets/found_from_<source>.json`

📌 This script is useful to **discover existing OSMO on random or recycled keys**.