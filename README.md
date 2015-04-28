Ideas to the web application **wo**dore are collected in this repository.

# Introduction
The idea of the **wo**dore web application is to have a tool which helps you to plan a tour and share it with friends or even work together on the same tour. The idea of the appliction is to have three levels:
1. **Points** Add points on a map.
2. **Route** Cose points to create a route. It is also possible to draw.
3. **Route Group** Build a tour group, for this you can choose multiple routes.

Details are given below. An important aspect of this application is to be able to work together on a tour.
The goal is not to have an website with many public tours (although this might be possible as well) like www.hikr.org or www.skitouren.ch and so on.

Next are the three basic levels of the application explained with all the tasks for each level. The application is still under development and not much is done yet but here is a short gif how it could look like in the end.

**TODO** Add web app gif


# Application Basics

## Points
At this level you have a map and you can place points, which is basiclly a location on the map. This could be for example a bus station, a mountain hut, or a lake. It is possible for every point to add tags, which are like categories. Two different tags are possible, private and public: A public tag can be used by everybody (eg. a tag *Bus Station*). A public tag does not mean your point is public. A private tag is, as the name suggests, not public. This are more personal tags, like *Easter 2014 Tour*. Every added point can be shared with other users either readable or writable.

A filter can be used to filter points by tags, often used tags come first.

### Screenshots

**TODO** Screenshots

### Tasks *(not yet defined)*
- [ ] Define back end
- [ ] Define front end
- *more*

## Routes
A tour consists of points and drawings on the map. The points are selected on the map and can be re-ordered. For your tour you might for example include first a *bus stop* the a *break point* and finally the *mountain hut* were your tour ends (for the first day). To show were you should walk/drive you can highlight the route. 

### Screenshots

**TODO** Screenshots

### Tasks *(not yet defined)*
- [ ] Define back end
- [ ] Define front end
- *more*

## Route Groups
Finally you can group different routes. In the example before we had only one day, we might create a second route for the second day and include both routes in a route group. It should also be possible to include even route groups into route groups. This way you can build larger groups for example for a one week trip with a few two days route groups.

### Screenshots

**TODO** Screenshots

### Tasks *(not yet defined)*
- [ ] Define back end
- [ ] Define front end
- *more*

# Technical Details

## Back-end
The back-end is build on top of gae-init/gae-init which is initialization project for Google App Engine based on Python, Flask, RESTful, Bootstrap and tons of other cool stuff. The user management is also done by gae-init.

## Front-end
The final app should be a single page web application which works on mobiles, tables and computers. A key feature is to have a map with different tiles. Leaflet/Leaflet for the maps. Most tiles so far are based on the open street map ([OSM](https://www.openstreetmap.org), [Thunderforest](http://www.thunderforest.com/), [Refuges](http://maps.refuges.info/), [Stamen](http://maps.stamen.com/)), except for the swisstopo map (www.map.geo.admin.ch). Other good (hiking) tiles suggestions are welcome.

