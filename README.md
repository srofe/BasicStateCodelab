# Basic State Codelab

This application is based on the 
[State in Jetpack Compose](https://developer.android.com/codelabs/jetpack-compose-state) 
which is part of the 
[Jetpack Compose Pathway](https://developer.android.com/courses/pathways/compose).

## Initial State
After creating the project a `WaterCounter` composable is created which holds a 
static state value in a `count` variable. A `WellnessScreen` composable is also 
created which contains the `WaterCounter` composable. This is used to replace 
the `Greeting` composable which is included in the `MainActivity.kt` file when a 
new Compose project is created.

The initial `WaterCounter` composable is:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    val count = 0

    Text(
        text = "You've had $count glasses.",
        modifier = modifier.padding(16.dp)
    )
}
```
This contains a static state value: `count`. Note that `count` will always be 
zero each time `WaterCounter` is recomposed, and because it is a constant
value - since it is a `val` not a `var` and cannot be changed. 
