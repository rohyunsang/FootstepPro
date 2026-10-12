# Changelog

## 1.0 (2026-10)

- Footstep component with four detection modes: Auto (Footstep notifies where an animation has them, foot bones elsewhere), Foot Bones, Anim Notify and Distance; the modes are exclusive, so a step never plays twice
- Walk / run by ground speed, landing sounds after falls
- Surface from the floor's physical material: Footstep Physical Materials, physical materials listed in the library, Physical Surface types, default surface
- 30 surfaces, 90 sounds, 30 physical materials, floor master material and 30 floor materials
- Built-in step particles for 19 surfaces (dust, snow, splashes, leaves, grains), drawn by one instanced mesh, no Niagara needed; optional Niagara / Cascade effect per surface
- Footprint decals on sand, snow, mud and soft dirt, mirrored for the right foot, with lifetime, fade and a per-character limit; wet and muddy trails after water and mud
- Footstep Surfaces window (editor): add floors from selected or dropped sounds, see each floor's progress (sounds, physical material, used on floors, saved) with the next step highlighted, create the physical material, apply it to materials or level actors, save everything it changed in one click; Footstep Surface Tools for editor Blueprints and Python
- Notify tool (editor): Content Browser > Footstep Pro > Add / Remove Footstep Notifies, the Footstep Notifies animation modifier, and Blueprint / Python nodes
- Random pitch and volume per step, no immediate repeats, attenuation and concurrency settings
- On Footstep event, Play Footstep / Get Surface At and other Blueprint nodes
- Footstep.Debug, Footstep.Log and Footstep.LogFeet console commands
- Example map with all floors, a ramp, a deck and a landing pit; the template walk and run animations carry Footstep notifies
