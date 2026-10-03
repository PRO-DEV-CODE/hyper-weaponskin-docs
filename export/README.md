# Export

## อยากทำอะไร ใช้ตัวไหน

| อยากทำ | ใช้ export | ฝั่ง | มีใครใช้อยู่แล้วในเซิร์ฟ |
| --- | --- | --- | --- |
| เปิดเมนูสกินจากปุ่มใน UI ของตัวเอง | `OpenSkinMenu` / `openmenu` | client | Hyper_Inventory — ปุ่ม "สกิน" ในเมนูคลิกขวาของอาวุธ |
| ปิดเมนูสกินจากโค้ดอื่น | `CloseSkinMenu` / `closemenu` | client | — |
| เช็คว่าอาวุธชิ้นนี้ใส่สกินอะไรอยู่ | `GetEquippedSkin` | client | — |
| เช็คว่าผู้เล่นมีสกินตัวนี้ไหม | `IsSkinOwned` | client | — |
| อ่านแคตตาล็อกอาวุธ/สกิน/ตำแหน่งทั้งหมด | `getconfig` | client | Hyper_Skinpos — เครื่องมือจูนตำแหน่ง prop |
| อ่านรายการสกินของอาวุธหนึ่งหมวด | `getskininfomation` | client | — |
| ยืมมือ ped ไปเสียบ prop ของตัวเอง | `SuspendProps` / `ResumeProps` | client | Hyper_Skinpos — หยุดเสียบตอนจูนตำแหน่ง |
| อ่านสกินที่ผู้เล่นใส่อยู่ (ฝั่ง server) | `GetPlayerSkins` / `getPlayerSkins` | server | — |
| แจกไอเทมปลดล็อกสกิน (กาชา, ภารกิจ, ร้าน) | `GrantSkin` | server | — |
| ยึดไอเทมปลดล็อกสกินคืน | `RevokeSkin` | server | — |

## Export ฝั่ง Client

### `OpenSkinMenu(mainName?)` · alias `openmenu`

เปิดเมนูสกิน ส่งชื่ออาวุธเข้าไปด้วยเพื่อให้เมนูเด้งไปที่หมวดและระดับของอาวุธชิ้นนั้นเลย ไม่ส่งก็เปิดที่หมวดแรก

```lua
exports["Hyper_Weaponskin"]:OpenSkinMenu("WEAPON_BOTTLE_C")
exports["Hyper_Weaponskin"]:openmenu("WEAPON_BOTTLE_C")   -- ชื่อเดิม ใช้ได้เหมือนกัน
```

เมนูไม่เปิดซ้อนตัวเอง เรียกตอนเปิดอยู่แล้วจะไม่ทำอะไร

### `CloseSkinMenu()` · alias `closemenu`

ปิดเมนู คืนกล้อง คืนอาวุธที่ถืออยู่ก่อนเปิด และคืนเข็มทิศ

```lua
exports["Hyper_Weaponskin"]:CloseSkinMenu()
```

### `GetEquippedSkin(mainName)`

คืนลำดับสกินที่ใส่อยู่บนอาวุธชิ้นนั้น หรือ `nil` ถ้าไม่ได้ใส่

```lua
local idx = exports["Hyper_Weaponskin"]:GetEquippedSkin("WEAPON_MACHETE_A")
if idx then print("ใส่สกินลำดับ " .. idx .. " อยู่") end
```

### `IsSkinOwned(mainName, skinIdx)`

คืน `true` ถ้าผู้เล่นมีไอเทมปลดล็อกสกินชิ้นนั้น อ่านจาก snapshot ล่าสุดที่ server ส่งมา

```lua
if exports["Hyper_Weaponskin"]:IsSkinOwned("WEAPON_BOTTLE_A", 3) then
    -- มีสกินลำดับ 3 ของหมวดปากฉลาม
end
```

### `getconfig()`

คืนตาราง `Shared` ทั้งก้อน — มี `Families`, `Weapons`, `Positions`, `TierColor`, `Branding` ครบ ใช้ตอนต้องการไล่แคตตาล็อกเองโดยไม่ต้อง copy config

```lua
local shared = exports["Hyper_Weaponskin"]:getconfig()
for _, fam in ipairs(shared.Families) do
    print(fam.key, fam.label, #fam.levels)
end
```

### `getskininfomation(key)`

คืนรายการสกินของอาวุธหนึ่งตัว รับได้ทั้งลำดับใน `Shared.Weapons` และชื่ออาวุธ

```lua
local skins = exports["Hyper_Weaponskin"]:getskininfomation("WEAPON_KNIFE_A")
local skins2 = exports["Hyper_Weaponskin"]:getskininfomation(1)
```

### `SuspendProps()` / `ResumeProps()`

หยุดและสั่งเสียบ prop สกินใหม่ ใช้ตอน resource อื่นต้องการเสียบ prop ของตัวเองเข้ามือเดียวกัน ไม่งั้นจะได้ prop สองตัวซ้อนกัน ไม่ยุ่งกับสกินที่บันทึกไว้ในฐานข้อมูล

```lua
exports["Hyper_Weaponskin"]:SuspendProps()
-- เสียบ prop ของตัวเองแล้วทำอะไรก็ว่าไป
exports["Hyper_Weaponskin"]:ResumeProps()
```

## Export ฝั่ง Server

### `GetPlayerSkins(source)` · alias `getPlayerSkins`

คืนสกินที่ผู้เล่นใส่อยู่ทั้งหมดและระดับที่ปักดาวไว้

```lua
local data = exports["Hyper_Weaponskin"]:GetPlayerSkins(source)
-- data.equipped = { ["WEAPON_BOTTLE_B"] = 4, ... }
-- data.favorite = { ["WEAPON_BOTTLE_B"] = true, ... }
```

### `GrantSkin(source, itemName, count?)`

แจกไอเทมปลดล็อกสกินให้ผู้เล่น คืน `true` เมื่อสำเร็จ และส่ง log เข้า Discord พร้อมชื่อ resource ที่เรียก

```lua
exports["Hyper_Weaponskin"]:GrantSkin(source, "skin_bottle_songkran", 1)
```

เหมาะกับกาชา ภารกิจ ร้านค้า หรือระบบรางวัล — ให้ของแล้วสกินจะปลดล็อกในเมนูทันที

### `RevokeSkin(source, itemName, count?)`

ยึดไอเทมปลดล็อกคืน ส่ง log เหมือนกัน

```lua
exports["Hyper_Weaponskin"]:RevokeSkin(source, "skin_bottle_songkran", 1)
```

## Resource ที่เชื่อมอยู่

### เรียก Hyper_Weaponskin

| Resource | ใช้อะไร | ทำอะไร |
| --- | --- | --- |
| Hyper_Inventory | `openmenu` | ปุ่ม "สกิน" ในเมนูคลิกขวาของอาวุธ — ปิดกระเป๋าแล้วเปิดเมนูสกินที่อาวุธชิ้นนั้น |
| Hyper_Skinpos | `getconfig`, `SuspendProps`, `ResumeProps` | เครื่องมือจูนออฟเซ็ต prop ในมือ อ่านแคตตาล็อกและสั่งหยุดเสียบระหว่างจูน |

### ถูก Hyper_Weaponskin เรียก

| Resource | ใช้อะไร | ทำอะไร |
| --- | --- | --- |
| Hyper_Inventory | `ApplyCustomImage`, `ResetCustomImage`, `SetSkinnable` | เปลี่ยน/คืนไอคอนอาวุธในกระเป๋า และบอกว่าอาวุธตัวไหนมีปุ่ม "สกิน" |
| Hyper_Notifyall | `sendNotify` | ข้อความแจ้งเตือนมุมขวาบน (มี `esx:showNotification` เป็นตัวสำรอง) |
| Hyper_Compass | `setCompassVisible` | ซ่อนแถบเข็มทิศตอนเมนูเปิด เพราะมันอยู่ตรงเดียวกับป้ายหัวหน้าต่าง |
| Hyper_Discordlogs | `CreateLog` | ส่ง log การใส่สกิน การแจก/ยึด และเหตุผิดปกติเข้า Discord |
| Hyper_Check | protected.lua, vault.lua | กันยิง event ซ้ำ และส่งไฟล์ฝั่ง client ตอนรันไทม์ |

ทุกการเรียกออกห่อด้วย `pcall` และเช็คสถานะ resource ก่อน — resource ปลายทางไม่ได้รันก็ยังใช้เมนูได้ตามปกติ

## Event

| Event | ทิศทาง | payload | ใช้ทำอะไร |
| --- | --- | --- | --- |
| `Hyper_Weaponskin:server:rpc` | client → server | `reqId, name, payload` | ช่องทางเดียวของทุกคำขอ: `menu.open`, `state.sync`, `state.roster`, `skin.equip`, `weapon.favorite`, `effect.broadcast` |
| `Hyper_Weaponskin:client:rpcResult` | server → client | `reqId, result` | คำตอบของ RPC ข้างบน |
| `Hyper_Weaponskin:client:loadEquipped` | server → client | `equipped, favorite` | ส่งสถานะตอน login และตอนแอดมินสั่ง `/reloadskin` |
| `Hyper_Weaponskin:client:remoteEquipped` | server → ทุก client | `serverId, equipped` | ทะเบียนสกินของผู้เล่นคนหนึ่ง ให้ทุกเครื่องเสียบ prop ตาม |
| `Hyper_Weaponskin:client:incomingEffect` | server → เหยื่อ | `{ kind, FRAME, SOUND }` | เฟรมเต็มจอและเสียงตอนโดนตี/โดนฆ่า |
| `Hyper_Weaponskin:Notify` | server → client | `msg, kind` | ข้อความแจ้งเตือนจากฝั่ง server |

## คำสั่ง

| คำสั่ง | ใคร | ทำอะไร |
| --- | --- | --- |
| `/weaponskin` | ทุกคน | เปิดเมนูสกิน |
| `/giveskin <id> <ชื่อไอเทม> [จำนวน]` | แอดมิน | แจกไอเทมปลดล็อกสกิน |
| `/takeskin <id> <ชื่อไอเทม> [จำนวน]` | แอดมิน | ยึดไอเทมปลดล็อกคืน |
| `/reloadskin [id]` | แอดมิน | โหลดสกินของผู้เล่นจากฐานข้อมูลใหม่แล้วส่งให้ client |

## ข้อควรรู้

- export ฝั่ง client ทั้งหมดอยู่ในไฟล์ที่ส่งผ่าน Code Vault ของ `Hyper_Check` — ต้องให้ `Hyper_Check` รันก่อน ไม่งั้น export จะยังไม่มี
- ชื่อ export แบบเก่า (`openmenu`, `closemenu`, `getconfig`, `getskininfomation`, `getPlayerSkins`) คงไว้ให้ resource เดิมที่ยังเรียกอยู่ โค้ดใหม่ควรใช้ชื่อแบบใหม่
- `GrantSkin` แจก "ไอเทมปลดล็อก" ไม่ใช่ "ใส่สกินให้" — ผู้เล่นยังต้องเปิดเมนูกดใส่เอง
