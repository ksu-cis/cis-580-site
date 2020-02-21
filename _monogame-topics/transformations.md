---
title: "Transformations and Coordinate Systems"
layout: topic
---

A core aspect of many games is the need to move objects around the game's world.  With simple 2D games, this can be easily accomplished by assigning each game object a Vector2 representing its position in the world, and then adding or subtracting from that vector.  But what if we want our perspective in the game world to change as well?  In a platformer, we might want to have the screen scroll left or right as the player moves through the level.  In a scrolling shooter, we may want the world to pass by under our plane.  How can we accomplish this task?

A first approach might be to continue to use a position vector for each of our game objects, and add a second Vector2 to keep track of the scrolling in the world.  Then, when we draw the character sprites, we could add this scrolling vector to the sprite's position to position the part of the world we want to see on-screen:

```csharp
public void Draw() {
  spriteBatch.Begin();
  foreach(GameObject go in gameObjects) {
    spriteBatch.Draw(go.Texture, go.Position + ScrollOffset, Color.White);
  }
  spriteBatch.End();
}
```

What we are effectively doing here is _transforming our coordinate system_.  Think about the coordinate system of our world.  Each game object has an X and Y which is measured from the origin of that world.  Our screen _also_ has a coordinate system, with its origin in the upper-right hand corner, and X values increasing to the right, and Y values increasing as we move down the screen.  In simple games with no scrolling, _these two coordinate systems are the same_.  They effectively overlap.  

But when we add scrolling, they can diverge.  The `ScrollOffset` vector is the measure of this divergence - it tells us how far our world coordinate system is from our screen coordinate system.  When we add the `ScrollOffset` Vector2 to our game objects' position vectors, we are _transforming_ that position from world coordinates into screen coordinates.  We pass the transformed position to the `SpriteBatch.Draw()` method so that the sprite appears in the proper position on the screen.

## Transformation Matrices

An alternative way to represent a transformation is to use a [Matrix](https://en.wikipedia.org/wiki/Matrix_(mathematics)).  A square matrix one dimension larger than the coordinate system can be used to represent any arbitrary transformation.  When we multiply the Vector2 by a transform Matrix, the result is the transformed version of the original Vector2.  While we can represent any arbitrary transformation, but the most commonly used in games are _translation_, _rotation_, and _scaling_.

### Translation Matrix
The translation matrix encodes a translation along the coordinate axes by a set amount (the t<sub>x</sub> and t<sub>y</sub> in the diagram).  Mathematically, its effect is identical to adding the `ScrollOffset` vector described above.

![Translation Matrix](assets/translation-matrix.svg)

However, because Monogame uses the graphics card, we can pass this transformation matrix to the SpriteBatch in our `SpriteBatch.Begin()` call, and offload the calculation from our CPU to our GPU.  This both simplifies our code (as we only have to create one translation matrix), and increases our efficiency (as the GPU is optimized for this kind of operation).

In MonoGame, a translation matrix can be created with the method [Matrix.CreateTranslation(float x, float y, float z)](http://www.monogame.net/documentation/?page=M_Microsoft_Xna_Framework_Matrix_CreateTranslation).  When using the matrix for 2D translations, use a value of `0` for the `z` argument.

### Rotation Matrix
The rotation matrix encodes a rotation about one of the primary axes.  For a 2D game, this would be the z-axis (the one normal to the screen).  The angle is provided in radians.  Note that this rotation occurs _around the origin_, thus you would typically apply rotations as part of a compound operation (see Concatenating Transformation Matrices, below).

![Rotation Matrix](assets/rotation-matrix.svg)

In MonoGame, a rotation matrix can be created with the method [Matrix.CreateRotationZ(float angle)](http://www.monogame.net/documentation/?page=M_Microsoft_Xna_Framework_Matrix_CreateRotationZ_1)

### Scaling Matrix
A scaling matrix encodes scaling along the primary axes.  In a 2D game, you would probably use a value of 0 for scaling along the z axis.  Note that this scaling occurs _relative to the origin_, thus you would typically apply scaling as part of a compound operation (see Concatenating Transformation Matrices, below).

In MonoGame, a scaling matrix can be created with the method [Matrix.CreateScale(float x, float y, float z)](http://www.monogame.net/documentation/?page=M_Microsoft_Xna_Framework_Matrix_CreateScale_1).  

### Concatenating Transformation Matrices

One of the unique benefits of using matrices for transform operations is that we can _contactenate_ multiple transformations together by multiplying their corresponding matrices.  For example, if we wanted to rotate a ship sprite around its center, we would first need to translate it so that its center fell at the origin (Matrix T), apply the rotation (Matrix R), and then translate it back (Matrix T<sup>-1</sup>).  These three operations can be concatenated in a single matrix by multiplying the three matrices together (C = T * R * T<sup>-1</sup>).

Multiplying a vector by this matrix effectively applies all three operations in order.  Thus, we can concisely represent a series of transformations as a single matrix.  We can apply a transformation matrix to a Vector2 with the static [Vector2.Transform(Vector2, Matrix)](http://www.monogame.net/documentation/?page=M_Microsoft_Xna_Framework_Vector2_Transform_1) method.

### SpriteBatch and Transformation Matrices
The SpriteBatch's Draw() method overloads use matrices internally to transform sprites - several take float values for scale and rotation.  In addition, these overloads also take a Vector2 specifying the origin (relative to the upper-left-hand corner of the sprite) around which this scaling and rotation should take place.  

Additionally, SpriteBatch.Start() can take a transformation matrix that will be _applied to all sprites rendered within that call_.  Going back to our platformer and scrolling shooter examples at the beginning of the article, this means we would not have to add any offset vectors - simply specify the translation at the start of the rendering cycle:

```csharp
public void Draw() {
  Matrix translation = Matrix.CreateTranslation(scrollOffset.X, scrollOffset.Y, 0);
  spriteBatch.Begin(SpriteSortMode.Deferred, null, null, null, null, null, translation);
  foreach(GameObject go in gameObjects) {
    spriteBatch.Draw(go.Texture, go.Position, Color.White);
  }
  spriteBatch.End();
}
```
