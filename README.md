# neko lua api.
renderer functions:

parameters: from(x,y), to(x1,y1), color(r,g,b,alpha), thickness
- `RenderLine(x, y, x1, y1, r, g, b, a, thickness)`

parameters: pos(x,y), string text, color(r,g,b,alpha), centered, stroke, outline_color(r,g,b,alpha)
- `RenderText(x, y, text, r, g, b, a, centered, stroked, outline_r, outline_b, outline_g, outline_a)`

parameters: string alert_message
- `AddAlert(alert_message)`

escape from tarkov functions:

parameters: from(x,y,z)

return type: EFT.Player*
- `GetAimbotTarget(from)`

return type: EFT.Player*
- `GetLocalPlayer()`

return type: EFT.GameWorld*
- `GetGameWorld()`

misc:
parameters: string module_name

return type: pointer to module base
- `GetModuleBase(module_name)`

parameters: string namespace, string class, string field_name

return type: Fields offset
- `GetFieldOffset(namespace, class, field_name)`

parameters: string namespace, string class, string field_name, int arg_count

return type: Offset to the compiled methods address

note: will not work if the method hasn't been compiled yet
- `GetCompiledJitMethodAddress(namespace, class, field_name, arg_count)`

- `UnhookMethod(namespace, class, field_name, arg_count)`

parameters: string namespace, string class, string field_name, int arg_count, pointer to our function
- //ffi c def
- //void HookMethod (const char* _namespace, const char* _class, const char* _methodName, int argCount, void* ourFunction );
- `HookMethod(namespace, class, field_name, arg_count, our_function_ptr)`

```lua
local ffi = require("ffi")
function UpdateHook(this)    
    RenderLine(100, 100, 100, 200, 255, 255, 255, 255, 1)
    RenderText(200, 200, "LOL", 255, 255, 255, 255, true, true, 0, 0, 0, 255)
end

ffi.cdef[[
typedef void (__fastcall *HookCallbackExample)();
void HookMethod (const char* _namespace, const char* _class, const char* _methodName, int argCount, void* ourFunction );
]]

local cb = ffi.cast("HookCallbackExample", UpdateHook)

ffi.C.HookMethod("EFT", "ClientApplication", "Update", -1, cb)`

function Unload()
  UnhookMethod("EFT", "ClientApplication", "Update", -1")
end
```

callbacks:

notes: Will run inside unity thread in an update function
- `Update()`

notes: Will run when lua is being unloaded, you should unhook everythnig you've hooked here
- `Unload()`
