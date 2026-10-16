# Footstep Pro for Unreal Engine

> This repository holds the documentation and the example project of **Footstep Pro**, a footstep plugin with 30 surfaces sold on Fab. The plugin and its sounds are not included here; get them from Fab. The example project is under [Releases](https://github.com/rohyunsang/FootstepPro/releases/latest).

Surface-aware footsteps for any character. Add the **Footstep** component and the character plays walk, run and landing sounds that match the floor under each foot, with dust, splashes, snow or leaves at the feet and footprints in sand, snow and mud. It ships with 30 surfaces (90 sounds), their physical materials and ready-made floor materials. Steps come from the animation's foot bones or from Footstep notifies, and an editor tool places those notifies for you.

- **Engine versions:** Unreal Engine 5.5, 5.6, 5.7, 5.8
- **Platform:** Windows (Win64)
- **Audio:** 90 sounds (30 surfaces × walk / run / land), mono, 44.1 kHz
- **Modules:** `FootstepPro` (Runtime), `FootstepProEditor` (Editor: the notify tool)
- **Manual (PDF):** `Docs/FootstepPro_Manual.pdf`
- **Online documentation:** https://github.com/rohyunsang/FootstepPro
- **Example project:** https://github.com/rohyunsang/FootstepPro/releases/latest/download/FootstepPro_ExampleProject.zip

![Walking over the 30 floors of the example map](Docs/images/hero.jpg)

## Contents

1. [Installation](#installation)
2. [Quick start](#quick-start)
3. [Example project](#example-project)
4. [What is included](#what-is-included)
5. [How a surface is chosen](#how-a-surface-is-chosen)
6. [Step detection](#step-detection)
7. [Adding Footstep notifies automatically](#adding-footstep-notifies-automatically)
8. [Effects and footprints](#effects-and-footprints)
9. [NPCs, monsters and animals](#npcs-monsters-and-animals)
10. [Component settings](#component-settings)
11. [Using your own floors and sounds](#using-your-own-floors-and-sounds)
12. [Blueprint API](#blueprint-api)
13. [Debugging](#debugging)
14. [FAQ](#faq)
15. [Support](#support)

## Installation

1. Install **Footstep Pro** from Fab into your engine (Fab > Library > Install to Engine).
2. Open your project and enable the plugin in **Edit > Plugins** (search "Footstep Pro"), then restart the editor.
3. The content appears in the Content Browser under **Plugins > Footstep Pro Content**. If you don't see it, turn on **Settings > Show Plugin Content** in the Content Browser.

## Quick start

1. Open your character Blueprint and click **Add > Footstep**.

   ![The Footstep component on the template character](Docs/images/bp_components.png)

2. Give your floors a footstep material. Either use one of the `MI_Floor_*` materials in **Footstep Pro Content > Materials > Floors**, or set the **Phys Material** of your own material to one of the `PM_Foot_*` physical materials.
3. Press **Play** and walk. Steps switch to run sounds above **Run Speed**, and a landing plays after a fall.

The default settings fit the UE5 Manny/Quinn skeleton (`foot_l`, `foot_r`). For other skeletons, set **Foot Bones** to your feet's bone names.

![Component settings](Docs/images/component_details.png)

## Example project

The example project opens on a map with all 30 floors, a metal ramp up to a wooden deck, and a sand pit to jump into. The panel at the top left shows the surface under the character and the step type.

1. Install the plugin from Fab (see Installation).
2. Download [FootstepPro_ExampleProject.zip](https://github.com/rohyunsang/FootstepPro/releases/latest/download/FootstepPro_ExampleProject.zip), unzip it and open `FootstepPro.uproject` with UE 5.5 or later.
3. Press **Play**. Use **WASD** to move and **Space** to jump. **Ctrl** switches between walking and running, **M** cycles the detection mode, and **F** shows the debug traces.

The template's walk and run animations already carry Footstep notifies (placed with the notify tool), so the default **Auto** mode uses them; animations without notifies fall back to the foot bones.

The **Footstep Demo HUD** actor in the map is only for the demo. You don't need it in your game.

## What is included

![The 30 floor materials](Docs/images/floors.jpg)

| Surfaces | |
|---|---|
| Hard | Asphalt, Concrete, Marble, Tile, Rock, Rubble, Wet Floor |
| Soft | Carpet, Dirt (Hard), Dirt (Soft), Mud, Sand, Gravel |
| Nature | Grass (Short), Grass (Dense), Grass (Dry), Leaves, Pine Needles, Snow (Soft), Snow (Packed), Ice, Shallow Water |
| Metal | Metal Grate, Metal Stairs, Metal (Thick), Metal (Thin) |
| Wood | Wood (Solid), Wood (Hollow), Wood (Creaky), Wood Deck |

In **Footstep Pro Content**:

| Folder | Content |
|---|---|
| `Sounds/<Surface>` | `SW_Foot_<Surface>_Walk`, `_Run`, `_Land` (90 sounds) |
| `PhysicalMaterials` | `PM_Foot_<Surface>`: one Footstep Physical Material per surface (30) |
| `Materials` | `M_FootstepFloor`: the floor master material (color, noise, planks/tiles, grout) |
| `Materials/Floors` | `MI_Floor_<Surface>`: a floor material per surface, with its physical material set (30) |
| `Data` | `DA_FootstepSurfaces`: the surface library; `ATT_Footsteps`: attenuation (full volume within 2 m, silent after 20 m) |
| `Effects` | `M_FootstepParticle` and its texture atlas (built-in step particles) |
| `Effects/Footprints` | `M_FootprintDecal` and `MI_Print_Sand`, `_Snow`, `_Mud`, `_Dirt`, `_Wet` |
| `Effects/Textures` | Particle atlas, shoe print and paw print (`T_Pawprint`, `T_Pawprint_N`) |

## How a surface is chosen

For every step, the component traces down from the foot and reads the physical material of what it hits. It then picks the first match in this list:

1. **Footstep Physical Material:** the material's **Footstep Surface** name (all `PM_Foot_*` work this way).
2. **Physical material listed in the library:** any physical material you add to a surface's **Physical Materials** list. Use this for materials you already have.
3. **Surface type:** the project's Physical Surface types (Project Settings > Physics > Physical Surface) that you add to a surface's **Surface Types** list.
4. **Default surface:** the library's **Default Surface** (Concrete).

Landscapes work the same way. Set the physical material on each landscape layer (in the layer info or the landscape material).

## Step detection

| Detection | How it works | Use it when |
|---|---|---|
| **Auto** (default) | Footstep notifies in animations that have them, foot bones in the others. | Most projects: hand-tuned timing where you placed notifies, automatic steps everywhere else |
| **Foot Bones** | Watches each foot bone. A step plays when a foot comes back down after clearly lifting off. Footstep notifies are ignored. | Any walk or run animation; nothing to set up |
| **Anim Notify** | Steps play only from **Footstep** notifies in your animations. | You want exact timing and nothing else |
| **Distance** | A step every half stride (**Walk Stride**, **Run Stride**) while moving on the ground. Notifies are ignored. | Characters without legs, cameras, vehicles |

The modes never mix within one step, so a step never plays twice. In **Auto**, an animation counts as "has notifies" while Footstep notifies keep arriving (at least one per second); when they stop, the foot bones take over.

**Placing notifies by hand:** in an animation sequence, right-click a notify track > **Add Notify > Footstep**. Set **Foot Bone** to the foot touching down. **Type** is Auto by default, which picks walk or run from the speed; it can also be forced to Walk, Run or Land. In the animation editor the notify previews the default surface's sound.

## Adding Footstep notifies automatically

![Footstep notifies placed by the tool on the template walk](Docs/images/notify_tool.png)

The notify tool reads the foot bones of an animation and places a **Footstep** notify on every frame a foot touches down, on a track named **Footsteps** (it uses the same lift / contact rule as the Foot Bones detection).

- **Content Browser:** select one or more animation sequences, right-click > **Footstep Pro > Add Footstep Notifies**. **Remove Footstep Notifies** takes them out again. Save the animations to keep the notifies.
- **Animation Modifier:** in an animation, **Animation Data Modifiers > Add Modifier > Footstep Notifies**. It keeps its settings (foot bones, lift and contact height) with the animation and can be reapplied or reverted.
- **Blueprint / Python (editor only):** `Find Foot Contacts` (look without changing), `Add Footstep Notifies`, `Remove Footstep Notifies`. For example, to process a folder in Python:

```python
import unreal
for path in unreal.EditorAssetLibrary.list_assets("/Game/Characters/Animations", recursive=True):
    seq = unreal.EditorAssetLibrary.load_asset(path)
    if isinstance(seq, unreal.AnimSequence):
        unreal.FootstepNotifyTools.add_footstep_notifies(seq, ["foot_l", "foot_r"], 8.0, 4.0)
        unreal.EditorAssetLibrary.save_loaded_asset(seq)
```

Existing Footstep notifies are replaced, other notifies are left alone. For skeletons that don't use `foot_l` / `foot_r`, use the modifier or the Blueprint / Python nodes, which take the bone names.

## Effects and footprints

![Footprints in snow and a muddy trail](Docs/images/effects.jpg)

**Particles:** each surface has built-in step particles: dust on dirt, sand and concrete, snow powder, splashes on water and mud, leaves and needles, gravel grains. They are camera-facing sprites simulated by the plugin and drawn with one instanced mesh per world, so they need no Niagara asset and cost one draw call for all characters. Each surface's **Particles** (shape, color, count, size, speed, lifetime) can be edited in the library. A landing spawns twice as many, running one and a half times as many.

**Your own effects:** set a surface's **Effect** to a Niagara or Cascade system to spawn it on every step, with or instead of the built-in particles.

**Footprints:** sand, snow, mud and soft dirt leave a decal under each foot. The right foot's print is mirrored automatically. Prints stay **Footprint Lifetime** seconds and fade out over the last **Footprint Fade Time** seconds; a character keeps at most **Max Footprints**.

**Trails:** a surface's **Trail Footprint** is carried onto the next floors: after shallow water the next 8 steps leave wet prints, after mud the next 6 leave muddy prints, each fainter than the one before.

Footprints are deferred decals, so the floor must receive decals (on by default for meshes and landscapes).

## NPCs, monsters and animals

The component works the same on any Character: the player, AI-controlled NPCs, monsters and animals. The example project's **FootstepTests/Maps/FootstepCreatures** map shows them walking by themselves (idle → patrol → chase, with a jump):

![A quadruped's paw prints in the sand](Docs/images/creatures_paws.jpg)

| Character | What to set |
|---|---|
| **AI NPC** (Behavior Tree, State Tree, your own state machine) | Nothing. Steps follow the animation and the speed, wherever the movement comes from. |
| **Large or small creatures** (actor or mesh scaled) | Nothing, with **Scale With Mesh** on (default): lift / contact heights, Run Speed, footprints and particles scale with the mesh. |
| **Animals** (four legs or more) | Nothing if the feet are named foot / paw / hoof: they are found automatically. Otherwise list the feet in **Foot Bones** (any number). Set **Run Speed** to the animal's own run threshold, and **Footprint Mask / Normal** to `T_Pawprint` / `T_Pawprint_N` for paw prints. |
| **Many NPCs** | Off-screen NPCs that only animate when seen keep stepping by distance (**Distance When Not Rendered**). Effects farther than **Effect Max Distance** are skipped. A Sound Concurrency caps crowds. |

When two feet land together (a trotting animal, notifies on both feet), one sound plays and each foot still leaves its print. The notify tool finds the feet the same way, so four-legged animations get one notify per paw.

## Component settings

| Setting | Default | |
|---|---|---|
| Surface Library | (plugin's) | Floors and sounds. Empty uses `DA_FootstepSurfaces`. |
| Detection | Auto | See [Step detection](#step-detection). |
| Run Speed | 350 | Ground speed (cm/s) from which run sounds play. |
| Min Speed | 20 | No steps below this speed. |
| Feet › Foot Bones | foot_l, foot_r | Bones or sockets of the feet, any number. Found by name (foot / paw / hoof) if none of them exist. |
| Feet › Scale With Mesh | on | Lift / contact heights, Run / Min Speed, footprints and particles follow the mesh's scale. |
| Feet › Distance When Not Rendered | on | Off-screen meshes with frozen bones keep stepping by distance. |
| Feet › Foot Lift Height | 8 | A foot is in the air once it is this many cm above its planted height. Raise it if steps double up; lower it for shuffling or crouched walks. |
| Feet › Foot Contact Height | 4 | A foot in the air touches down when it comes back within this many cm of its planted height. |
| Distance › Walk / Run Stride | 180 / 300 | cm per two steps (Distance detection). |
| Landing › Play Landing | on | Landing sound after a fall. |
| Landing › Min Landing Speed | 250 | Falling speed (cm/s) needed. Small hops stay quiet. |
| Trace › Trace Distance | 60 | How far below the foot to look for the floor (cm). |
| Trace › Trace Channel | Visibility | Collision channel of the floor trace. |
| Sound › Volume | 1.0 | Multiplied with the surface's own volume. |
| Sound › Pitch Variation | 0.06 | Random pitch of ±6 % per step. |
| Sound › Volume Variation | 0.15 | Each step up to 15 % quieter, at random. |
| Sound › Attenuation | (plugin's) | Empty uses `ATT_Footsteps`. |
| Sound › Concurrency | none | Optional Sound Concurrency, for example to limit steps from crowds. |
| Effects › Spawn Effects | on | Built-in particles and each surface's own effect. |
| Effects › Effect Scale | 1.0 | Multiplies the particle count. |
| Effects › Effect Max Distance | 3000 | No effects farther than this from the camera (cm). 0: no limit. |
| Footprints › Spawn Footprints | on | Footprints and trails. |
| Footprints › Footprint Size | 14 × 32 | Width and length (cm). |
| Footprints › Footprint Lifetime / Fade Time | 20 / 6 | Seconds a print stays, and the fade at the end. |
| Footprints › Max Footprints | 60 | Prints one character keeps; the oldest goes first. |
| Footprints › Footprint Forward Offset | 7 | Moves the print from the ankle toward the toes (cm). |
| Footprints › Footprint Mask / Normal | none | Replaces the shoe print's shape for this character (for example the included paw print). |
| Debug › Draw Debug | off | Draws each floor trace and the surface name. |

## Using your own floors and sounds

**Tools > Footstep Surfaces** is the place to add floors and their sounds. Every surface shows how far along it is: three bars in the list (sounds, physical material, used on floors) with the next thing to do, and on the right one card per step whose blue button does that next thing.

![The Footstep Surfaces window](Docs/images/surface_editor.png)

1. **Keep your floors in the project:** while the plugin's own library is open, a red banner offers **Make Project Copy**. It copies the library to `/Game/Footsteps` and, if the plugin's library was the project default, makes the copy the default, so every Footstep component without its own library uses it and plugin updates never touch it. The badge next to the library says which one you are editing: **Plugin**, **Project · not in use** (an orange banner offers **Use as Project Default**) or **Project · Default**. The setting lives in **Project Settings > Plugins > Footstep Pro**.
2. **Add a floor:** select its footstep sounds in the Content Browser and click **+ New from N Selected Sounds**, or drag the sounds onto the window. The surface is named after the words all the sounds share (`Footstep_Wood_Walk_01` + `Footstep_Wood_JumpLand` → `Wood`), and the sounds are sorted by name: *land / jump / fall* go to **Land**, *run / jog / sprint* to **Run**, the rest to **Walk**. Sounds that are already in a surface select that surface instead of making a copy.
3. **1 Sounds:** with the surface selected, **Add N Sounds** (or dragging) adds more. A surface with several sounds per type picks one at random without immediate repeats. **Walk / Run / Land** play one; the card says when a type borrows another (*Land uses Run*).
4. **2 Physical material:** **Create PM_Foot_<Surface>** makes the physical material that leads to the surface (next to the library), or shows the existing one.
5. **3 Used on floors:** select your floor materials in the Content Browser and click **Apply to N Materials**, or select floor actors in the level and click **Apply to N Actors**. The card counts the materials and level actors that use the surface. The default surface needs neither step: it plays wherever nothing else matches.
6. **4 Saved:** **Save All** (top right, with the number of unsaved assets) saves the library and everything the window changed: physical materials, materials and level actors. A blue dot marks surfaces changed since the last save; the **Needs work** and **Unsaved** filters list only those.

Below the cards, the details panel holds the surface's volume, particles, footprints and mapping. The status line at the bottom keeps the result of the last action and the number of problems (hover for the list: a surface without walk sounds, one no physical material leads to, a duplicate name, a missing default). Every change can be undone with Ctrl+Z. The same actions are available to editor Blueprints and Python as **Footstep Surface Tools** (`Add Surface From Sounds`, `Add Sounds`, `Find Or Create Physical Material`, `Apply To Materials`, `Apply To Actors`, `Validate`, `Copy Library To Project`, `Set Project Default Library`).

**By hand:** set your material's **Phys Material** (Material > Details > Physical Material) to a `PM_Foot_*`, or set **Phys Material Override** in a mesh's collision settings. A new surface is an element of the library's **Surfaces** with a **Surface** name and sounds in **Walk**, **Run** and **Land** (an empty Land list falls back to Run, and an empty Run list to Walk), plus a physical material of class **Footstep Physical Material** whose **Footstep Surface** is that name.

## Blueprint API

| Node | |
|---|---|
| **On Footstep** (event) | Every step and landing: Surface, Type (Walk / Run / Land), Location, Physical Material. Use it for dust, decals or AI noise. |
| **Play Footstep** | Plays a step of a given type now, optionally under a foot bone. |
| **Play Footstep Auto** | Plays a walk or run step for the current speed. |
| **Get Surface At** | The surface (and physical material) under a world location. |
| **Get Last Surface** | Surface of the most recent step. |
| **Get Movement Type** | Walk or Run for the current speed. |
| **Get Active Detection** | The source in use right now (for Auto: Anim Notify or Foot Bones). |
| **Spawn Particles** (Footstep Effects subsystem) | Spawns a built-in particle burst anywhere, for example for a slide or a dropped object. |
| **Find Foot Contacts / Add / Remove Footstep Notifies** (editor) | The notify tool, for batch processing. |
| **Resolve Surface** (library) | The surface name for a physical material. |
| **Get Default Footstep Library** | The project default (Project Settings), else the plugin's library. |
| **Get Surface Names** (library) | All surface names in a library. |

## Debugging

| Console command | |
|---|---|
| `Footstep.Debug 1` | Draws every step's floor trace with the surface name, for all characters. |
| `Footstep.Log 1` | Logs every step: `[Footstep] <time> <surface> <type> <sound> <foot> <character>`. |
| `Footstep.LogFeet 1` | Logs the foot bone heights every frame, for tuning Foot Lift / Contact Height. |

![Debug traces](Docs/images/debug.jpg)

## FAQ

**No sounds play.** Check that the character's mesh has the foot bones listed in **Foot Bones**, and that the floor blocks the **Trace Channel** (Visibility by default). Then run `Footstep.Debug 1`: a trace that hits nothing draws red.

**Every floor sounds like concrete.** The floor's material has no footstep physical material, so the default surface plays. Set its Phys Material (see [Using your own floors and sounds](#using-your-own-floors-and-sounds)).

**Steps play twice, or some are missing.** Your animation lifts the feet more or less than Manny. Raise **Foot Lift Height** if steps double, or lower it if steps are missing. `Footstep.LogFeet 1` shows the real heights. You can also switch to **Anim Notify** detection.

**Footprints don't show.** The floor must receive decals: check **Receives Decals** on the mesh, and that the project's **DBuffer Decals** setting is on (default). Prints from running are 1.5 m apart, so with a camera close behind the character they are often already off screen.

**Does it work in multiplayer?** Each client plays the steps of the characters it sees, from their animation. Nothing is replicated. A dedicated server spawns no effects or footprints.

**Can I use it on AI and other characters?** Yes. Add the component to any Character (or any actor with a skeletal mesh). Use a Sound Concurrency to cap crowds.

## Support

- Email: llog@llog.dev
- Documentation and updates: https://github.com/rohyunsang/FootstepPro
