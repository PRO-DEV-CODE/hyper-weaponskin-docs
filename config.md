# การตั้งค่า

## ไฟล์ตั้งค่า

| ไฟล์ | ฝั่ง | เก็บอะไร |
| --- | --- | --- |
| `config/config.shared.lua` | shared | ชื่อตารางฐานข้อมูล, กันโกง/จำกัดความถี่, ชื่อหัวหน้าต่าง, path รูปไอเทม |
| `config/config.client.lua` | client | ธีมสี, ปุ่มและคำสั่ง, กล้องพรีวิว, สะพานกระเป๋า, สกินของผู้เล่นคนอื่น, คูลดาวน์ตี/ฆ่า |
| `config/config.language.lua` | shared | ข้อความแจ้งเตือนและข้อความบน UI ทั้งหมด |
| `config/config.positions.lua` | shared | ออฟเซ็ตและมุมของ prop สกินในมือ แยกต่อหมวดอาวุธ |
| `config/config.effects.lua` | shared | พาร์ทิเคิลดิบ, ออร่าตอนถืออาวุธ, ชุดเฟรม+เสียงของแต่ละธีม |
| `config/config.skins.lua` | shared | แคตตาล็อกสกิน, หมวดอาวุธ, ตารางออร่าตามระดับ |
| `config/config.log.lua` | server | เปิด/ปิด log, กลุ่มแอดมิน, ชื่อคำสั่งแอดมิน |

## ค่าที่ปรับบ่อย

### ธีมสีของเมนู

```lua
-- config/config.client.lua
Config.Theme = "seagreen"
-- เลือกได้: seagreen (ค่าเริ่มต้น), ember, azure, violet, crimson, amber, slate
```

### ปุ่มและคำสั่ง

```lua
-- config/config.client.lua
Config.Keys = {
    open = "",            -- ปุ่มลัดเปิดเมนู (ว่าง = ไม่ผูกปุ่ม ผู้เล่นตั้งเองได้ที่ FiveM Keybinds)
}
Config.Commands = {
    open = "weaponskin",  -- คำสั่งแชท /weaponskin
}
```

### สกินของผู้เล่นคนอื่น

```lua
-- config/config.client.lua
Config.RemoteSkins = {
    Enable           = true,
    render_distance  = 150.0,  -- เมตร ไกลกว่านี้ไม่เสียบ prop
    scan_interval_ms = 350,    -- ถี่แค่ไหนที่ไล่เช็คว่าใครควักอาวุธอะไรอยู่
    hide_interval_ms = 200,    -- ถี่แค่ไหนที่กดอาวุธจริงของคนอื่นให้ล่องหนซ้ำ
    idle_effects     = true,   -- เห็นออร่าของคนอื่นไหม
    idle_distance    = 80.0,   -- ระยะที่ยังยิงออร่าให้คนอื่น
}
```

### ออร่าตามระดับอาวุธ

```lua
-- config/config.skins.lua
Shared.IdleByLevel = true   -- true = ทุกสกินยิงออร่าตามระดับอาวุธเสมอ

Shared.LevelIdle = {
    [0] = FX.IDLE_LV1,   -- WEAPON_xxx_A
    [1] = FX.IDLE_LV2,   -- WEAPON_xxx_B
    [2] = FX.IDLE_LV3,
    [3] = FX.IDLE_LV4,
    [4] = FX.IDLE_LV5,   -- ระดับ +5 / +6 ใช้ชุดสูงสุดที่ตั้งไว้
}
```

สกินที่ตั้ง `IDLE = FX.IDLE_<ธีม>` ไว้จะยิงซ้อนเพิ่มอีกชุด ส่วน `IDLE = false` คือปิดออร่าธีมของสกินนั้น (ออร่าตามระดับยังอยู่)

### กันโกงและจำกัดความถี่

```lua
-- config/config.shared.lua
Shared.Security = {
    equip_cooldown_ms         = 250,    -- เว้นระยะระหว่างการกดใส่สกิน
    effect_cooldown_ms        = 250,    -- เว้นระยะระหว่างการยิงเอฟเฟกต์
    require_item_ownership    = true,   -- ต้องมีไอเทมปลดล็อกจริงถึงใส่ได้
    effect_max_distance       = 150.0,  -- เอฟเฟกต์ต้องยิงใส่เป้าหมายในระยะนี้
    effect_victim_cooldown_ms = 400,    -- เว้นระยะต่อเหยื่อหนึ่งคน
}
```

### กล้องพรีวิว

```lua
-- config/config.client.lua
Config.Preview = {
    cam_distance       = 3.4,
    cam_height         = 1.0,
    cam_target_z       = 0.6,   -- เล็งระดับสะโพกแทนหน้าอก
    cam_fov            = 42.0,
    rotate_sensitivity = 0.4,
    zoom_min           = 2.4,
    zoom_max           = 5.0,
}
```

### คูลดาวน์ตี / ฆ่า

```lua
-- config/config.client.lua
Config.Combat = {
    hit_cooldown_ms  = 500,
    kill_cooldown_ms = 2000,
}
```

### แอดมิน

```lua
-- config/config.log.lua
Config.Admin = {
    groups     = { ["admin"] = true, ["superadmin"] = true },
    cmd_grant  = "giveskin",     -- /giveskin <id> <ชื่อไอเทม> [จำนวน]
    cmd_revoke = "takeskin",     -- /takeskin <id> <ชื่อไอเทม> [จำนวน]
    cmd_reload = "reloadskin",   -- /reloadskin [id]
}
```

## ตำแหน่ง prop ในมือ

ค่าใน `config/config.positions.lua` จูนมาคู่กับ `rotationOrder = 2` (ZXY) แก้ค่าเดียวแล้วสกินทั้งหมวดขยับตาม

```lua
Shared.Positions = {
    DEFAULT               = { pos = vector3(0.0, 0.0, 0.0),       rot = vector3(0.0, 0.0, 0.0) },
    WEAPON_BOTTLE_A       = { pos = vector3(0.041, 0.008, -0.014), rot = vector3(-78.2, 63.3, 42.8) },
    WEAPON_KNUCKLE_A      = { pos = vector3(0.102, -0.023, -0.002), rot = vector3(-179.4, -108.0, -94.3) },
    WEAPON_MACHETE_A      = { pos = vector3(0.079, -0.019, -0.022), rot = vector3(-79.1, 40.8, 21.3) },
    WEAPON_POOLCUE_A      = { pos = vector3(0.079, -0.020, -0.022), rot = vector3(-74.8, 63.0, 44.5) },
    WEAPON_KNIFE_A        = { pos = vector3(0.079, -0.020, -0.022), rot = vector3(-79.1, 49.3, 29.2) },
    WEAPON_DAGGER_A       = { pos = vector3(0.079, -0.019, -0.022), rot = vector3(-79.1, 48.3, 28.3) },
    WEAPON_REVOLVER_MK2_A = { pos = vector3(0.155, 0.047, 0.008),  rot = vector3(-64.2, -12.2, -19.6) },
}
```

## เพิ่มสกินใหม่

สกินหนึ่งตัวต้องตรงกันสามชั้น ชั้นไหนไม่ตรงเมนูจะเปิดได้แต่ใช้ไม่ได้

| ชั้น | อยู่ที่ไหน | คีย์ |
| --- | --- | --- |
| โมเดลสกิน | `Hyper.Allweaponskins` | `skin.NAME` |
| รูปไอเทม | `Hyper_Inventory/html/img/items/` | `skin.IMAGE` |
| ไอเทมปลดล็อก | ตาราง `items` ของ es_extended | `skin.ITEMUSE` |

```lua
-- config/config.skins.lua
BOTTLE_SONGKRAN = {
    ITEMUSE  = "skin_bottle_songkran",
    IMAGE    = "skin_bottle_songkran",
    NAME     = "<ชื่อโมเดล .ydr ที่สตรีมไว้>",
    SKINNAME = "ปากฉลาม | สงกรานต์",
    SKINTIER = 2,
    HIT      = FX.SONGKRAN_HIT,
    KILL     = FX.SONGKRAN_KILL,
},
```

เพิ่มเสร็จแล้วใส่ชื่อสกินลงลิสต์ของหมวด (เช่น `BOTTLE_SKINS`) และรัน `sql/skins_items.sql` ลงฐานข้อมูล — ไม่ใช่แก้ `ITEMS.json` อย่างเดียว เพราะ es_extended จะ dump ทับทุกครั้งที่บูต

## ฐานข้อมูล

ตารางสร้างเองตอนบูตเมื่อ `Shared.Database.auto_migrate = true`

```sql
CREATE TABLE IF NOT EXISTS `hyper_weaponskin` (
    `identifier` VARCHAR(64) NOT NULL,
    `equipped`   LONGTEXT    NOT NULL DEFAULT '{}',   -- JSON: { [MAIN] = skinIndex }
    `favorite`   LONGTEXT    NOT NULL DEFAULT '{}',   -- JSON: { [MAIN] = true }
    `updated_at` TIMESTAMP   NOT NULL DEFAULT CURRENT_TIMESTAMP
                              ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (`identifier`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```
