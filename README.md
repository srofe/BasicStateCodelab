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
when a state components `value` changes:
```Kotlin
val count: MutableState<Int> = mutableStateOf(0)
```
However, this still doesn't change the displayed value of `count` when the 
button is tapped since the recomposition will just re-initialise `count` to 
zero.

To preserve the updated value of `count` Compose needs to `remember` the value.
This can be done using the inline `remember` function:
```kotlin
val count: MutableState<Int> = remember { mutableStateOf(0) }
```

This syntax can be simplified using Kotlin's
[delegated properties](https://kotlinlang.org/docs/delegated-properties.html).
This uses the `by` keyword to define `count` as a variable. This also requires 
importing the `getValue` amd `setValue` for the delegate:
```kotlin
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue
```
Which has the advantage of being able to use `count` directly without explicitly 
referring to it's `value` property:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    var count by remember { mutableStateOf(0) }

    Column(modifier = modifier.padding(16.dp)) {
        Text("You've had ${count} glasses.")
        Button(onClick = { count++ }, Modifier.padding(top = 8.dp)) {
            Text("Add one")
        }
    }
}
```

## State Drive UI
Recomposition cases the UI to be updated which may result in some composables 
entering or leaving the composition. To illustrate this, the `count` parameter 
can be used to hide or display the text showing the count of glasses of water:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    var count by remember { mutableStateOf(0) }

    Column(modifier = modifier.padding(16.dp)) {
        if (count > 0) {
            Text("You've had ${count} glasses.")
        }
        Button(onClick = { count++ }, Modifier.padding(top = 8.dp)) {
            Text("Add one")
        }
    }
}
```
Here the text is displayed only if `count` is greater than zero.

We can also change the state of the Button by linking the `enabled` parameter 
to `count`. For example, to disable the button when the `count` is 10 or 
greater, change the Button definition to:
```kotlin
Button(onClick = { count++ }, Modifier.padding(top = 8.dp), enabled = count < 10) {
            Text("Add one")
}
```

## Remember in Compose
The `remember` inline function stores objects in the composition, however, it 
forgets them if the source location where `remember` is called is not invoked 
during a recomposition. To illustrate this, add a `WellnessTaskItem`:
```kotlin
@Composable
fun WellnessTaskItem(taskName: String, onClose: () -> Unit, modifier: Modifier = Modifier
) {
    Row(modifier = modifier, verticalAlignment = Alignment.CenterVertically) {
        Text(
            modifier = Modifier.weight(1f).padding(start = 16.dp),
            text = taskName
        )
        IconButton(onClick = onClose) {
            Icon(Icons.Filled.Close, contentDescription = "Close")
        }
    }
}
```
Then add an `if` statement to show the task item if a `showTask` flag is true:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    Column(modifier = modifier.padding(16.dp)) {
        var count by remember { mutableStateOf(0) }
        if (count > 0) {
            var showTask by remember { mutableStateOf(true) }
            if (showTask) {
                WellnessTaskItem(
                    onClose = { showTask = false },
                    taskName = "Have you taken your 15 minute walk today?"
                )
            }
            Text("You've had ${count} glasses.")
        }
        Button(onClick = { count++ }, Modifier.padding(top = 8.dp), enabled = count < 10) {
            Text("Add one")
        }
    }
}
```
Initially the task will be shown (when the `count` is greater than zero). Then 
when the `Close` button is tapped for the task it will be hidden.

Next, we add a button to clear the `count`:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    Column(modifier = modifier.padding(16.dp)) {
        var count by remember { mutableStateOf(0) }
        if (count > 0) {
            var showTask by remember { mutableStateOf(true) }
            if (showTask) {
                WellnessTaskItem(
                    onClose = { showTask = false },
                    taskName = "Have you taken your 15 minute walk today?"
                )
            }
            Text("You've had ${count} glasses.")
        }
        Row(Modifier.padding(top = 8.dp)) {
            Button(onClick = { count++ }, enabled = count < 10) {
                Text("Add one")
            }
            Button(onClick = { count = 0 }, Modifier.padding(start = 8.dp)) {
                Text("Clear water count")
            }
        }
    }
}
```
Now, pressing the `Add one` button will increment `count` - which will show 
the task item since `count` is greater than zero. Continuing to tap `Add one`
will continue to increment `count` and the task will still be visible.

Next, press the close/delete `X` for the item, and it will no longer be shown.
Continuing to tap the `Add one` increments `count` and the task is still now 
shown since `showTask` remains false.

Next, press `Clear water count`. This clears `count` which removes all text
since count is now zero. When clicking `Add one` the task is now shown again, 
however, we expect it not to be displayed since we have not changed the value 
of `showTask`. What happens in this case is that `showTask` is forgotten since 
the location of the code where it is remembered is not invoked, so it is 
re-initialised when `var showTask by remember { mutableStateOf(true) }` is 
executed again.

This can be corrected by lifting where the definition of state is to the top 
of the `WaterCounter` function, like:
```kotlin
@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    var count by remember { mutableStateOf(0) }
    var showTask by remember { mutableStateOf(true) }
    
    Column(modifier = modifier.padding(16.dp)) {
        if (count > 0) {
            if (showTask) {
                WellnessTaskItem(
                    onClose = { showTask = false },
                    taskName = "Have you taken your 15 minute walk today?"
                )
            }
            Text("You've had ${count} glasses.")
        }
        Row(Modifier.padding(top = 8.dp)) {
            Button(onClick = { count++ }, enabled = count < 10) {
                Text("Add one")
            }
            Button(onClick = { count = 0 }, Modifier.padding(start = 8.dp)) {
                Text("Clear water count")
            }
        }
    }
}
```
Now neither `count` or `showTask` will be forgotten as the location of their 
definition will be executed each time the `WaterCounter` function is called. 
This is an example of **State Hoisting** - which will also be discussed further 
below.

## Restore State
Loss of state can also occur for other reasons, such as when the device 
orientation is changed. If we run the application, add some water to the 
counter then rotate the device, the `Activity` is recreated and the state is 
forgotten.

While `remember` allows state to be retained across recompositions, it is not 
retained across configuration changes (such as orientation changes). To save 
state across configuration changes, use `rememberSaveable` instead of `remember`:
```kotlin
import androidx.compose.runtime.saveable.rememberSaveable

@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    ...
    var count by rememberSaveable { mutableStateOf(0) }
    ...
}
```

## State Hoisting
Composables with internal state tend to be less reusable and harder to test. To
overcome this, it is better to lift, or hoist, the state to a higher level 
object. 

The general way to create a stateless composable is to replace the state with 
two parameters:
* A `value` of type `T` which contains the current value to dsiplay.
* An `onValueChange(T)-> Unit` event which is used to change `T` to a new value. 

State hoisting also has other benefits:
* **Single Source of Truth**: By moving state instead of duplicating it, there 
 is only one source of truth.
* **Shareable**: Hoisted state can be shared by multiple composables.
* **Interceptable**: Callers to a stateless composable can decide to ignore or 
  modify events before changing the state.
* **Decoupled**: The state for a stateless composable can be stored anywhere -  
 e.g., in a View Model.

This can be done with our example as:
```kotlin
@Composable
fun StatelessCounter(count: Int, onIncrement: () -> Unit, modifier: Modifier = Modifier) {
    Column(modifier = modifier.padding(16.dp)) {
        if (count > 0) {
            Text("You've had $count glasses.")
        }
        Button(onClick = onIncrement, Modifier.padding(top = 8.dp), enabled = count < 10) {
            Text("Add one")
        }
    }
}

@Composable
fun WaterCounter(modifier: Modifier = Modifier) {
    var count by rememberSaveable { mutableStateOf(0) }
    StatelessCounter(count, { count++ }, modifier)
}
```
