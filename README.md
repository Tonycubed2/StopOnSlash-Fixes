# Stop On Slash (tonycubed2 build)

A rebuild of [Stop On Slash](https://www.nexusmods.com/skyrimspecialedition/mods/66155) by **dTry**, from the
open-source code at [doodlum/StopOnSlash](https://github.com/doodlum/StopOnSlash) (GPL-3.0, see `COPYING`).
All credit goes to dTry and doodlum; the history up to commit 295013d is theirs. Not an official release, and no
build is uploaded to Nexus (the mod's Nexus permissions ask for the author's permission first).

Changes, build setup only (no gameplay code changed):
* Built against CommonLibSSE-NG 16.2.0 (SE 1.5.97, AE 1.6.1170, 1.7.99, 1.7.104). Built with 6.7.0, the
  `IsDead()` check on melee hits called vtable slot 0x9A (`TESObjectREFR::CreateAnimNoteReceiver`) instead of
  0x99 (`Actor::IsDead`), checked against SkyrimSE.exe 1.5.97 and 1.6.1170 and the built DLL.
* `include/PCH.h` defines `SKSEAPI` (16.x no longer does).
* Plain CMake setup against a local CommonLibSSE-NG (no submodule), no post-build copy into the game folder.
  Build steps: `BUILD.txt`.

---

Original README:

# Stop on Slash
Hitstop mod for Skyrim featuring compatibility and customization.


https://www.nexusmods.com/skyrimspecialedition/mods/66155
