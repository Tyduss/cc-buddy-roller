# buddy-roller

**English** | [中文](README.md)

A custom Claude Code Skill for **querying, filtering, and changing** your Claude Code Buddy pet.

Supports natural language input (Chinese & English), automatically brute-forces matching userIds, renders ASCII pet sprites with stat panels, and writes config for you.

**Supports both Bun (native install) and Node.js (npm install) runtimes** — automatically detects your environment and uses the correct hash algorithm, no manual config needed.

## Features

| Feature | Usage | Description |
|---------|-------|-------------|
| View current pet | `/buddy-roller` | Reads your `~/.claude.json` and displays your current pet |
| Query any userId | `/buddy-roller query:abc123` | View the pet for any given userId |
| Filter & change pet | `/buddy-roller legendary penguin crown shiny` | Searches for a matching userId and auto-replaces config |

## Installation

```bash
git clone https://github.com/Tyduss/cc-buddy-roller.git
cp -r cc-buddy-roller ~/.claude/skills/
```

Restart Claude Code and you're good to go.

## Usage Examples

### View current pet

```
/buddy-roller
```

Automatically reads the userID from `~/.claude.json` and displays your current pet sprite and stats.

### Query any userId

```
/buddy-roller query:ab54093b-xxxx-xxxx-xxxx
```

Read-only query — does not modify any config.

### Change pet

Supports natural language in both Chinese and English:

```
/buddy-roller legendary penguin, crown, shiny, ✦ eye
/buddy-roller legendary dragon wizard shiny
/buddy-roller --species cat --rarity epic --hat tophat
```

The Skill will:
1. Show your current pet first
2. After confirmation, start searching for a matching userId
3. Display the new pet's ASCII sprite + stats table
4. Auto-modify `~/.claude.json` (replace userID, delete companion block)
5. Prompt you to restart Claude Code to activate the new pet

### Available attributes

| Attribute | Values |
|-----------|--------|
| **Species** | duck, goose, blob, cat, dragon, octopus, owl, penguin, turtle, snail, ghost, axolotl, capybara, cactus, robot, rabbit, mushroom, chonk |
| **Rarity** | common (★), uncommon (★★), rare (★★★), epic (★★★★), legendary (★★★★★) |
| **Eye** | ·, ✦, ×, ◉, @, ° |
| **Hat** | none, crown, tophat, propeller, halo, wizard, beanie, tinyduck |
| **Shiny** | shiny |

You don't need to specify all attributes — just the ones you care about.

## Output Examples

### Legendary Capybara · tinyduck hat · ◉ eye

| Attribute | Value |
|-----------|-------|
| **Rarity** | ★★★★★ legendary |
| **Species** | Capybara |
| **Eye** | ◉ |
| **Hat** | tinyduck |
| **Shiny** | No |
| **Stats** | DEBUGGING 63 / PATIENCE **100** / CHAOS 69 / WISDOM 88 / SNARK 52 |

```
    ,>
  n______n
 ( ◉    ◉ )
 (   oo   )
  `------´
```

### Epic Dragon · crown hat · · eye

| Attribute | Value |
|-----------|-------|
| **Rarity** | ★★★★ epic |
| **Species** | Dragon |
| **Eye** | · |
| **Hat** | crown |
| **Shiny** | No |
| **Stats** | DEBUGGING 57 / PATIENCE 51 / CHAOS 50 / WISDOM **100** / SNARK 39 |

```
   \^^^/
  /^\  /^\
 <  ·  ·  >
 (   ~~   )
  `-vvvv-´
```

### Common Penguin · ✦ eye · shiny

| Attribute | Value |
|-----------|-------|
| **Rarity** | ★ common |
| **Species** | Penguin |
| **Eye** | ✦ |
| **Hat** | none |
| **Shiny** | Yes |
| **Stats** | DEBUGGING 76 / PATIENCE 6 / CHAOS 8 / WISDOM 33 / SNARK 10 |

```
  .---.
  (✦>✦)
 /(   )\
  `---´
```

## Compatibility

| Install method | Runtime | Hash algorithm |
|----------------|---------|----------------|
| npm install | Node.js | FNV-1a |
| Native install | Bun | Bun.hash |

The script auto-detects your runtime and picks the correct hash algorithm. **Note:** Bun and Node produce different hash results — make sure the runtime matches your Claude Code installation method.

## File Structure

```
cc-buddy-roller/
├── README.md        # Chinese docs
├── README.en.md     # English docs
├── SKILL.md         # Skill definition (read by Claude Code)
└── find-buddy.js    # Search/query script (compatible with Node.js & Bun)
```

## How It Works

Claude Code's Buddy pet is deterministically generated from the `userID` field in `~/.claude.json` via a seeded PRNG. This Skill brute-forces a userId that matches your desired attribute combination, then replaces the value in your config to get the pet you want.

## Notes

- Legendary + shiny combinations are extremely rare (~1 in 8.64 million), searches may take several minutes
- Results are computed in the background — you can wait while the search runs
- You must restart Claude Code after replacing the userId for changes to take effect
- The `companion` block is deleted so a fresh name and personality are generated

## License

Apache-2.0
