---
title: "Input"
layout: topic
---

You have likely worked with user input primarily using an _event-driven_ model up to this point.  That makes a lot of sense, given in most applications, the program must wait for the user to take some action before any changes in state need to be made.  Consider a text editor - it does not need to render anything new until the user starts typing.  Then it needs to add the typed characters to its text buffer, apply any spell check/grammar check operations, autosave, etc.  Once these tasks are completed, there really isn't much to do until the user types the next set of characters.

In contrast, a game is a _simulation_, and the simulation must advance, and the updated game world rendered even if there is _no_ input from the user.  The [Game Loop Pattern](https://gameprogrammingpatterns.com/game-loop.html) embodies this need, an in many game systems the entire game loop will be processed within 1/60th of a second to allow game rendering to sync with the monitor's refresh rate.  The kind of event-driven UI we see in productivity apps is incompatible with the game loop approach, as the input event could be triggered while the game us updating or rendering, potentially changing the game state in the middle of these operations.

## Input State Buffering
The solution is to record incoming input into a buffer in memory.  Consider the keyboard - we can represent the state of each key with a single bit.  When we receive a `keydown` event, we can set the corresponding bit in the buffer to `1`.  When a `keyup` event is received, we can change the bit back to a `0`.  Before we go into the update portion of the game loop, we can copy this buffer into another buffer of the same size, which we then make available to the update method.  This second buffer does not change throughout the update process making it safe for us to use, while the primary buffer continues to receive event-based updates, allowing the operating system to continue providing input.  The next time through the game loop, the process repeats.  If you think this looks like the [Double Buffer Pattern](https://gameprogrammingpatterns.com/double-buffer.html), you're absolutely right.

In Monogame, this buffering is handled for us by the framework.  We can get a copy of the current input buffer for any input device by invoking the corresponding static `GetState()` method for that input device.  The devices themselves are represented with static classes implementing the `GetState()` method.  These are:

* [Keyboard](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Keyboard)
* [Mouse](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Mouse)
* [GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
* [Joystick](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Joystick)

In addition, the framework handles multiple players by assigning input devices an index.  If you have four controllers hooked up to your computer, for example, these will have indices 0-3.  Invoking `GamePad.GetState(2)` will get the state for the 3rd GamePad.  In games with a single player, you may need to determine which input device is the one currently used by the player.

## Input State Structs

The `GetState()` methods return a `struct` corresponding to the input buffer, with methods for querying this state.  For example, to determine if the up key is pressed, we can use:

```csharp
KeyboardState keyboardState = Keyboard.GetState();
if(keyboardState.IsKeyDown(Keys.Up)) {
  // TODO: Update the game based on the up key being pressed
}
```

There is a drawback to moving from event-based to buffer-based input; you no longer are notified _exactly when a key is pressed_.   Instead, you can only ask, _is the key pressed now?_.  Consider a key used for an _interact_ action in a game. You might approach a light switch and press this key, expecting the light to be turned on.  If you used a `bool` to represent if the light was on, and to flip its value where the TODO is located, you might be surprised by the result.  Remember, a frame is updated in a fraction of a second (often 1/30th or 1/60th of a second).  The average human response time is _much_ slower.  So for several frames, the action key will be pressed.  Each time the game updates, the value of the `bool` representing the light will flip.  The light would be on for a frame, off for a frame, on for a frame... rapidly toggling on and off.  Eventually the player will let up the key, and depending on how many frames it was held down, it could be on or off.

## Double-Buffering Input State
The solution to this challenge is to implement the [Double-Buffer Pattern](https://gameprogrammingpatterns.com/double-buffer.html) with the keyboard state.  Instead of one `KeyboardState` object, you need two - one to hold the current state, and one to hold the old state.  Every frame, you copy the new state over the old state, i.e.:

```csharp
public class MyGame : Game {
  private KeyboardState oldKeyboardState;
  private KeyboardState newKeyboardState;

  ...

  public void override update(GameTime gameTime)
  {
    newKeyboardState = Keyboard.GetState();

    // TODO: Update game

    oldKeyboardState = newKeyboardState;
    base.Update(gameTime);
  }
}
```

Now you can use the values of both the `oldKeyboardState` and `newKeyboardState` to determine if the key was just pressed this frame:

```csharp
if(!oldKeyboardState.IsKeyDown(Keys.A) && newKeyboardState.IsKeyDown(Keys.A)) {
  // Key was pressed - flip the switch
  lights = !lights;
}
```

Similarly, you can determine if the key was released this frame:

```csharp
if(oldKeyboardState.IsKeyDown(Keys.A) && !newKeyboardState.IsKeyDown(Keys.A)) {
  // Key was released
}
```

This corresponds to the `KeyDown` and `KeyUp` events - triggered when the key is first pressed, or first released.
