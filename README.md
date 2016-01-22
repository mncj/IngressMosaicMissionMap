# Ingress Mosaic Mission Map
This project is a web map for visualizing the so called banner-/mosaic-missions within the game Ingress. 
A live version of this code containing mosaics in Munich, Germany can be found at http://ingress.tallistroan.de

## How to create your own map
Feel free to fork this project to create a web map for your city/region.

For the background map you need an API-Token from www.mapbox.com
Their free plan should be sufficient for a mission map with a normal number of views. 
Copy your API-Key into the [index.html](index.html) file.

You also need the [exportMosaics script](exportScript/exportMosaics.js) to get the mosaic data.
Because it is no IITC-plugin but instead a simple script you have to paste into the developer console 
of Google Chrome, knowledge in using the console can be benefical.

Before you can export your first mosaic you need to delete the already cached missions.
Load your IITC, open the developer console, paste the script into the Console tab and press Enter to run it.
Close the small windows with the provided buttons. The tab with the IITC should reload when everything is done.
Hint: You can also delete them manually under Resources > Local Storage > plugins-missions-missioncache

When the preparation is done execute the following steps:
* Select all missions that belong to the mosaic in ascending order (starting with number 1, 2, ...)
* Customize the first five variables in the script as desribed in the comments above them
* Run the script in the developer console
* Copy the data from the new window 'KML File' into a new file with the displayed filename.
  Copy this .kml file to the folder [kml](/kml). This file can be downloaded by the user for further use in external programs.
* Copy the data from the new window 'GeoJSON Output' into a .js file.
  You can also copy all mosaics into one file called [missions.js](missions.js)
  If you use multiple files don't forget to link them in [index.html](index.html) and [overview.html](overview.html)
* Copy an image (e.g. a screenshot from the scanner app) from the mosaic called 'mosaicName.jpg' into the [pics folder](pics). Use the same mosaicName you used in the export script.
* Close the windows by using 'Delete Mission Cache and Reload Intel Map'. The Intel tab should reload then.
  **Attention:** When you clicked the button, the locally cached mission will be deleted, so make sure you copied and saved the exports before that, otherwise you need to select all single missions again.

#### Adjustments in [index.html](index.html)

For all upcoming changes you must use the same values for mosaicID, mosaicName and mosaicTitle which you already used in the export script

* Include the new mosaic in the menu-list as follows:
``` html
<li><a href="index.html?id=mosaicID">mosaicTitle</a></li>
```
* Include an entry into the function showStartPortals() as follows:
``` javascript
markers.addLayer( L.geoJson(mosaicName_startMissions.features[0], {
                  pointToLayer: startMissionStyle,
                  onEachFeature: onEachPointOverview
                }));
```
* Within the switch-statement create a new case:
``` javascript
case "mosaicID":
  addLayer(mosaicName_mission, mosaicName_startMissions);
  break;
```            

#### Adjustments in [overview.html](overview.html)

* Include the new mosaic in the menu-list as follows:
``` html
<li><a href="#id=mosaicID">mosaicTitle</a></li>
```
* Include the mosaic at the begin of the script part as follows:
``` javascript
addMissions(mosaicName_startMissions.features[0], mosaicName_mission, 'a');
```
#### Finished

Now everything is finished and you new mosaic should be visible at the map.

In case of errors/mosaics not showing at the map, the error messages in the developer console can be of great help to locate the problem. 
## Special cases

When two or more missions start at the same portal you can avoid 
overlapping of the number labels by manually adjusting the properties 'missionNumber'
in the exported geojson-variable mosaicName_startMissions. 
In case you need to change the value for the first mission
you also need to adjust the cases in the function startMissionStyle in [index.html](index.html). Example:
``` javascript
function startMissionStyle(feature, latlng) {
  switch (feature.properties.missionNumber) {
    case "1, 6": // special pattern when mission 1 and 6 starting at the same portal
    case 1: // default value
    [...]
```
