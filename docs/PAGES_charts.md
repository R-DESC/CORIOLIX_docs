![](img/banner_cruise_chart.png)
# Cruise Charts Page
The cruise charts page ("Cruise Charts" from the CORIOLIX navigation menu) is a web mapping interface for CORIOLIX.  It presents a dynamic map view of the vessel, its realtime data, and supporting geospatial information such as nearby research infrastructure, routes, and waypoints.  The cruise chart page also has built-in integrations with the Cruise Event Log and Route Planning tools.
## Basic Map controls:
Basic map controls include the "out-of-the-box" and ubiquitous *Zoom*, *Pan*, and *Mouse Coordinates* leaflet controls for navigation.
#### Zoom
Located in the upper lefthand corner of the map pane.  Click + to zoom in, click - to zoom out.  
![](img/chart_zoomscale.png)
#### Pan
Left click, hold and drag on the map to pan (note: no graphic of this operation).
#### Mouse Coordinates
Located in the lower righthand corner of the map pane.  Provides coordinate readout (decimal degrees) for mouse pointer location.  
![](img/chart_mousecoords.png)

## Menu Bar controls:
Menu Bar controls are custom controls to the CORIOLIX charting interface.  You may expand any of the controls by selecting its icon from the thin Menu Bar ribbon along the righthand side of the map pane.
#### Layer Control
Located in at the top of the Menu Bar, the Layer Control provides a interface for toggling layer visibility on the map.
![](img/chart_layer_control.png)

##### Background Layers:
Background layers are externally sourced, locally cached, map services that provide geographic 'background' context to the charting interface.  One and only one background layer is always active, but we've selected three background layers to choose from.  You may add additional layers by request or by editing the map configuration file.

* Open Streetmap
![](img/chart_layer_openstrtmap.png)

* Global Multi-Resolution Topography Synthesis (https://www.gmrt.org/)
![](img/chart_layer_gmrt.png)

* NOAA Electronic Nautical Charts (NOAA ENCs)
![](img/chart_layer_enc.png)

##### Raster Overlays
Raster overlays are similar to background layers.  They are also externally sourced, locally cached, map services that provide local context to the charting interface.  However, raster layers are off (inactive) by default.  When activated (selected) they plot on top of the background layers.  We've provided two default raster layers.  Currently the latest image is provided by default and there is no user control for vieweing past (out of date) imagery.

* 8-Day Composite Global SST 
* 8-Day Composite Global Chl-a conentration

##### Ship Track Data
This grouping presents the ship navigation in trackpoint (navigation fixes) or trackline (navigation path) format.  Note that trackpoints are clickable and will open a pop-up with Heading, COG, and SOG data relevant to the trackpoint of interest.

##### Underway Science Data
This grouping includes trackpoint overlays organized into two instrument class groups, Flowthrough and Met. & Atmospheric.  All points are clickable - to show relevant sensor data observations at that point, and may be colorized by unique instrument parameter via the settings tools.

#### Custom Routing
Located just below the Layer Control in the Menu Bar, the Custom Routing controls provide a user interface for creating new routes (routes are defined here as simple line or polyline shapes stored as geoJSON in the CORIOLIX database).  
![](img/chart_routing.png)
Users may draw a route on the map, calculate route distance, and also the time required to complete route under various transit speeds.
![](img/chart_routing_ex.png)

#### Events
All cruise events are available for visualization in the charting interface.  The Events control, immediately below the Custom Routing control, provides the user a mechanism to toggle event visibility.  Place a check next to any event that you would like to make visible in the map view.
![](img/chart_events_ex.png)

#### Re-Center Map
Click the "crosshairs" icon at the bottom of the menu bar to reposition the map view to have the vessel in the center of the view.  
![](img/chart_crosshairs.png)

#### Settings
Use the chart settings sidebar to modify the default behavior or setup of the map.  Chart settings will be saved to your browser cache and will persist across sessions.  Your settings will be lost if/when you clear your cache.

The first group of settings control how the Underway Science Data trackpoints will be rendered.  Use the dropdown menus to select an instrument and color scheme.  When available, we use the local range for the parameter of interest to set the color bounds.  If the local range is not available, we use the global range.  If no global range is available then the user will need to manually set the color minimum and maximum.

We've made the display of the ship, the true winds wind barbs, and the dead reckoning overlays optional for users.  You may use the setting page to toggle overlay icon visibility for these items as well as set a default cruise track display interval (default = 600 minutes or 10 hours).

![](img/chart_settings.png)
