# ionic-cheatsheet

## Android
### When your emulator has dns problems
```
cd $ANDROID_HOME/tools
emulator -avd Nexus_5X_API_26 -dns-server 8.8.8.8
```

### When ionic is struggling sending the app to the emulator
- Clean your config.xml
- Restart your adb server

```
adb kill-server && adb forward --remove-all
adb start-server
```

