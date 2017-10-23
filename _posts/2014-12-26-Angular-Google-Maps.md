---
layout: post
title: Google Maps in AngularJS
---

## How do I display a nice map?

[Gist of Code](ttps://gist.github.com/fuhton/8869022)
[Angular directives docs](https://developers.google.com/maps/documentation/javascript/controls)

### Directives in the HTML

At a high level ( and this is heavily paraphrased ), directives are markers on the DOM that the Angular compiler recongnizes and manipulates. The code to generate a Google Map is below.
```
<div ng-app="googleMap">
    <map class="angular_map_canvas" id="map_canvas">
    </map>
</div>
```
The HTML is pretty straight forward for Angular. You declare your app and then you create the customized directive, which in this case is the “map” element.

### Directives in the JS
```
angular.module("googleMap", []).directive("map", function() {
    return {
        restrict: "E",
        replace: true,
        template: "<div></div>",
        link: function(scope, element, attrs) {
            var geocoder = new google.maps.Geocoder();
            address = address;
            geocoder.geocode( { "address": address}, function(results, status) {
                if (status === google.maps.GeocoderStatus.OK) {
                   loctLat = results[0].geometry.location.d;
                   loctLong = results[0].geometry.location.e;
                   loct = new google.maps.LatLng(loctLat, loctLong);
                   map = new google.maps.Map(document.getElementById(attrs.id), mapOptions);
                }
           });
        }
    }
});
```
This is the primary function which creates the map based on the information we provide it. The only confusing one might be the address and geo-coder. The address is any address you might want – like 1600 Pennsylvania Ave NW, Washington, DC 20500 for example. The geocoder will render this into a string of data from which we get the latitude and longitude for our map. We can pass in many options for our map ( notice the mapOptions ) at the end of the map variable.

### Map Option

Below are some basic map options that define parts of our map. Noticeably the center, the zoom, and the type of map we’re displaying. The Google Map’s Javascript Controls have a complete list of options.
```
mapOptions = {
   center: loct,
   zoom: 12,
   styles: pioStyles,
   mapTypeId: google.maps.MapTypeId.ROADMAP
};
```
### POI STYLES
These styles define how the map is rendered and what is displayed. The [Google Map’s Javascript Styling](https://developers.google.com/maps/documentation/javascript/styling) list’s some more features and what not. THe few listed here turn off most markers and keep the map mostly flat.

pioStyles =[{
    featureType: "poi",
    elementType: "labels",
    stylers: [{
        visibility: "off"
    }]
}];
And that’s that! Pretty easy to get all of it up and running. Part 2 will cover how the map is handled with resizing and markers. If you have any questions just send me them via twitter @fuhton.

