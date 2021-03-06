# neko lua api.
> renderer functions:

parameters: from(x,y), to(x1,y1), color(r,g,b,alpha), thickness
- `RenderLine(x, y, x1, y1, r, g, b, a, thickness)`

parameters: pos(x,y), string text, color(r,g,b,alpha), centered, stroke, outline_color(r,g,b,alpha)
- `RenderText(x, y, text, r, g, b, a, centered, stroked, outline_r, outline_b, outline_g, outline_a)`

parameters: string alert_message
- `AddAlert(alert_message)`

> escape from tarkov functions:

parameters: from(x,y,z)

return type: EFT.Player*
- `GetAimbotTarget(from)`

return type: EFT.Player*
- `GetLocalPlayer()`

return type: EFT.GameWorld*
- `GetGameWorld()`

> misc:
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
- `HookMethod(namespace, class, field_name, arg_count, our_function_ptr)`

```lua
local ffi = require("ffi")
function UpdateHook(this)    
    RenderLine(100, 100, 100, 200, 255, 255, 255, 255, 1)
    RenderText(200, 200, "LOL", 255, 255, 255, 255, true, true, 0, 0, 0, 255)
end

ffi.cdef[[
typedef void (__fastcall *HookCallbackExample)();
]]

local cb = ffi.cast("HookCallbackExample", UpdateHook)
HookJitMethod("EFT", "ClientApplication", "Update", -1, tostring(ffi.cast("uint64_t", cb)))

function Unload()
  UnhookMethod("EFT", "ClientApplication", "Update", -1)
end
```

> ImGui

parameters:
- `void ImGuiSetItemDefaultFocus()`

parameters: string name, bool isSelected, int imguiFlags, Vector2 size, Vector2 padding
- `bool ImGuiSelectable`

parameters: string label, Vector2 size, bool border, int imGuiFlags, bool drawLabel
- `bool ImGuiBeginChild`

parameters: string text
- `void ImGuiText()`

parameters: string label, string floatPointer, float min_value, float max_value, string format
- `bool ImGuiSliderFloat`

parameters: string label, string floatArrayPointer
- `bool ImGuiColorEdit4`

parameters: string label, Vector2 size (0,0 for automatic size)
- `bool ImGuiButton`

parameters: Vector2 pos
- `void ImGuiSetCursorPos`

parameters: string label, string pointerToSelectedMemberInt, string items_separated_by_zeros
- `bool ImGuiCombo`

parameters: string label, Vector2 size, string pointerToBool
- `bool ImGuiCheckbox`

parameters:
- `void ImGuiEndChild`

parameters: string text
- `void ImGuiText`

example:
```lua
local ffi = require("ffi")

local colorBuffer = ffi.new("float[4]", {0,0,0,0})
local checkboxBuffer = ffi.new("bool[1]", false)
local sliderBuffer = ffi.new("float[1]", 0)
local comboBuffer = ffi.new("int[1]", 0)

function LuaMenuFirstChild()
  ImGuiText("test")
  ImGuiColorEdit4("test", tostring(ffi.cast("uint64_t", colorBuffer)))
  ImGuiCheckbox("test checkbox", tostring(ffi.cast("uint64_t", checkboxBuffer)))
  ImGuiSliderFloat("test slider", tostring(ffi.cast("uint64_t", sliderBuffer)), 0, 24, "%.2f" )
  ImGuiCombo("test combo", tostring(ffi.cast("uint64_t", comboBuffer)), "item1\0Item2\0Item3\0Item4" )
  if(ImGuiButton("test button", 0,0))
  then
    --button was pressed
  end
end

-- to access checkbox value do checkboxBuffer[0]
-- to access slider value do sliderBuffer[0]

function LuaMenuSecondChild()
  ImGuiText("test second child")
end

```

> callbacks:

notes: Will run inside unity thread in an update function
- `Update()`

notes: Will run when lua is being unloaded, you should unhook everythnig you've hooked here
- `Unload()`

notes: Child on the left of the menu
- `LuaMenuFirstChild()`

notes: Child on the right of the menu
- `LuaMenuSecondChild()`
