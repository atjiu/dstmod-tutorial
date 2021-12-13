水群时偶然间发现 `风铃` 发的，记录一下

```lua
local moddir = KnownModIndex:GetModsToLoad(true)
local enablemods = {}

for k, dir in pairs(moddir) do
    local info = KnownModIndex:GetModInfo(dir)
    local name = info and info.name or "unknow"
    enablemods[dir] = name
end
-- MOD是否开启
function IsModEnable(name)
    for k, v in pairs(enablemods) do
        if v and (k:match(name) or v:match(name)) then return true end
    end
    return false
end
```