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

As you can see, it doesn't do much. If you read the code and my previous post, you may probably have guessed already what is going on.  The gist is, we read a gradient color palette from *somewhere* (we'll get to this) and use it to create a simple Copper list that changes the background color every 10 raster lines.
But let's break this into smaller pieces.

```
#include <effect.h>
#include <copper.h>
```

First, we of course have some headers provided by the framework. Since our effect doesn't do much, we only need two - [effect.h](https://github.com/cahirwpz/demoscene/blob/master/include/effect.h) that contains some useful definitions, like the `Effect` struct itself and profiler stuff. Generally speaking, it will be used in every effect.
[copper.h](https://github.com/cahirwpz/demoscene/blob/master/include/copper.h) is another classic, without it we wouldn't be able do define and run our Copper list.

I encourage you to snoop around [include dir](https://github.com/cahirwpz/demoscene/tree/master/include) itself to check out other goodies the framework has to offer. Among other things, you will find Blitter helpers, shape primitives, tools to work with color palettes and music players.

```
#define WIDTH 320
#define HEIGHT 256
#define DEPTH 1
```

Some rather self-explanatory definitions regarding the screen we are going to work with. Lores PAL screen with only one bitplane. You may think "wait, only one? But we have 16 colors on the screen! One bitplay can have only two colors..."
While this is true, we only ever touch one color register, color register `0` and change it state mid-frame many times. ^^" That's why we don't need more.

```
static CopListT *cp;
static CopInsT *bplptr[DEPTH];
static BitmapT *screen;

#include "data/gradient_pal.c"
```

Here we declare our Copper list, a bitmap that will be our screen and a *bitplane pointer*. [TODO explain]
The last line includes an external asset. It may be a color palette (like in our case here), an image, a tracker module, an array of coordinates for Blitter to draw lines... anything really. In next part of this post we'll see how those assets are prepared and managed, for now just remember our `gradient_pal.c` looks like this:

```
#define gradient_pal_count 16

const __data PaletteT gradient_pal = {
  .count = 16,
  .colors = {
    0x001,
    0x002,
    ...
```

Now we can skip some code and go straight to the last line:

```
EFFECT(Hello, NULL, NULL, Init, NULL, Render, NULL);
```

This is how we register the effect. First argument is the effect's name, the rest are hook functions called at different points of the effect's life cycle. Our `Hello` has a lot of `NULLs`, the fully filled macro would look like this:

```
EFFECT(Hello, Load, Unload, Init, Kill, Render, VBlank);
```

- `Load` - is called right at the beginning. It's the best place to put all data preparation code to have it ready when the `Init` start. When we run a production (which is a collection of many different effects), their `Load`s will be called together before the first effect starts, so keep that in mind!
- `Unload` - like `Load` but in reverse, all `Unloads` get called when the production is ending and the last effect finishes. Currently rarely used in favor of `Kill`, see the disclaimer below. 
- `Init` - that's the entry point of the effect. We need to set up bitplanes and Copper list(s), enable/disable right DMA channels here.
- `Kill` - all cleanup code goes here, unlike `Unload` it's called when the given effect finishes.
- `Render` - called once per frame, most of the effect's logic will be happening there. In our example it only waits for the VBlank (which isn't needed, you can remove it and see the effect still runs, but I left it here to demonstrate the function).
- `VBlank` - that's the code that will be called under the VBlank interrupt. It's a small window of time, so don't put too much logic there, but if you for example want to switch the color palette, it may be a good place to do it.

[TODO load/unload disclaimer]

