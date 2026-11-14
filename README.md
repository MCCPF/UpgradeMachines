# ⚙️ UpgradeMachines

> 🚀 A **Paper / Purpur** plugin (Minecraft Java) that lets players upgrade **Furnaces, Blast Furnaces, Smokers, Hoppers, Dispensers, Droppers and Crafters** to make them work faster.

![Paper](https://img.shields.io/badge/Paper%20%2F%20Purpur-26.2%2B-blue) ![Java](https://img.shields.io/badge/Java-25%2B-orange) ![Vault](https://img.shields.io/badge/Vault-optional-lightgrey)

---

## ✨ Features

- 📈 **Upgrades only raise the rate.** No new items, no new recipes, no other changes to vanilla mechanics.
- 🧠 **The level lives on the block itself** (PersistentDataContainer). No external database, and it always moves and loads with the world.
- 📦 **Levels survive breaking.** Break an upgraded block and the item keeps its level. Place it again and the level comes back.
- 💰 **Configurable prices**: money (through Vault), items, or both.
- 🖱️ **Three confirm styles**: a chest-style GUI, an action-bar prompt, or a native Minecraft Dialog.
- 🎨 **Fully customisable text**, with placeholders, `&` colour codes, `&#RRGGBB` hex colours and Roman numeral levels.

### 🔧 What each machine gets

| Machine | Upgrade effect | Default levels |
|---|---|---|
| 🔥 **Furnace / Blast Furnace / Smoker** | Smelts several items per cycle (batch smelting). Time and fuel per cycle stay vanilla; only ingredients scale, so higher levels save fuel. | 1 → 8 / 16 / 24 / 32 items |
| 🪣 **Hopper** | Moves extra items per transfer (vanilla: 1 item every 8 ticks). | 1 → 2 / 3 / 4 / 5 / 7 items |
| 🏹 **Dispenser** | Shoots out extra items per activation. | 1 → 2 / 3 / 4 / 5 / 6 items |
| 📤 **Dropper** | Drops extra items per activation (same mechanism as the Dispenser, separate settings). | 1 → 2 / 3 / 4 / 5 / 6 items |
| 🛠️ **Crafter** | Crafts extra times per trigger. | 1 → 2 / 3 / 3 / 4 / 5 crafts |

---

## 📋 Requirements

- 🖥️ **Purpur / Paper 26.2+** (tested on Purpur 26.2-stable). Plain Spigot is not supported, because the plugin uses `CrafterCraftEvent`.
- ☕ **Java 25+** (the runtime Purpur 26.2 / 26.3 needs).
- 🧰 **Maven** to build.
- 💵 **Vault** *(optional)*: needed only for money prices, together with an economy plugin such as EssentialsX. Without Vault, money prices are skipped automatically and everything else still works.

---

## 🏗️ Building

```bash
mvn clean package
```

📁 The jar is written to `target/upgrademachines-<version>.jar`. Put it in your server's `plugins/` folder and restart.

---

## 🎮 How to use it in game

### ⌨️ Commands

| Command | Description | Permission |
|---|---|---|
| `/upgrade info` | Shows the level of the block you are looking at, and the price of the next level. | everyone |
| `/upgrade reload` | Reloads `config.yml`. | `upgrademachine.admin` |

### ⬆️ Upgrading a block

1. 🧍 **Sneak + right-click** a furnace, hopper, dispenser, dropper or crafter.
2. 🪟 A **confirm screen** opens. Its title already shows the result and the price (for example `Furnace 16 items per cycle - 500`), and the middle item lists current level → new level, effect and price.
3. ✅ **Confirm** takes the money or items and upgrades the block. ❌ **Cancel** just closes the screen, and costs nothing.

- 🙌 No wand or special item is needed. An empty hand or a non-placeable item (sword, food, tool…) opens the screen.
- 🧱 **Holding something placeable in your main hand places it instead.** Blocks, redstone, string, seeds, water or lava buckets and so on are placed like vanilla, so you can still sneak to build against machines.
  - 💡 If the upgrade price is a block item (for example `IRON_BLOCK`), you don't need to hold it. The plugin takes it from anywhere in your inventory.
- 🔐 Needs the `upgrademachine.use` permission (granted to everyone by default).
- 🖐️ A normal right-click (without sneaking) opens and uses the block like vanilla.
- ⛔ There is no downgrade.

### 🖼️ Confirm styles (`gui.style`)

| Style | What it does |
|---|---|
| 📦 `inventory` *(default)* | A 27-slot confirm / cancel GUI, as described above. |
| 💬 `actionbar` | No GUI. A short prompt appears in the action bar (`gui.actionbar.prompt-format`). Sneak + right-click the **same block** again within `gui.actionbar.timeout-seconds` (default 6) to confirm. To cancel, just don't click again. It is plain action-bar text with no frame or background (you'd need your own resource pack for that). |
| 🪧 `dialog` | Opens a **native Minecraft Dialog** (the game's Dialogs feature, not an inventory hack), with its own frame and real **Confirm** / **Cancel** buttons, and no resource pack needed. Title, body and buttons are set under `gui.dialog.*`. The player's client must support Dialogs. |

📱 `actionbar` and `dialog` are both easier on mobile, where tapping small grid slots in the `inventory` GUI is awkward.

#### 🪧 Dialog options (`gui.dialog`)

| Key | Description |
|---|---|
| `title-format` | Dialog title. |
| `body` | Body lines, joined into one block of text so the line spacing stays tight. |
| `confirm-label` / `cancel-label` | Button labels. `\n` makes a second line (the default confirm label shows the level and price). |
| `button-layout` | `side-by-side` (classic confirm box), `stacked` (confirm above cancel), or `separated` *(default: cancel moved to the very bottom)*. |
| `confirm-button-width` / `cancel-button-width` | Button width in Dialog pixels (default 200). Raise it if a longer label gets cut off. |

---

## 📦 Breaking and placing upgraded blocks

⛏️ Breaking a block with level ≥ 1 drops an item with a **custom name and lore** that show its real stats, for example:

```
Dropper → Lv.III
Drops 4 items at a time
```

- 🧬 The level is stored **inside the item** (not just in its text). Place it anywhere and the new block gets the same level straight away.
  - 📉 If `max-level` has since been lowered below that level, the level is clamped to the new max.
- 🪨 Level 0 blocks drop as normal vanilla items.
- 🎨 **Creative mode:** vanilla drops nothing when you break a block in Creative, which would lose the level. So for blocks with level ≥ 1, the plugin puts the named item straight into the player's inventory. Level 0 blocks still just disappear, as in vanilla.
- 🏷️ **The block's own GUI title** (for example when you open a crafter to craft) changes as soon as you upgrade, with no need to break and replace it. It uses the same format as `item-name-format`.
- ⚠️ **Limitation:** only normal player breaking (`BlockBreakEvent`) is covered. Blocks destroyed by explosions or other means drop vanilla items and lose their level.

---

## 🛠️ Configuration (`config.yml`)

Each machine has a `display-name` (shown in menus, messages and dropped items), a `max-level`, a list of rates per level, and a `price`. **Index 0 = not upgraded = no price.**

```yaml
furnace:
  display-name: "Furnace"                      # any text you like
  effect-format: "{rate} items per cycle"      # becomes {effect}; {rate} comes from the list below
  max-level: 4
  # index 0 = not upgraded (1 item, like vanilla), levels 1-4 = 8/16/24/32
  smelt-batch-size: [1, 8, 16, 24, 32]
  price:
    money: [0, 500, 1500, 3000, 6000]          # money to reach that level (needs Vault)
    item:
      material: IRON_INGOT                     # set to "" for no item price
      amounts: [0, 8, 16, 32, 64]
```

| Machine key | Rate list |
|---|---|
| `furnace` | `smelt-batch-size` (items per cycle) |
| `hopper` | `extra-transfers` (extra items per transfer) |
| `dispenser` / `dropper` | `extra-items` (extra items per activation) |
| `crafter` | `cooldown-reduction-ticks` (extra crafts per trigger) |

- 💸 Money and item prices can be used together (the player must pay both), or set one to `0` / `""` to use only the other.
- 📝 Every machine has its own `effect-format`. It takes one placeholder, `{rate}`, accepts `&` colours, and its result replaces `{effect}` everywhere (GUI title, lore, `/upgrade info`, dropped items).
- 🔄 Edit the numbers and run `/upgrade reload`. No rebuild needed.

### 🏷️ Names per block type (`display-names`)

Some machines cover several vanilla blocks. Right now that's `furnace`, which covers Furnace, Blast Furnace and Smoker with the same rates and prices. To give each its own **name** while sharing rates and prices, use `display-names`. You don't have to set every type: missing ones fall back to `display-name`.

```yaml
furnace:
  display-name: "Furnace"          # default / fallback
  display-names:
    FURNACE: "Furnace"
    BLAST_FURNACE: "Blast Furnace"
    SMOKER: "Smoker"
```

🔑 Keys are Bukkit Material names in upper case (`FURNACE`, `BLAST_FURNACE`, `SMOKER`). The name automatically shows up everywhere `{name}` is used.

### 🪜 Names per level (level lists)

Both `display-name` and each value in `display-names` can be **one string** (used for every level) or a **list per level**. The list index is the level, like every other list in the file (index 0 = not upgraded, index 1 = Lv. 1, …). The bundled config already does this for every machine:

```yaml
furnace:
  display-names:
    FURNACE: ["Furnace", "Copper Furnace", "Iron Furnace", "Gold Furnace", "Diamond Furnace"]

hopper:
  display-name: ["Hopper", "Copper Hopper", "Iron Hopper", "Gold Hopper", "Diamond Hopper", "Netherite Hopper"]
```

📏 The list should have `max-level + 1` entries. If a level has no name (list too short, or `""`), the fallback order is: `display-names.<MATERIAL>` → `display-name` → the plugin's built-in default. The name is never empty.

### 🏛️ Roman numeral levels (`{level-roman}` / `{current-roman}` / `{max-roman}`)

Besides `{level}` / `{current}` / `{max}` (1, 2, 3…), each has a Roman numeral twin: `{level-roman}` / `{current-roman}` / `{max-roman}` (I, II, III, IV, V…).

- 🔢 **The bundled defaults already use Roman numerals** everywhere "Lv." appears.
- ↩️ To switch back to plain numbers, use `{level}` / `{current}` / `{max}` in `gui.info-item.name-format`, `gui.info-item.lore` and `gui.item-name-format`.
- 0️⃣ Level 0 has no Roman numeral, so it shows as `0`.
- 🧑‍💻 `/upgrade info` and the chat messages for upgrade success or failure don't go through the templates. Changing those back to plain numbers needs a code change (`UpgradeManager.toRoman(...)` in `UpgradeCommand.java` / `UpgradeConfirmGuiListener.java`).

### 🎨 Confirm screen and dropped item text (`gui:`)

The `gui:` section (at the top of the same file, before `furnace:`) controls the sneak + right-click confirm screen, and the name and lore of items dropped from upgraded blocks. Every text field is free to edit and supports these placeholders:

| Placeholder | Meaning |
|---|---|
| `{name}` | Machine name at that level (from `display-name` / `display-names`). |
| `{level}` / `{level-roman}` | Target level: number / Roman numeral. |
| `{current}` / `{current-roman}` | Current level: number / Roman numeral. |
| `{max}` / `{max-roman}` | Max level: number / Roman numeral. |
| `{rate}` | The raw number for the target level, e.g. just `16` with no text around it. |
| `{effect}` | The full effect text from that machine's `effect-format`, e.g. `16 items per cycle`. |
| `{price}` | Short price summary, e.g. `500 + 8x IRON_INGOT`, or the free text. Numbers and item names inside it have no forced colour, so a colour placed before `{price}` applies (e.g. `&e{price}` is all yellow). The free text and the `+` separator have their own colours: `gui.price-free` and `gui.price-separator`. |

🌈 **Colours:** `&` codes (`&a` green, `&c` red, `&l` bold…) or **hex as `&#RRGGBB`** (e.g. `&#4D60FF`), mixed freely in one string, including inside `display-name` / `display-names`.

🧱 Button and filler `material` values take any Bukkit Material name. The middle info item always shows the block being upgraded.

```yaml
gui:
  title-format: "&8{name} &f{effect} &7- {price}"
  info-item:
    name-format: "&b&l{name} &f→ &bLv.{level-roman}"
    lore:
      - "&7Level: &f{current-roman} &7→ &a{level-roman}&7/{max-roman}"
      - "&7Effect: &f{effect}"
      - ""
      - "&7Price: &f{price}"
  confirm-button:
    material: LIME_STAINED_GLASS_PANE
    name: "&a&lConfirm upgrade"
    lore: ["&7Click to confirm the upgrade"]
  cancel-button:
    material: RED_STAINED_GLASS_PANE
    name: "&c&lCancel"
    lore: ["&7Close without paying anything"]
  filler:
    material: GRAY_STAINED_GLASS_PANE
  item-name-format: "&b{name} &f→ &bLv.{level-roman}"   # dropped item name
  item-lore-format: ["&7{effect}"]                       # dropped item lore
```

📌 In `item-name-format` / `item-lore-format`, `{level}` and `{current}` (and their Roman versions) are the same value: the item's own level.

> 🌏 The bundled `config.yml` ships with Thai text. The examples above are translated so you can see what each field does.

---

## ⚠️ Known limitations

- 🏹 **Dispenser / Dropper:** the extra items are simply dropped in front of the block. They don't copy each item's special dispense behaviour (water buckets, spawn eggs, arrows…). The first item still behaves like vanilla; the bonus items come out as plain drops.
- 🛠️ **Crafter:** a newer block (1.21+) whose Paper API is less settled than the others, so the plugin crafts again rather than shortening the block's internal cooldown. Test it on your real server version before going live.
- 💵 **Money prices** need Vault plus an economy plugin (such as EssentialsX), otherwise they are skipped. The plugin isn't tied to any particular economy plugin.
- 🧪 Mainly tested on **Purpur 26.2-stable** (compiled against `paper-api:26.2.build.121-stable`, Java 25). The code only uses standard API that hasn't changed between 26.2 and 26.3-alpha, so Purpur 26.3 should work, but retest on a real 26.3 server as soon as a stable build is out (see `HANDOFF.md`).
