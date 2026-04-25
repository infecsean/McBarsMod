# McBarsMod
A DRG Mod that adds the Minecraft Health &amp; Armor Bars

I'm assuming you the viewer have basic knowledge of Blueprint modding, since I uploaded this mainly for people to learn or see how things work or debug.
If you aren't familiar, you can follow [this guide](https://mod.io/g/drg/r/drg-basic-modding-guide) for basic modding and [this guide](https://mod.io/g/drg/r/how-to-blueprint-mod) for BP modding.

### How to open:
- Have Unreal Editor V4.27.2
- If you have the FSD-Template, go to next step, if not, follow the two guides above to see how to set it up from [this repo](https://github.com/DRG-Modding/FSD-Template)
- Put the 3 folders in the Content folder (If you already have ModHub and DRGLib, then only pull the Bar folder to the content folder)

### File Structure
ModHub and DRGLib are dependencies.
The MinecraftHPnArmor contains the main files:
- /Icons: This just contains all of the sprites I made from copying Minecraft online sources and imported as Texture assets
- InitCave: Spawns the mod only when you're in a mission (Mod will not load in space rig)
- McHpAStruct & McHpA_Table (both terribly named btw, sorry I'm still learning): Used for storing Texture references to the icons above
- EMcBarState: Just an enum for the three animation states the bars can be in (static, shake, regen)
- SAV_McBarSettings & WBP_McBarSettings: SAV is the save file format (just 2 variables for now), and the WBP is what's displayed on ModHub/UUI

That leaves 3 files left:
- BP_MCHealthArmor: Top level, handles
  - Creating the HUD
  - Loading saved configs, working with ModHub to save
  - Hooking ModHub page changes to UI changes
  - Hooking Events related to health and armor to changing UI
- WBP_McStatusIcon:
  - Contains 1 instance of the heart/armor icon widget
  - Owns 2 functions to control behavior of each icon in the row:
    - SetIconState(EMcBarState state, bool bHit)
    - SetShakePosition(bool offset)
- WBP_McHealthArmor:
  - Contains the layout of the 2 bars, which is just a VerticalBox containing 2 HorizontalBox containers
  - On widget initialized, store all icons into 2 arrays to manage in the future, default animation states to static.
  - UpdateHealth/UpdateArmor:
    - Finally the fun part. Taking in a percentage health and a bool bHit,
    - Iterate over array of icons, index * 0.1 to calculate the threshold that the heart/armor percentage needs to be to be full, the rest is just setting that behavior.
  - UpdateAnimByState (this is called 24 times per second)
    - When static, set all offset to false
    - When shake, set all offset to be randomly true/false
    - When regenerate, set all offset to false increment the regen index, then offset the heart/armor at that index
      - This creates that effect where the entire regen animation is over an entire cycle of animation
