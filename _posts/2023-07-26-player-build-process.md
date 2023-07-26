---
title: "Post: Standard"
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - Post Formats
  - readability
  - standard
---

Summary of the Player Content Build calculation:
1. Gather dependencies from special singleton objects that make it into the build (called "GlobalGameManagers")
2. Build the first scene (level0). This is special case, processed before Resources, because we want the first Scene to be particularly fast to load. Hence we want all its dependent data to be in a single sharedAsset file.
The level0 file contains the heirarchy of the scene, e.g. closely matching what is in the Scene file in the editor. The sharedAssets0.assets file contains objects from Assets that are referenced by the scene.
3. Gather dependencies for Resources folders and build the resources.assets. This file can reference content already assigned to sharedAssets0.assets instead of duplicating it.
4. Build the rest of the scenes (level1 to N). Each level file can have its own sharedAsset file, but it can also refer to objects already assigned in any of the already generated sharedAsset files.
For example level2 can potentially use content from sharedAssets2.assets, sharedAssets1.assets, and sharedAssets0.assets.
5. Finalize the global data that is actually referenced to strip out unnecessary content. The result is written to globalgamemanagers.assets.

Of course that is a very high level summary that skips through a bunch of details, but i hope it is helpful to demystify how this works. One implication is that the order of scenes can have a big impact in how data is assigned into the sharedAsset files. For example if originally an Asset is only referenced by level5 and later it gets referenced from level1 then on the next build that data moves to level1's sharedAsset file. Possibly that might explain some of the changes in content that you observe when preparing patches.
