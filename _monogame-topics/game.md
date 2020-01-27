---
title: "The Game Class"
layout: topic
---
At the heart of the MonoGame framework is the [Microsoft.Xna.Framework.Game class](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Game).  The Game class is responsible for working with the operating system abstracting away much of the complexity of initializing the system to run a game.  

It also exposes several useful game systems with properties, like a ContentManager, GraphicsDevice, GameServiceContainer, and GameComponentCollection.  We'll explore these in more detail elsewhere.

Games built on the framework are build over this abstraction layer by _extending_ the Game class.  This derived class also serves as a host for centralizing game systems as class variables.  For example, the starter project declares a [GraphicsDeviceManager](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_GraphicsDeviceManager) and [SpriteBatch](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_SpriteBatch):

```csharp
GraphicsDeviceManager graphics;
SpriteBatch spriteBatch;
```
These two are included in the starter project, because they will be used in nearly every game.  The `graphics` object interfaces with the graphics card and is responsible for rendering the game on screen, and the `spriteBatch` is used to draw 2D graphics and text.

The Game class internalizes the [Game Loop Pattern](https://gameprogrammingpatterns.com/game-loop.html) and exposes hooks for your game code with virtual lifecycle methods that your derived class can override.

## The Initialize() method
The initialize method is used to set up any non-graphical game systems (we need to wait on graphics loading until the graphics card has been configured, as graphics content is ideally loaded into the video card's memory).

This also makes it a good place to set the desired screen resolution.  You can do so by setting a preferred back buffer size on the graphics object:

```csharp
graphics.PreferredBackBufferWidth = 2000;
graphics.PreferredBackBufferHeight = 1000;
graphics.ApplyChanges();
```

## The LoadContent() method
This method is used to load graphical and other game content, as it is run _after_ the graphics card is initialized.  This is also why the `spriteBatch()` is initialized here.

## The UnloadContent() method
This method is invoked before the game exits, which makes it a good place to unload content that is not managed by the ContentManager (as it will manage unloading its content itself).

## The Update(GameTime gameTime) method
This method is invoked every iteration of the game loop.  This is where game logic (i.e. moving things around) should be handled.  The `gameTime` variable contains a high-resolution measurement of how much time has elapsed between frames, which can be used for precise animation control.  Not surprisingly, this method embodies the [Update Method Pattern](https://gameprogrammingpatterns.com/update-method.html).

## The Draw method
This method is responsible for rendering the game.  It's not obvious from looking at the method, but it actually renders into a back buffer using the [Double Buffer Pattern](https://gameprogrammingpatterns.com/double-buffer.html).  

## The Full Virtual Method List
Those are the virtual methods overridden in the starter project's Game1 class, but there are other, less often used ones as well.  The full list is:

```csharp
protected virtual bool BeginDraw();
protected virtual void BeginRun();
protected virtual void Dispose(bool disposing);
protected virtual void Draw(GameTime gameTime);
protected virtual void EndDraw();
protected virtual void EndRun();
protected virtual void Initialize();
protected virtual void LoadContent();
protected virtual void OnActivated(object sender, EventArgs args);
protected virtual void OnDeactivated(object sender, EventArgs args);
protected virtual void OnExiting(object sender, EventArgs args);
protected virtual void UnloadContent();
protected virtual void Update(GameTime gameTime);
```
