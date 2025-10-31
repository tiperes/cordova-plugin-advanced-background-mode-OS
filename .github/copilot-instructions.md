# AI Agent Instructions for cordova-plugin-advanced-background-mode

## Project Overview
This is a Cordova plugin that enables infinite background execution for mobile applications. The plugin supports:
- Android/Amazon FireOS
- iOS
- Browser

## Architecture

### Core Components
- `src/android/BackgroundMode.java`: Main Android implementation
  - Manages background service lifecycle
  - Handles notifications and permissions
  - Communicates with JavaScript layer

- `src/ios/APPBackgroundMode.m`: Main iOS implementation
  - Uses audio session tricks to keep app alive
  - Handles app lifecycle events
  - Manages WebKit configuration

- `www/background-mode.js`: JavaScript interface
  - Provides API for Cordova apps
  - Handles events and configuration
  - Bridge between native code and web layer

### Key Patterns
1. Event System
   ```javascript
   // Events: enable, disable, activate, deactivate, failure
   cordova.plugins.backgroundMode.on('activate', callback);
   ```

2. Configuration Objects
   ```javascript
   // Use for notification customization
   cordova.plugins.backgroundMode.setDefaults({
     title: String,
     text: String,
     icon: String,
     color: String, // Android only
     ...
   });
   ```

## Development Workflows

### Android-specific
1. Permission handling for Android 13+:
   ```java
   // Check and request POST_NOTIFICATIONS permission
   cordova.plugins.backgroundMode.requestPermissions();
   ```

2. Service lifecycle follows:
   - `onPause()` -> Start background service
   - `onResume()` -> Stop service
   - `onDestroy()` -> Clean up

### iOS-specific
1. Audio session configuration is critical:
   - Uses silent audio to keep app alive
   - Handles interruptions (e.g., phone calls)
   - Requires background audio capability

## Common Pitfalls
1. Never modify `ForegroundService.java` without updating corresponding notification handling in `BackgroundMode.java`
2. iOS background audio must be configured in both native code and `plugin.xml`
3. Android notification settings must be handled differently for API 26+ (Oreo) vs older versions

## Testing
1. Test mode transitions:
   - Foreground -> Background
   - Background -> Foreground
   - Lock screen behavior
2. Test notification behavior on different Android versions
3. Verify iOS audio session handling with other audio apps

## Integration
1. Installation requires proper permissions in:
   - Android: `plugin.xml` manifest entries
   - iOS: Background mode capabilities
2. Configuration should be done early in app lifecycle:
   ```javascript
   document.addEventListener('deviceready', () => {
     cordova.plugins.backgroundMode.enable();
   }, false);
   ```