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

parameters: string namespace, string class, string field_name, int arg_count, pointer to our function
- `HookJitMethod(namespace, class, field_name, arg_count, our_function_ptr)`

callbacks:

notes: Will run inside unity thread in an update function
- `Update()`
