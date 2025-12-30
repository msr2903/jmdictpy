# JMdictPy

Fast Python wrapper for [jmdict-simplified](https://github.com/scriptin/jmdict-simplified) with SQLite backend and automatic updates.

## Features

- 🚀 **Fast lookups** - SQLite with indexed queries (sub-millisecond)
- 🔄 **Auto-updates** - Checks for new releases on initialization
- 🌍 **Multi-language** - Supports 10 languages (eng, ger, rus, hun, dut, spa, fre, swe, slv)
- 📦 **Zero config** - Downloads and builds database automatically
- 🔍 **Full-text search** - FTS5 for meaning/gloss searches
- 📚 **Complete coverage** - JMdict, JMnedict (names), Kanjidic (characters)

## Installation

```bash
pip install jmdictpy
```

Or install from source:

```bash
git clone https://github.com/your-username/jmdictpy
cd jmdictpy
pip install -e .
```

## Quick Start

```python
from jmdictpy import JMDict

# Initialize (downloads ~25MB on first run)
jmd = JMDict()

# Lookup by kanji/kana
result = jmd.lookup("食べる")
for entry in result.entries:
    print(entry)
# 食べる【たべる】: to eat; to live on

# Lookup by reading
entries = jmd.lookup_by_reading("たべる")

# Search by meaning (full-text search)
entries = jmd.search("to eat")

# Lookup kanji character
char = jmd.lookup_character("食")
print(f"{char.literal}: {char.meanings}")
# 食: ['eat', 'food']

# Lookup names (JMnedict)
names = jmd.lookup_name("田中")
for name in names[:3]:
    print(name)
# 田中【たなか】 - Tanaka (surname, place)
# 田中【たなた】 - Tanata (surname)
# 田中【たんか】 - Tanka (surname)
```

## API Reference

### JMDict Class

```python
JMDict(
    db_path=None,       # Custom database path (default: ~/.jmdictpy/jmdict.db)
    language="eng",     # Translation language
    auto_update=True,   # Check for updates on init
    memory_mode=False,  # Load DB into RAM for faster queries
    common_only=False,  # Only download common words
)
```

### Methods

| Method | Description |
|--------|-------------|
| `lookup(query)` | Main search - finds entries by kanji, kana, or meaning |
| `lookup_by_reading(reading)` | Search by kana reading only |
| `lookup_by_meaning(meaning)` | Exact match on translation |
| `search(query)` | Full-text search on meanings |
| `lookup_by_id(id)` | Get entry by JMdict ID |
| `lookup_name(text)` | Search JMnedict for names |
| `lookup_character(char)` | Get Kanjidic info for a kanji |
| `check_for_updates()` | Check if new version available |
| `update()` | Download and install latest version |

### Data Models

```python
@dataclass
class Entry:
    id: str
    kanji: list[Kanji]      # Kanji writings
    kana: list[Kana]        # Kana readings
    senses: list[Sense]     # Meanings/translations

@dataclass
class Sense:
    part_of_speech: list[str]
    glosses: list[Gloss]    # Translations
    fields: list[str]       # Field of use (math, comp, etc.)
    dialect: list[str]
    misc: list[str]

@dataclass
class NameEntry:
    id: str
    kanji: list[Kanji]
    kana: list[Kana]
    translations: list[dict]
    
    # Properties
    romaji: str             # "Tanaka"
    name_types: list[str]   # ["surname", "place"]

@dataclass
class LookupResult:
    entries: list[Entry]        # JMdict entries
    names: list[NameEntry]      # JMnedict names
    characters: list[Character] # Kanjidic characters
```

## Configuration

### Environment Variables

| Variable | Description |
|----------|-------------|
| `JMDICTPY_DATA_DIR` | Custom data directory (default: `~/.jmdictpy`) |

### Languages

Supported language codes (ISO 639-2):

- `eng` - English
- `ger` - German
- `rus` - Russian
- `hun` - Hungarian
- `dut` - Dutch
- `spa` - Spanish
- `fre` - French
- `swe` - Swedish
- `slv` - Slovenian
- `all` - All languages

## Updates

JMdictPy checks for updates from [jmdict-simplified releases](https://github.com/scriptin/jmdict-simplified/releases) automatically.

```python
# Check current version
print(jmd.version)  # 3.6.1+20251229123436

# Check for updates
local, remote, available = jmd.check_for_updates()

# Update to latest
jmd.update()
```

## License

MIT License

Dictionary data from [JMdict](http://www.edrdg.org/jmdict/j_jmdict.html) by the Electronic Dictionary Research and Development Group, used under [EDRDG License](http://www.edrdg.org/edrdg/licence.html).
