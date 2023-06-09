After the lengthy theoretical introduction, it's time to get our coding environment working. Through all parts of this "tutorial", we will be using toolchain prepared by my friend and mentor, Cahir of Ghostown fame. 
It's a full environment for cross-compilation from PC to the Amiga, featuring API for communication with all part of the Amiga chipset, GDB debugger integration, modified FS-UAE emulator (also with its own, built-in debugger) and other goodies. 

There are two ways to setting everything up - building it yourself (only Linux and MacOS) or using a ready-to-go Debian virtual machine prepared by Codi, another Ghostown member, and adapted by me for purposes of this series.

## DIY way
1. Grab base toolchain from [here] - it contains `amiga-gcc` compiler, object dumper and the emulator. While you can try building it yourself (by using a prepared `nazwa` script), be warned, it may be a bit capricious, especially on systems that are not Debian-based. On repo's **Releases** page, Cahir provides pre-compiled binaries of the latest version, you can grab them if you have troubles.
2. If you built the binaries yourself, remember to add them to your `$PATH`
3. Clone the tutorial repo prepared by me from [here]. Here is the whole boilerplate - libraries, headers, system-stuff and, of course, this is where we will be building our effects.
4. As we will be using git LFS to manage assets, remember to pull them as well, by calling `git lfs install` and `git lfs pull`.
5. Run `make` in the repo's root directory - it will build all of the tools, convert assets and compile all of the available effects. You will probably have to install some missing packages before it will succeed, just read what the `make` output tells you. 
6. Done! You can skip to the *"Running effects"* section now.

## Virtual machine
1. Install `VritualBox` if you don't have it already
2. Grab the machine from [here]
3. Import it
4. The toolchain and repository with tutorial effects are already inside - navigate to `sciezka` and call `make` to verify it builds everything correctly
5. Done!

# Running effects
No matter which way you've used, you should now be in the main directory of the `amiga-dmscn-tutorial`.

# Example effect
Let's look at the example code I prepared for this post. It will let us familiarize with the effect's code structure and most important parts. Here it is:

```
#include <effect.h>
#include <copper.h>
#include <color.h>
#include <types.h>
#include <fx.h>
#include <gfx.h>
#include <stdlib.h>
#include <system/memory.h>

#define WIDTH 320
#define HEIGHT 256
#define DEPTH 3

static CopListT *cp;
static CopInsT *bplptr[DEPTH];
static BitmapT *screen;

#include "data/gradient_pal.c"

static void MakeCopperList(CopListT *cp, CopInsT **bplptr) {
  short i;

  CopInit(cp);
  CopSetupBitplanes(cp, bplptr, screen, DEPTH);
  
  for(i = 0; i < gradient_pal_count; i++) {
    CopWaitSafe(cp, 10 * i, 0);
    CopSetColor(cp, 0, gradient_pal.colors[i]);
  }

  for(i = 0; i < gradient_pal_count; i++) {
    CopWaitSafe(cp, Y(110 + (10*i)), 0);
    CopSetColor(cp, 0, gradient_pal.colors[14-i]);
  }

  CopEnd(cp);
}

  

static void Init(void) {
  screen = NewBitmap(WIDTH, HEIGHT, DEPTH);
  SetupPlayfield(MODE_LORES, DEPTH, 0, 0, WIDTH, HEIGHT);
  cp = NewCopList(100);
  MakeCopperList(cp, bplptr);
  CopListActivate(cp);

  EnableDMA(DMAF_RASTER | DMAF_BLITTER);
}

  

static void Render(void) {
  TaskWaitVBlank();
}

EFFECT(Hello, NULL, NULL, Init, NULL, Render, NULL);
```

You will find the same code in the `effects/hello` directory in our tutorial repo too. After calling `make run`, you should see something like this:

![example Amiga effect](hello.png)

It doesn't do mu