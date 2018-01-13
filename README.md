# ionic-cheatsheet

## npm
### When you need to sudo while you should NOT
```
sudo chown -R $(whoami) /usr/local/lib/node_modules
sudo chown -R $(whoami) /usr/local/bin
sudo chown -R $(whoami) /usr/local/share
```

## Typescript
### Articles
- [Module resolution](https://www.typescriptlang.org/docs/handbook/module-resolution.html)
- [Pitfalls for namespaces and modules](https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html)


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

