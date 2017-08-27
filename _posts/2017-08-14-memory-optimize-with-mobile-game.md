---
layout: "post"
title: "Memory Optimize With Mobile Game"
date: "2017-08-14 00:07"
---
# Aspect Of Mobile Game
Have you played the games named "Battlefield 1" or "Diablo III", yes they engage both the mind and the eye, so wonderful they are! But they also need high requirement, if you want to play them with phone, maybe several years later, the hardware isn't high enough to run, more accurately, cpu need more power and memory need more large. In the majority situation memory is the bottleneck, it's more and more large now but not enough. At previous article we know how to track the memory, now we will go deep into how to optimize the memory.<!--more-->

# Texture
If you track the memory, i will find the most memory will spend by texture in most case, the background, the sprite, the ui, whatever you see are consist of texture in the memory. First we need to know how many memory will cost by a texture, some young developers always have the point that the texture storage with large space will cost more memory, for example the png always take more space usage than jpeg(this just affect memory with I/O)
> obviously they make a mistake, the memory texture cost decide with two key: the size and pixel format. if the texture have the size with width and height, each pixel cost n bytes, the texture will cost memory: width*height*n

### Size
If a image have pure color, you can reduce the image to one pixel and scale with width and height in the game, i am promise everybody won't find the different vision. unfortunately most time we need colorful image
> image with pure color partly which can use 9-slice scaling.

If you have a huge scene with background, you can make it with tile map which will save a lot of memory and also make the scene colorful. If the background can't be change with a whole screen size, as we know the screen measure with phone is much smaller than pad and pc
> the image with low resolution can cheat your eyes successful, even more make the image 80 percent size and scale 1.25 in the game is perfect.

### Pixel Format
Here is a list to show you the memory each pixel format cost:

Pixel Format | Memory(bits)
------ | ------
BGRA8888 | 32
RGBA8888 | 32
RGB888 | 24 	
RGB565 | 16
A8 | 8
I8 | 8
AI88 | 16
RGBA4444 | 16
RGB5A1 | 16
PVRTC4 | 4
PVRTC4A | 4
PVRTC2 | 2
PVRTC2A | 2
ETC1 | 4
ETC2 | 8
S3TC_DXT1 | 4
ATITC | 8

In the majority of cases the art designer send you the images with extension("png" or "jpg"), the png images are compress with RGBA8888 and jpg compress with RGB888, you can convert them to other pixel format, or the texture will be the original. But we always convert the pixel format with source file except special requirement, because it affect the performance.

You can convert the pixel format with the tools "ImageMagick" and "TexturePacker" which are very excellent, they can also pack several images into a big image(you will know later), and some powerful function you will find. what pixel format should we use for our app? On my experience:

### All Platform
if the image need high visual aesthetics, like button, dialog, and so on with UI, RGBA8888 or RGB888 if without alpha will be the best choose, some images you are not sure and with small size, you can also use it. The image with whole screen size must be have your emphases attention, first check if there are many blank space with the image, were this to happen, divide the image into pieces to make sure with less blank space, try using RGBA4444 with dithering or RGB565 if without alpha, even using compress pixel format which we will discuss later. Textures for 3D models can be all right using RGB565, some effects can use RGBA4444 or RGB565, even compress pixel format, which have the reason your eyes will be cheat when the images are moving.
According to the list you know compress pixel format cost less memory, obviously the quality always be anxious, sometimes you need to do some test with images to decide, with different GPU the device will support different compress pixel format.

### Android
Every android developers need to face a monster, a lot of device with all sorts of hardware, at the moment Android bother us with different GPU.

compress pixel format | GPU | Manufacturer
--- | --- | ---
PVRTC4, PVRTC4A, PVRTC2, PVRTC2A | Series of PowerVR SGX | Imagination Technologies
ATITC | Series of Adreno | Qualcomm
S3TC | Series of Tegra | NVIDIA

Fortunately, there is a compress pixel format named "ETC1" as a part of OpenGL ES 2.0 Graphics Standard support by all of Android device with OpenGL ES 2.0, that's enough, the defect is without alpha, the upgrade version name "ETC2" support alpha, but new device with OpenGL ES 3.0 can run with it. So we always take the solution:

> Texture with RGB will convert to ETC1, nothing need to change.
>
> Texture with RGBA need divide into two textures, RGB with ETC1 and Alpha with ETC1 or A8(more memory but with more higher quality), we need change the fragment shader to deal with the two textures.

```cpp
varying vec4 v_fragmentColor;
varying vec2 v_texCoord;
uniform sampler2D u_s2dRGB;
uniform sampler2D u_s2dAlpha;
void main()
{
    vec4 color = texture2D(u_s2dRGB, v_texCoord);
    color.a = texture2D(u_s2dAlpha, v_texCoord).r;
    gl_FragColor = color * v_fragmentColor;
}
```

### iOS
iOS developers are more lucky because all the devices with Series of PowerVR SGX(Thanks Apple, Thanks Jobs, Thanks the engineer), PVRTC2 and PVRTC2A are 2 bits, the quality can't be accept in most case, PVRTC4A support alpha, but quality for alpha is worst, so we also deal like ETC1:

> Texture with RGB will convert to PVRTC4, nothing need to change.
>
> Texture with RGBA need divide into two textures, RGB with PVRTC4 and Alpha with PVRTC4 or A8, the same fragment shader we should change.

# Pack Images Together
In OpenGL ES 1.0/1.1 texture have the restriction with power of tow , if a image with size(140 * 140), the texture will be 256 * 256 in the memory, you will ignore it with one image, but if there are so many image like this, it will waste a lot of memory
> pack several images into one POT image sounds a good solution. simple example 140*140 and 260*260 will cost 256 * 256 + 512 * 512 separately, and cost 512 * 512 if they are packed, it will save 256 * 256. Otherwise one POT image can make draw call reduce which make the game more fluid.

OpenGL ES 2.0 remove the restriction, but if you use compress pixel format like ETC1 or PVRTC4 which make sure texture with POT and square, you also need to pack images to save memory.

# Animation Data
Texture is the NO.1 in memory cost, if you develop an 3D Action Game, the NO.2 always be animation data. Skeletal animation is the most popular skill for 3D animate, the key frame contain each skeleton position, with key frame and skeleton numbers decide animation data size. The art designer can reduce key frame and skeleton numbers, but the work is very hard and massive, can we change the data with position?

As you know position always storage with Vector3(float x, float y, float z), 12 Bytes cost, a simple idea arise in mine mind, ShortVector3(short x, short y, short z), convert Vector3 to ShortVector in file and memory, and restore when we need use them to do logic, now the memory will be half. Shall we use CharVector3(char x, char y, char z) or nBytesVector3? With more thinking we can make Vector3 or even all the data with each key frame as a entirety, each element have different bytes, you can give a thought to do some test for that, we'll not discuss here.

# Audio
Memory with audio is also a big cost, it also have a lot of storage format like image, but the different is just one format called "wave" can be recognize by sound card.

> So whatever you storage, the audio will convert to wave when load into sound engine, the memory decide by the time a audio will play. make combine with short audio, or use stream  cut audio into pieces and load each piece when need play and unload when it's finish can save memory.

# Dynamic Use
We need create a lot of game object in the game, they will cost a lot of memory, you can track them and find solution with the top memory cost.
