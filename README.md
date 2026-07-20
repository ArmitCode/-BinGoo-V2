# 🔍 BinGoo v2 — Modernized Dorking Tool

> **Original Author:** [Hood3dRob1n](http://kaoticcreations.blogspot.com)  
> **Original Release:** ~2011  
> **Updated:** 2026  
> **License:** Use at your own risk

```
          __   _,--="=--,_   __
         /  \."    .-.    "./  \
        /  ,/  _   : :   _  \/` \
        \  `| /o\  :_:  /o\ |\__/
         `-'| :="~` _ `~"=: |
            \`     (_)     `/
     .-"-.   \      |      /   .-"-.
.---{     }--|  /,.-'-.,\  |--{     }---.
 )  (_)_)_)  \_/`~-===-~`\_/  (_(_(_)  (
(   ____  _        ____             _   )
 ) | __ )(_)_ __  / ___| ___   ___ | | (
(  |  _ \| | '_ \| |  _ / _ \ / _ \| |  )
 ) | |_) | | | | | |_| | (_) | (_) |_| (
(  |____/|_|_| |_|\____|\___/ \___/(_)  )
 )                                     (
'---------------------v2--By-Hood3dRob1n'
```

---

## 📖 What is BinGoo?

**BinGoo** is a pure Bash-based dorking and vulnerability analysis tool. It scrapes search results from **Google** and **Bing** using custom dorks, then analyzes the discovered links for common signs of **SQL Injection**, **LFI/RFI**, and other web vulnerabilities.

This is the **2026 modernization** of the original tool, keeping the legendary clean structure intact while making it work again in today's web landscape.

---

## ✨ What's New in v2

| Feature | v1 (2011) | v2 (2026) |
|---------|-----------|-----------|
| **Search Engine** | `lynx` (broken on modern sites) | `curl` with real browser headers |
| **User Agents** | Opera 12, IE 10, Firefox 15 | Chrome 126, Firefox 127 |
| **UA Rotation** | ❌ Static | ✅ Random per-request via `shuf` |
| **Rate Limiting** | ❌ None | ✅ Random delays (0.5–3s) |
| **Google Parsing** | `lynx -dump` (blocked) | `curl` + `grep -oP` regex |
| **Bing Parsing** | `lynx -dump` (broken) | `curl` + `grep -oP` regex |
| **SQLi Regexes** | 76 patterns | **95 patterns** (see below) |
| **LFI Regexes** | 7 patterns | **12 patterns** (see below) |
| **Dependencies** | `lynx` + `curl` | `curl` only |
| **`mktemp`** | Legacy syntax | Modern `XXXXXX` syntax |
| **`wc -l`** | `cut -d' '` (fragile) | `< file` redirection |
| **`--ssl`** | Deprecated flag | Removed |
| **Kill Process** | `cut -d' '` (buggy) | `awk '{print $2}'` |

---

## 🚀 Installation

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/BinGoo.git
cd BinGoo

# Make executable
chmod +x bingoo

# Install dependency (if not already installed)
sudo apt-get install curl bc    # Debian/Ubuntu
# or
sudo pacman -S curl bc          # Arch
```

### Optional Dependencies (for plugins)

| Tool | Purpose | Install |
|------|---------|---------|
| `nmap` | Digger recon plugin | `apt-get install nmap` |
| `sqlmap` | SQLi validation | `pip install sqlmap` |
| `fimap` | LFI validation | [SVN checkout](https://github.com/kurobeats/fimap) |
| `dig` / `host` / `nslookup` | DNS resolution | Usually pre-installed |

---

## 🎯 Usage

```bash
./bingoo
```

Then follow the interactive prompts:

```
Please select which option you would like to use:
1) Google
2) Bing
3) Bing Geo Dorker
4) Bing Shared Hosting Check
5) Digger Recon Tool
6) Analyze & Tools
```

### Single Dork Scan
```
→ Choose "Google" or "Bing"
→ Choose "Single Dork Scan"
→ Enter your dork: inurl:admin.php
→ Wait for results...
```

### Mass Dork Scan
```
→ Choose "Mass Scan w/Imported Dork List"
→ Provide path to dork file (e.g., dorks/sharedhosting.lst)
→ Results saved to g-links.txt or b-links.txt
```

### Analyze Links for Vulnerabilities
```
→ Choose "Analyze & Tools"
→ Select link source (Bing / Google / Both / Custom)
→ Tool tests each URL with `'` injection and regex matching
→ Results saved to:
    results/SQLi.results
    results/LFI.results
    results/possibles.results
```

---

## 🔬 Vulnerability Detection

### SQL Injection Regexes (95 patterns)

**Classic Errors:**
- `Warning: mysql_*()` — MySQL errors
- `Warning: pg_*()` — PostgreSQL errors
- `ORA-00921`, `ORA-01756` — Oracle errors
- `Microsoft JET Database Engine error` — Access/MS SQL
- `ODBC Error`, `JDBC error` — Driver errors
- `error in your SQL syntax`, `unexpected end of SQL command`

**Modern Errors (NEW in v2):**
- `mysqli_sql_exception` — MySQLi (PHP 7+)
- `PDOException` — PDO (PHP 5.1+)
- `SQLSTATE[` — PDO error codes
- `sqlite3_query()` — SQLite3
- `MariaDB server version` — MariaDB
- `syntax error at or near` — PostgreSQL
- `Fatal error: Uncaught Exception`

### LFI/RFI Regexes (12 patterns)

- `Warning: include(`, `Warning: require_once(`
- `function.include`, `function.require`
- `Disallowed Parent Path`, `include_path`
- `Warning: main(`
- `Warning: file_get_contents(` ⭐ *NEW*
- `Warning: fopen(` ⭐ *NEW*
- `Warning: readfile(` ⭐ *NEW*
- `failed to open stream` ⭐ *NEW*
- `No such file or directory` ⭐ *NEW*

---

## 🌍 Bing Geo Dorker

Target specific TLDs or country codes:

```
Examples: .com, .net, .org, .gov, .edu
          .uk, .ir, .ru, .cn, .de
```

Uses `dorks/site.lst` with `site:` operator automatically injected.

---

## 🏠 Bing Shared Hosting Check

Find other sites on the same IP/server that might have vulnerabilities:

```
→ Enter target IP or domain
→ Resolves DNS automatically
→ Runs sharedhosting.lst dorks against IP
```

---

## 🛡️ Anti-Detection Features

| Technique | How it works |
|-----------|--------------|
| **UA Rotation** | Randomly picks from 4 modern browser UAs per request |
| **Request Delays** | Random sleep 0.5–3s between pages |
| **Real Headers** | Sends `Accept`, `Accept-Language`, `Accept-Encoding`, `DNT`, etc. |
| **Compression** | `--compressed` flag for gzip/br support |
| **Referer Spoofing** | `http://www.google.com/q?=foobar` |

> ⚠️ **Note:** Google may still show CAPTCHA on heavy usage. Use Bing as fallback or rotate your IP/VPN.

---

## 📁 File Structure

```
BinGoo/
├── bingoo              ← Main script (this file)
├── README.md           ← You are here
├── dorks/
│   ├── sharedhosting.lst
│   └── site.lst
├── plugins/
│   ├── digger          ← Recon plugin
│   ├── SQLi            ← SQLi tools wrapper
│   ├── LFI             ← LFI tools wrapper
│   ├── pagefinder      ← Admin page finder
│   └── admin.lst       ← Wordlist for pagefinder
└── results/            ← Auto-created on first run
    ├── SQLi.results
    ├── LFI.results
    ├── possibles.results
    └── crash_dumped.results
```

---

## ⚠️ Known Issues & Tips

1. **Google CAPTCHA** — If you get no results from Google, your IP may be temporarily blocked. Use Bing until it clears.
2. **Best Practice** — Scan with Google → Analyze → Scan with Bing → Analyze. This minimizes blocks.
3. **Large Lists** — Files with 2000+ links are auto-split into 1000-line chunks and processed in parallel.
4. **CTRL+C** — Safe interrupt. Active work is dumped to `results/crash_dumped.results`.
5. **Plugins** — `plugins/digger`, `plugins/SQLi`, `plugins/LFI`, `plugins/pagefinder` are sourced dynamically. Make sure they exist.

---

## 🙏 Credits

- **Original Author:** Hood3dRob1n ([Blog](http://kaoticcreations.blogspot.com))
- **Original Tool:** BinGoo v1 (~2011)
- **Modernization:** 2026 Update — keeping the legendary Bash structure alive

> *"I decided I was going to do everything in bash to make things difficult on myself, well cause thats how I am :p"* — Hood3dRob1n

---

## 📜 Disclaimer

**This tool is for educational and authorized security testing purposes only.**  
The original author and contributors take **no responsibility** for any misuse.  
Always ensure you have **explicit permission** before testing any target.

```
Greetz to and from INTRA!
```
