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

## Events in Compose
To enable `count` to be updated a `Button` composable is added to `WaterCounter`
which increments `count` each time the button is tapped by implementing an 
`onClick` event handler:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    var count = 0

    Column(modifier = modifier.padding(16.dp)) {
        Text(
            text = "You've had $count glasses.",
        )
        Button(
            onClick = { count++ },
            Modifier.padding(top = 8.dp)
        ) {
            Text("Add one")
        }
    }
}
```
When running the application and tapping the button the `count` that is 
displayed does not change since (as noted above) each time `WaterCounter` is 
recomposed, the value of `count` is set to zero (since this is the value it is 
set to each time the `WaterCounter` function is called).

Also, Compose will not detect any change in the state of `count` because it has 
not been asked to do so. So, tapping the button doesn't actually force any 
recomposition.

## Memory in a Composable Function
In order to have Compose track the state of property, we need to use Compose's 
`State` or `MutableState` types. This enables recomposition to be triggered 
when a state components `value` changes. 
