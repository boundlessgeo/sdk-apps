# Tutorial: Quickview

## Introduction
This tutorial takes you through the steps to create a BoundlessSDK application.

## The end game // What you'll build
[http://boundlessgeo.github.io/sdk-apps/quickview](http://boundlessgeo.github.io/sdk-apps/quickview)

## Getting Started
Follow getting started instructions to create a new app called myapp.

[https://boundlessgeo.github.io/sdk/book/getting_started.html](https://boundlessgeo.github.io/sdk/book/getting_started.html)

This creates a skeleton app and runs a debug server on port 3000.

## What's in the BoundlessSDK skeleton app `myapp`?
* Two base map layers
  * OSM Streets
  * ESRI World Images
* Two widgets/components
  * Zoom In/Out Buttons
  * `LayerList` Component

## Inside of `myapp/app.jsx`
`app.jsx` is the main file for this BoundlessSDK app.
* initial import statements
  ```
  import React from 'react';
  import ReactDOM from 'react-dom';
  import ol from 'openlayers';
  import {addLocaleData, IntlProvider} from 'react-intl';
  import getMuiTheme from 'material-ui/styles/getMuiTheme';
  import Zoom from '@boundlessgeo/sdk/components/Zoom';
  import MapPanel from '@boundlessgeo/sdk/components/MapPanel';
  import LayerList from '@boundlessgeo/sdk/components/LayerList';
  import injectTapEventPlugin from 'react-tap-event-plugin';
  import enLocaleData from 'react-intl/locale-data/en';
  import enMessages from '@boundlessgeo/sdk/locale/en';
  ```
* `ol.Map` definition
  * The Map gets defined with a layer group, that combines the OSM streets and ESRI world imagery layers.
  * The view is defined with an initial center and zoom level
    * To change center with lat/long coordinates, use:
        `center: ol.proj.fromLonLat([long, lat])`
  ```
  var map = new ol.Map({
    controls: [new ol.control.Attribution({collapsible: false})],
    layers: [
      new ol.layer.Group({
        type: 'base-group',
        title: 'Base maps',
        layers: [
          new ol.layer.Tile({
            type: 'base',
            title: 'OSM Streets',
            source: new ol.source.OSM()
          }),
          new ol.layer.Tile({
            type: 'base',
            title: 'ESRI world imagery',
            visible: false,
            source: new ol.source.XYZ({
              attributions: [
                new ol.Attribution({
                  html:['Tiles &copy; Esri &mdash; Source: Esri, i-cubed, USDA, USGS, AEX, GeoEye, Getmapping, Aerogrid, IGN, IGP, UPR-EGP, and the GIS User Community']
                })
              ],
              url: 'http://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}'
            })
          })
        ]
      })
    ],
    view: new ol.View({
      center: [0, 0],
      zoom: 4
    })
  });
  ```

## Adding base maps to `myapp` BoundlessSDK app
The skeleton app provides two base maps, OSM Streets and ESRI World Imagery. We’ll add two more to `myapp`: Carto DB Light and Carto DB Dark

Add the following layer definitions in `app.jsx` after the existing OSM Streets layer:

```
new ol.layer.Tile({
  type: 'base',
  title: 'CartoDB light',
  visible: false,
  source: new ol.source.XYZ({
    url: 'http://s.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png',
    attributions: [
      new ol.Attribution({
        html: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, &copy; <a href="http://cartodb.com/attributions">CartoDB</a>'
      })
    ]
  })
}),
new ol.layer.Tile({
  type: 'base',
  title: 'CartoDB dark',
  visible: false,
  source: new ol.source.XYZ({
    url: 'http://s.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png',
    attributions: [
      new ol.Attribution({
        html: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, &copy; <a href="http://cartodb.com/attributions">CartoDB</a>'
      })
    ]
  })
}),
```

Reload the debug server to see the new base map options we've just added.

## Adding widgets (components) on `myapp` BoundlessSDK app
The first widget/component we'll add is called `GeoLocation`.

What it does: [https://boundlessgeo.github.io/sdk/book/api/Geolocation.html](https://boundlessgeo.github.io/sdk/book/api/Geolocation.html)
  * Geolocation uses the current position of the user in the map. Can show the current position on the map, and also track the position.
    `<Geolocation map={map} />`

* In `app.jsx`

  * import the component
    * import the `Geolocation` component by adding an import statement to the top of `app.jsx`, after the existing import statements

          `import Geolocation from '@boundlessgeo/sdk/components/Geolocation';`

  * update the render function
    * In the render function of `myapp`, we need to add the definition of our new component, `Geolocation`, after the existing `Zoom` component

          `<div id='geolocation-control'><Geolocation map={map} /></div>`

* In `app.css`

  * adjust style
    * in `app.css`, we'll adjust the styling to give the new `Geolocation` component a position on the MapPanel


          ```
          #geolocation-control {
            position: absolute;
            margin-left: 20px;
            top: 129px;
          }
          ```

Reload the debug server and see the new widget we've just added.

## Adding a `Header` to `myapp` BoundlessSDK `myapp`
The `Header` will be a landing spot at the top of the app page for menu buttons to be added later.

* In `app.jsx`

  * import the component
    * `import Header from '@boundlessgeo/sdk/components/Header';`

  * update the render function
    * In our render function, make a new `var header` to house the `Header` component and all children components to be added later

    * We'll give the `Header` component a `title` property to name our app, in this case, we'll call it 'Boundless SDK Quickview':

    ```
    render() {
      var header = (
        <Header title='Boundless SDK Quickview/>
      );
      return (
         <div id='content'>
           {header}
          <MapPanel id='map' map={map} />
          <div id='layer-list'><LayerList collapsible={false} map={map} /></div>
          <div id='zoom-buttons'><Zoom map={map} /></div>
          <div id='geolocation-control'><Geolocation map={map} /></div>
        </div>
      );
    }
    ```

* In `app.css`

  * adjust style

    ```
    #map {
      height: calc(100% - 56px);
    }
    #content {
      width: 100%;
      height: 100%;
      position: fixed;
      bottom: 0px;
    }
    #zoom-buttons {
      margin-left: 20px;
      position: absolute;
      top: 76px;
    }
    #layer-list {
      position: absolute;
      top: 76px;
      right: 20px;
    }
    #geolocation-control {
      position: absolute;
      margin-left: 20px;
      top: 185px;
    }
    ```

Reload the debug server to see the new `Header`.

## Adding a menu button to `myapp` BoundlessSDK app
The first button will add will be a `Measure` component

What it does:
[https://boundlessgeo.github.io/sdk/book/api/Measure.html](https://boundlessgeo.github.io/sdk/book/api/Measure.html)
  * `Measure` adds area and length measure tools as a menu button
    `<Measure map={map}/>`

* In `app.jsx`

  * import the component
    `import Measure from '@boundlessgeo/sdk/components/Measure';`

  * update the render function
    ```
    var header = (
      <Header><Measure map={map}/></Header>
    );
    ```
* No style updates needed for this addition

Reload the debug server and try out the `Measure` button

## Adding a widget from `ol.Map` object in `myapp` BoundlessSDK app
First we'll add a `ScaleLine` directly into our `ol.Map` object

What it does:
[https://openlayers.org/en/latest/apidoc/ol.control.ScaleLine.html](https://openlayers.org/en/latest/apidoc/ol.control.ScaleLine.html)
  * `ScaleLine` is a control displaying rough y-axis distances, calculated for the center of the viewport.
    `ol.control.ScaleLine()`

* In `app.jsx`

  * Update the `ol.Map` controls array to include the `ScaleLine`
    ```
    controls: [new ol.control.Attribution({collapsible: false}), new ol.control.ScaleLine()],
    ```

* In `app.css`

  * style the `ScaleLine` to look like the other widgets on the `MapPanel`

    ```
    .ol-scale-line {
      background-color: #0097a7;
    }
    ```

Reload the server to see the new widget

## Adding the `LeftNav` component

Quickview uses a `LeftNav` to display the `LayerList` and other tabs. First, we'll walkthrough displaying the empty `LeftNav` component.

* In `app.jsx`

  * import the component

    `import LeftNav from '@boundlessgeo/sdk/components/LeftNav';`

  * update the render function

    `<LeftNav open={this.state.leftNavOpen} onRequestClose={this.leftNavClose.bind(this)}/>`

    `LeftNav` will take two properties, `open` and `onRequestClose`.

    * `open`: for open, we'll need to set state.

      At the top of the `MyApp` class, add a constructor:

        ```
        constructor(props){
          super(props);
          this.state = {
            leftNavOpen: true
          };
        }
        ```

      After the constructor, we'll need to define `leftNavOpen` and `leftNavClose`.

        ```
        leftNavOpen(value) {
          this.setState({
            leftNavOpen: true
            }, function() {
              map.updateSize();
            });
        }
        leftNavClose(value) {
          this.setState({
            leftNavOpen: false
            }, function() {
              map.updateSize();
            });
        }
        ```

      Next, add a `onLeftIconTouchTap` property to the `Header` component to open/close the `LeftNav`.

        `onLeftIconTouchTap={this.leftNavOpen.bind(this)}`

     Finally, we'll update the style directly in `app.jsx` by adding a new `div` inside of `<div id='content'>`, to contain the `MapPanel` and other components that appear on the map.

     ```
     <div id='content'>
       {header}
       <LeftNav open={this.state.leftNavOpen} onRequestClose={this.leftNavClose.bind(this)}/>
       <div className='map' style={{left: this.state.leftNavOpen ? 360 : 0, width: this.state.leftNavOpen ? 'calc(100% - 360px)' : '100%'}}>
         <MapPanel id='map' map={map} />
         <div id='layer-list'><LayerList collapsible={false} map={map} /></div>
         <div id='zoom-buttons'><Zoom map={map} /></div>
         <div id='geolocation-control'><Geolocation map={map} /></div>
       </div>
     </div>
     ```

Reload the server to see the new `LeftNav` in place.

## Adding `Tablist` and `Tabs` to `myapp` BoundlessSDK app

QuickView app uses a `Tablist` component to manage its `Layerlist`, `Legend`, and `FeatureTable` components.

* In `app.jsx`

  * import the component

      `import {Tab} from 'material-ui/Tabs';`

  * update the render function

      * At the top of render, we'll add a new `const` called `tabList`

        ```
        const tabList = [
          <Tab
            disableTouchRipple={true}
            key={1}
            value={1}>
            <div id='layer-list'>
              <LayerList
                inlineDialogs={true}
                allowStyling={true}
                expandOnHover={false}
                showOnStart={true}
                allowFiltering={true}
                showOpacity={true}
                showDownload={true}
                showGroupContent={true}
                showZoomTo={true}
                allowReordering={true}
                map={map} />
            </div>
          </Tab>
        ]
        ```

      Inside of return,

## Adding internationalization to `myapp` BoundlessSDK app

BoundlessSDK supports internationalization (i18n) should you wish to adapt your app to support any language than English.

For a working example, see the Tabbed App demo.

[http://boundlessgeo.github.io/sdk-apps/tabbed/#map=9783.93962050256/-16839563.6/8850169.51/0](http://boundlessgeo.github.io/sdk-apps/tabbed/#map=9783.93962050256/-16839563.6/8850169.51/0)
