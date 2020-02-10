---
title: "Text"
layout: topic
---
At some point, you're likely to want to add text to your games.  In most programs, the rendering of text is handled via libraries and tools provided by the operating system, but this is not true of most games.  In part, this is historical - early DOS games, for example, put the VGA graphics system into one of several special modes that were set up for rendering graphics instead of rendering text (remember, in the early days a computer monitor was effectively a terminal, like CMD in Windows or Bash in Linux).  In modern games, the desire to build cross-platform games typically means we need to handle our own text rendering.

Following that thought, consider how text is rendered.  There are two broad categories of sprites - bitmap sprites and vector sprites.  Bitmap fonts work like an old printing press, which utilizes a moving font consisting of a reusable metal stamps for each character, at each font, size, and weight.  By arranging the movable type in the correct order you could stamp out a block of text.  Instead of movable type bitmap fonts consist of a bitmap representation of the character, and instead of stamping, the bits of each character are copied into a destination.

In contrast, most modern fonts are vector-based, which means rather than storing the exact bits of the characters rendered at a certain size, it contains _instructions_ for drawing the font.  With such a representation, you can make your font render _at any size_ without need for extra sets of bitmaps - instead the renderer just scales up the dimensions used in the directions.  The process of "rasterizing" text using these fonts is a bit involved but basically the same across all applications that use it, so operating systems traditionally handle the process for us.  This also allows us to place all font files in a single system folder, and make all installed fonts available in all applications.  

As you might expect, we typically use Bitmap fonts in games.  So we need some way to convert vector fonts into a bitmap format.

## Quick and Dirty
The easiest method is to use a graphics editor to create an image with the text we need.  This works well if we only have a few, well-defined messages to convey to the player, i.e. "Press Start to Begin", "You Loose!", and "You Win!".  Once we have the saved graphic, we can load and display it as with any sprite.

However, with this method we can't render _dynamic_ text, that is, text created as the game runs.  This would include details like the score, or number of lives.  One solution to this challenge is to create separate images for each character and then write code to assemble them in order, just like a printing press.

## SpriteBatch.DrawText() and Sprite Fonts
XNA provides us exactly this functionality through the [SpriteBatch](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_SpriteBatch) object's `DrawString()` methods and the `SpriteFont` class.  The various `DrawString()` methods each take a `SpriteFont` object and a `string` to render using it, and provide different options for placing and formatting the text on screen.

The [SpriteFont](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_SpriteFont) object represents a specific font, including the bitmap data for all included characters.  It is generated for us from a vector font in the Content Pipeline, which we'll discuss shortly.  It also provides the very important `MeasureString()` methods, which returns a Vector2 measuring the size the string or StringBuilder passed as an arguement will be when rendered.  

This last is important, as _there is NO automatic text-wrapping_ functionality built into the `DrawString()` method - the string is simply printed as-is.  If you need more nuanced control over string placement, you have to write it yourself!

## Sprite Descriptions and the Content Pipeline
We create `SpriteFonts` using the Content Pipeline by from _Sprite Descriptions_, a UML file that describes the font we want to use.  A Sprite Description file looks like this:

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
This file contains an xml description of a font, and will be read by the XNA
Framework Content Pipeline. Follow the comments to customize the appearance
of the font in your game, and to change the characters which are available to draw
with.
-->
<XnaContent xmlns:Graphics="Microsoft.Xna.Framework.Content.Pipeline.Graphics">
  <Asset Type="Graphics:FontDescription">

    <!--
    Modify this string to change the font that will be imported.
    -->
    <FontName>Tahoma</FontName>

    <!--
    Size is a float value, measured in points. Modify this value to change
    the size of the font.
    -->
    <Size>18</Size>

    <!--
    Spacing is a float value, measured in pixels. Modify this value to change
    the amount of spacing in between characters.
    -->
    <Spacing>0</Spacing>

    <!--
    UseKerning controls the layout of the font. If this value is true, kerning information
    will be used when placing characters.
    -->
    <UseKerning>true</UseKerning>

    <!--
    Style controls the style of the font. Valid entries are "Regular", "Bold", "Italic",
    and "Bold, Italic", and are case sensitive.
    -->
    <Style>Regular</Style>

    <!--
    If you uncomment this line, the default character will be substituted if you draw
    or measure text that contains characters which were not included in the font.
    -->
    <!-- <DefaultCharacter>*</DefaultCharacter> -->

    <!--
    CharacterRegions control what letters are available in the font. Every
    character from Start to End will be built and made available for drawing. The
    default range is from 32, (ASCII space), to 126, ('~'), covering the basic Latin
    character set. The characters are ordered according to the Unicode standard.
    See the documentation for more information.
    -->
    <CharacterRegions>
      <CharacterRegion>
        <Start>&#32;</Start>
        <End>&#126;</End>
      </CharacterRegion>
    </CharacterRegions>
  </Asset>
</XnaContent>

```

As you might guess from the names, you provide the font name, size, style, and other details.  If you want both normal and bold styles, you will need to create _two_ SpriteFonts.  Similarly, if you want two sizes, say 16pt and 30pt, you will need to create _two_ SpriteFonts.  

Additionally, the `CharacterRegions` property determines what character(s) are included in your font.  Typically this would be the alphabet as both capitals and lowercase, plus the digits - but if you need additonal characters you'll need to modify this section.  Any character that doesn't exist in the SpriteFont is rendered as the default character (an * by default).

Once you have the Sprite Description customized, build it with the Content Pipeline Tool to create the binary version of the SpriteFont.  This can be loaded like any other content in the game.

> The MonoGame Pipeline Tool uses an out-of-date version of freetype6.dll that may cause font descriptions to not build.  You can download the new x64 version [here](https://github.com/ubawurinna/freetype-windows-binaries#windows-binaries-dll-of-freetype-win32win64).  Replace the existing `freetype6.dll` in `C:\Program Files (x86)\MSBuild\MonoGame\v3.0\Tools` with the downloaded dll (note: you'll need to rename the downloaded `freetype.dll` to `freetype6.dll`).

## Legal Notes
What you are basically doing by creating a SpriteFont is converting a Vector Font to a Bitmap Font.  This means you are creating a version of the font for _redistribution_ with your game, rather than an end-product.  You need to careful of licensing terms, as many fonts are licensed to you for creating derived works, but explicitly forbid redistributing the font itself.  
