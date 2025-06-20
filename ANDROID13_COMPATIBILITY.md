# Android 13+ Compatibility Changes

This document outlines the changes made to ensure compatibility with Android 13+ (API level 33+).

## Changes Made

### 1. Build Configuration Updates (`android/build.gradle`)

- **compileSdkVersion**: Updated from 27 to 34
- **buildToolsVersion**: Updated from '27.0.3' to '34.0.0'
- **minSdkVersion**: Updated from 16 to 21
- **targetSdkVersion**: Updated from 27 to 34
- **Dependencies**: Added `androidx.core:core:1.10.0` for Android 13+ compatibility

### 2. AndroidManifest.xml Updates

Added necessary permissions for Android 13+:

- `android.permission.POST_NOTIFICATIONS` - Required for notification permissions on Android 13+
- `android.permission.BROADCAST_CLOSE_SYSTEM_DIALOGS` - For cross-package broadcast receivers
- `android.permission.WAKE_LOCK` - For DataWedge integration

### 3. Java Code Updates (`RNDataWedgeIntentsModule.java`)

#### New Imports Added:

```java
import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Build;
import androidx.core.content.ContextCompat;
```

#### Broadcast Receiver Registration Updates:

- Added `RECEIVER_EXPORTED` flag for cross-package broadcasts on Android 13+
- Created helper method `registerReceiverWithCompatibility()` for consistent handling
- Updated all broadcast receiver registrations to use the new helper method

#### New Methods Added:

- `hasRequiredPermissions()` - Checks for required permissions on Android 13+
- `registerReceiverWithCompatibility()` - Handles broadcast receiver registration with Android 13+ compatibility
- `checkAndroid13Permissions(Callback callback)` - React method to check permission status

## Usage

### Checking Android 13+ Permissions

```javascript
import { NativeModules } from "react-native";

const { DataWedgeIntents } = NativeModules;

// Check if running on Android 13+ and if notification permission is granted
DataWedgeIntents.checkAndroid13Permissions((result) => {
  console.log("Is Android 13+:", result.isAndroid13Plus);
  console.log("Has notification permission:", result.hasNotificationPermission);
});
```

### Requesting Permissions (in your React Native app)

For Android 13+, you may need to request notification permissions:

```javascript
import { PermissionsAndroid, Platform } from "react-native";

const requestNotificationPermission = async () => {
  if (Platform.OS === "android" && Platform.Version >= 33) {
    try {
      const granted = await PermissionsAndroid.request(PermissionsAndroid.PERMISSIONS.POST_NOTIFICATIONS, {
        title: "Notification Permission",
        message: "This app needs notification permission to receive DataWedge broadcasts.",
        buttonNeutral: "Ask Me Later",
        buttonNegative: "Cancel",
        buttonPositive: "OK",
      });
      return granted === PermissionsAndroid.RESULTS.GRANTED;
    } catch (err) {
      console.warn(err);
      return false;
    }
  }
  return true;
};
```

## Breaking Changes

1. **Minimum SDK**: Increased from 16 to 21
2. **Target SDK**: Updated to 34 (Android 14)
3. **Dependencies**: Now requires `androidx.core:core:1.10.0`

## Migration Guide

1. Update your app's `build.gradle` to use the new SDK versions
2. Request notification permissions on Android 13+ if needed
3. Test broadcast receiver functionality on Android 13+ devices
4. Update any custom broadcast receiver registrations to use the new helper methods

## Testing

Test the following scenarios on Android 13+ devices:

- Broadcast receiver registration and unregistration
- DataWedge intent reception
- Scanner enumeration
- Permission handling

## Notes

- The module now uses `RECEIVER_EXPORTED` flag for cross-package broadcasts on Android 13+
- All broadcast receivers are properly handled for both older and newer Android versions
- Permission checks are included for Android 13+ specific requirements
- Backward compatibility is maintained for Android versions below 13
