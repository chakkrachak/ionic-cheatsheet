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

## Display a map
### The good plugin
- [Ionic reference](https://ionicframework.com/docs/native/google-maps/)
- [Repo GitHub](https://github.com/mapsplugin/cordova-plugin-googlemaps)

### app.module.ts
```typescript
import {BrowserModule} from '@angular/platform-browser';
import {ErrorHandler, NgModule} from '@angular/core';
import {IonicApp, IonicErrorHandler, IonicModule} from 'ionic-angular';
import {SplashScreen} from '@ionic-native/splash-screen';
import {StatusBar} from '@ionic-native/status-bar';

import {MyApp} from './app.component';
import {HomePage} from '../pages/home/home';
import {GoogleMaps} from '@ionic-native/google-maps';

@NgModule({
    declarations: [
        MyApp,
        HomePage
    ],
    imports: [
        BrowserModule,
        IonicModule.forRoot(MyApp)
    ],
    bootstrap: [IonicApp],
    entryComponents: [
        MyApp,
        HomePage
    ],
    providers: [
        StatusBar,
        SplashScreen,
        {provide: ErrorHandler, useClass: IonicErrorHandler},
        GoogleMaps
    ]
})
export class AppModule {
}
```

### home.html
```html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
    <div id="map_canvas">
        <button ion-button (click)="onButtonClick($event)">Demo</button>
    </div>
</ion-content>
```

### home.scss
```scss
page-home {
    #map_canvas {
        height: 90%;
    }
}
```

### home.ts
```typescript
import { Component } from "@angular/core/";
import {
    GoogleMaps,
    GoogleMap,
    GoogleMapsEvent,
    Marker,
    GoogleMapsAnimation,
    MyLocation
} from '@ionic-native/google-maps';

import { Platform } from 'ionic-angular';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {

    mapReady: boolean = false;
    map: GoogleMap;

    constructor(private googleMaps: GoogleMaps, public platform: Platform) {
        this.platform.ready().then(() => {
            this.loadMap();
        })
    }

    loadMap() {
        // Create a map after the view is loaded.
        // (platform is already ready in app.component.ts)
        this.map = this.googleMaps.create('map_canvas', {
            camera: {
                target: {
                    lat: 48.8467892,
                    lng: 2.3749036
                },
                zoom: 18
            }
        });

        // Wait the maps plugin is ready until the MAP_READY event
        this.map.one(GoogleMapsEvent.MAP_READY).then(() => {
            this.mapReady = true;
        });
    }

    onButtonClick() {
        if (!this.mapReady) {
            this.showToast('map is not ready yet. Please try again.');
            return;
        }
        this.map.clear();

        return this.map.animateCamera({
            target: {
                lat: 48.8467892,
                lng: 2.3749036
            },
            zoom: 17,
            duration: 0
        }).then(() => {
            // add a marker
            return this.map.addMarker({
                title: '@ionic-native/google-maps plugin!',
                snippet: 'This plugin is awesome!',
                position: {
                    lat: 48.8467892,
                    lng: 2.3749036
                }
            });
        })
    }

    showToast(message: string) {
        alert(message);
    }
}
```

## Using geolocation
### The good plugin
- [Ionic reference](https://ionicframework.com/docs/native/geolocation/)

### app.module.ts
```typescript
import {BrowserModule} from '@angular/platform-browser';
import {ErrorHandler, NgModule} from '@angular/core';
import {IonicApp, IonicErrorHandler, IonicModule} from 'ionic-angular';
import {SplashScreen} from '@ionic-native/splash-screen';
import {StatusBar} from '@ionic-native/status-bar';
import {Geolocation} from '@ionic-native/geolocation';

import {MyApp} from './app.component';
import {HomePage} from '../pages/home/home';

@NgModule({
    declarations: [
        MyApp,
        HomePage
    ],
    imports: [
        BrowserModule,
        IonicModule.forRoot(MyApp)
    ],
    bootstrap: [IonicApp],
    entryComponents: [
        MyApp,
        HomePage
    ],
    providers: [
        StatusBar,
        SplashScreen,
        Geolocation,
        {provide: ErrorHandler, useClass: IonicErrorHandler}
    ]
})
export class AppModule {
}
```

### home.ts
```typescript
import {Component} from '@angular/core';
import {NavController, Platform} from 'ionic-angular';
import {Geolocation} from '@ionic-native/geolocation';

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {

    constructor(public navCtrl: NavController, private geolocation: Geolocation, private platform: Platform) {
        this.platform.ready().then(() => {
            this.getLocation();
        })
    }

    getLocation() {
        this.geolocation.getCurrentPosition({
            timeout: 300000,
            enableHighAccuracy: true,
            maximumAge: 3600
        }).then((resp) => {
            alert(resp.coords.latitude + ' ' + resp.coords.longitude);
        }).catch((error) => {
            alert('Error getting location : ' + JSON.stringify(error));
        });
    }
}
```
### config.xml (if you are using iOS > 11)
Add this 
```xml
<edit-config target="NSLocationWhenInUseUsageDescription" file="*-Info.plist" mode="merge">
    <string>your custom text here</string>
</edit-config>
```
    
## Reading a local json file (some quick and dirty way)
### An example file
- [Opendata](https://opendata.paris.fr/explore/dataset/liste-des-cafes-a-un-euro/export/) 

### app.module.ts
```typescript
import {BrowserModule} from '@angular/platform-browser';
import {ErrorHandler, NgModule} from '@angular/core';
import {IonicApp, IonicErrorHandler, IonicModule} from 'ionic-angular';
import {SplashScreen} from '@ionic-native/splash-screen';
import {StatusBar} from '@ionic-native/status-bar';

import {MyApp} from './app.component';
import {HomePage} from '../pages/home/home';
import {HttpModule} from "@angular/http";

@NgModule({
    declarations: [
        MyApp,
        HomePage
    ],
    imports: [
        BrowserModule,
        IonicModule.forRoot(MyApp),
        HttpModule
    ],
    bootstrap: [IonicApp],
    entryComponents: [
        MyApp,
        HomePage
    ],
    providers: [
        StatusBar,
        SplashScreen,
        {provide: ErrorHandler, useClass: IonicErrorHandler}
    ]
})
export class AppModule {
}
```

### home.ts
```typescript
import {Component, NgZone} from '@angular/core';
import {NavController, Platform} from 'ionic-angular';
import {Http} from "@angular/http";
import "rxjs/add/operator/map";

@Component({
    selector: 'page-home',
    templateUrl: 'home.html'
})
export class HomePage {
    pois: Array<Poi> = [];

    constructor(public navCtrl: NavController, private platform: Platform, private zone: NgZone, private http: Http) {
        platform.ready().then(() => {
            this.http.get('../../assets/data/pois.json')
                .map(res => res.json())
                .subscribe((jsonPois) => {
                    jsonPois.map(rawPoi => {
                        let poi: Poi = new Poi();

                        poi.name = rawPoi.fields.nom_du_cafe;
                        poi.displayInformation = {
                            addressLabel: rawPoi.fields.address + ' ' + rawPoi.fields.arrondissement + ' Paris'
                        };

                        if (rawPoi.geometry !== undefined) {
                            poi.coords = {
                                lat: rawPoi.geometry.coordinates[0],
                                lon: rawPoi.geometry.coordinates[1]
                            };
                        }

                        return poi;
                    }).filter((poi: Poi) => {
                        return ((poi.coords !== undefined) && (poi.displayInformation !== undefined) && (poi.displayInformation.addressLabel !== undefined));
                    }).map((poi: Poi) => {
                        this.zone.run(() => {
                            this.pois.push(poi);
                        });
                    })
                });
        })
    }
}

class Poi {
    name: string;
    coords: {
        lat: number,
        lon: number
    };
    displayInformation: {
        addressLabel: string
    }
}   
``` 

