# A clean approach to deal with Permissions in Android

This small project shows a clean and simplified approach for the recommended workflow for requesting permissions in Android.

## Dependencies

You need to use AndroidX Fragment 1.3.0 or higher.

```
implementation "androidx.fragment:fragment-ktx:1.3.0-rc01"
```

## How to use

Using it is as simple as registering the PermissionManager with your Fragment and, whenever you want to request permissions, you just have to send what permissions you need, a rationale of why you're requesting those permissions for a second time, and then simply get the results as a callback:

```kotlin
class YourFragment : Fragment() {

    private val permissionManager = PermissionManager.from(this)
    yourView.setOnClickListener {
        permissionManager
            .request(Permission.Camera)
            .rationale("We need permission to use the camera")
            .checkPermission { granted: Boolean ->
                if (granted) {
                    // Do something with the camera
                } else {
                    // You can't access the camera
                }
            }
    }
}
```

## Requesting multiple permissions simultaneously

**Option 1:** Simply add a new entry to the Permission class with your required permissions:

```kotlin
sealed class Permission(vararg val permissions: String) {
    // Individual permissions
    object Camera : Permission(CAMERA)

    // Bundled permissions for MyFeature
    object MyFeature : Permission(CAMERA, WRITE_EXTERNAL_STORAGE)
}
```

**Option 2:** Send as many Permission as you require to the PermissionManager:

```kotlin
permissionManager.request(Permission.Camera, Permission.Storage, Permission.Location)
```

## Checking permission results individually

`checkPermission { }` will return a Boolean telling you whether all permissions were granted or not.
If you want more granular control about which permissions were granted or declined, you can use `checkDetailedPermission { }` to receive a `Map<Permission, Boolean>` with the results:

```kotlin
permissionManager
    .request(Permission.Camera, Permission.Storage)
    .rationale("We need two permissions at once!")
    .checkDetailedPermission { result: Map<Permission, Boolean> ->
        if (result.all { it.value }) {
            // We have all the permissions
        } else {
            // Check in result which permission was denied
        }
    }
```