# Visualization Features - "What Can You See?"

This document describes the visualization capabilities added to the BakkesModSDK to help plugin developers understand what can be "seen" in the game.

## New Methods Added

### ActorWrapper::IsVisible()

**Location:** `include/bakkesmod/wrappers/Engine/ActorWrapper.h`

This method provides a convenient way to check if an actor is visible in the game world. It checks the actor's visibility flags (`bHidden` and `bHiddenSelf`) to determine if the actor should be rendered.

**Usage Example:**
```cpp
ActorWrapper actor = GetSomeActor();
if (actor.IsVisible()) {
    // Actor is visible, proceed with rendering logic
}
```

### CanvasWrapper::IsOnScreen()

**Location:** `include/bakkesmod/wrappers/canvaswrapper.h`

Two overloaded methods to check if a position is visible on the screen:
- `bool IsOnScreen(Vector location)` - Checks if a 3D world position is on screen
- `bool IsOnScreen(Vector2 screenPos)` - Checks if a 2D screen position is within viewport bounds

**Usage Example:**
```cpp
CanvasWrapper canvas = GetCanvas();
Vector worldPos = ball.GetLocation();

if (canvas.IsOnScreen(worldPos)) {
    Vector2 screenPos = canvas.Project(worldPos);
    // Draw something at the screen position
    canvas.SetPosition(screenPos);
    canvas.DrawString("Ball");
}
```

### Circle Drawing Methods

**Location:** `include/bakkesmod/wrappers/canvaswrapper.h`

New shape drawing capabilities for circular visualizations:
- `void DrawCircle(Vector2 center, float radius)` - Draw circle outline with default segments
- `void DrawCircle(Vector2 center, float radius, int segments)` - Draw circle outline with custom segment count
- `void FillCircle(Vector2 center, float radius)` - Draw filled circle with default segments
- `void FillCircle(Vector2 center, float radius, int segments)` - Draw filled circle with custom segment count

**Usage Example:**
```cpp
CanvasWrapper canvas = GetCanvas();
canvas.SetColor(255, 0, 0, 255); // Red
canvas.DrawCircle(Vector2{100, 100}, 50); // Draw circle at (100,100) with radius 50
canvas.FillCircle(Vector2{200, 200}, 30, 32); // Draw filled circle with 32 segments
```

## Combining Features

These features can be combined to create powerful visualization plugins:

```cpp
void OnRender(CanvasWrapper canvas) {
    ServerWrapper server = gameWrapper->GetCurrentGameState();
    if (server.IsNull()) return;

    ArrayWrapper<CarWrapper> cars = server.GetCars();
    for (int i = 0; i < cars.Count(); i++) {
        CarWrapper car = cars.Get(i);
        
        // Check if car is visible
        if (!car.IsVisible()) continue;
        
        Vector carLocation = car.GetLocation();
        
        // Check if car is on screen
        if (canvas.IsOnScreen(carLocation)) {
            Vector2 screenPos = canvas.Project(carLocation);
            
            // Draw a circle around visible cars on screen
            canvas.SetColor(0, 255, 0, 200);
            canvas.FillCircle(screenPos, 5);
        }
    }
}
```

## Summary

These visualization features answer the question "what can you see?" by providing:

1. **Visibility Checking** - Determine if actors are set to be visible in the game world
2. **Screen Presence Detection** - Identify if objects are within the visible viewport
3. **Enhanced Drawing Capabilities** - Draw circular shapes to visualize points, areas, and ranges

These additions make it easier for plugin developers to create informative overlays and visualizations that respond to what's actually visible in the game.
