# google-maps-dynamic-markers
**Pre-requisites:** 
 
 You'll to add 'gmaps4rails' gem.  [here](https://github.com/apneadiving/Google-Maps-for-Rails).
 
 Then inside map handler add following code to initialize rich marker

 ```
 //initialize Rick marker

var RichMarkerBuilder,
    extend = function(child, parent) { for (var key in parent) { if (hasProp.call(parent, key)) child[key] = parent[key]; } function ctor() { this.constructor = child; } ctor.prototype = parent.prototype; child.prototype = new ctor(); child.__super__ = parent.prototype; return child; },
    hasProp = {}.hasOwnProperty;

RichMarkerBuilder = (function(superClass) {
    extend(RichMarkerBuilder, superClass);

    function RichMarkerBuilder() {
        return RichMarkerBuilder.__super__.constructor.apply(this, arguments);
    }

    RichMarkerBuilder.prototype.create_marker = function() {
        var options;
        options = _.extend(this.marker_options(), this.rich_marker_options());
        return this.serviceObject = new RichMarker(options);
    };
    //create rich marker options
    RichMarkerBuilder.prototype.rich_marker_options = function() {
        var marker;
        marker = document.createElement("div"); // Create DOM element
        marker.setAttribute('class', 'marker_container'); // set class attribute
        marker.innerHTML = this.args.custom_marker; // cutom marker html code contained in string 
        // return customized marker 
        return {
            content: marker
        };
    };

    return RichMarkerBuilder;

})(Gmaps.Google.Builders.Marker);

this.buildMap = function(markers) {
    var handler;
    /initialize map
    handler = Gmaps.build('Google', {
        builders: {
            Marker: RichMarkerBuilder
        }
    });
    return handler.buildMap({
        provider: {},
        //div id where Map is loaded
        internal: {
            id: 'map'
        }
    }, function() {
        markers = markers.map(function(m) {
            marker = handler.addMarker(m); // add individual marker to map
            marker.serviceObject.set('id', m.id); ***// You can add other attributes using set ***
            return marker;
        });
        handler.bounds.extendWith(markers);
        // you can add javascript here to custmize imteractio with marker 
        //following code is just sample
        _.each(markers, function(marker, j) {
            return google.maps.event.addListener(marker.serviceObject, 'click', function(object) {
                marker_active = $.grep(markers , function (e) {return e.serviceObject.content.toString().indexOf('orange') >= 0 })
                id = marker.serviceObject.id

                if(marker_active != "") {$(".orange").removeClass("orange")}
                marker_text = marker.serviceObject.title
                marker.serviceObject.set('content', "<div class='marker_container'><p class='orange'>"+marker_text+"</p></div>")
        });

        handler.fitMapToBounds(); 
        handler.getMap().setZoom(12); // set map zoom here
        // center map to specific location
        handler.map.centerOn({
            lat: 41.8781,
            lng: -87.6298
        });
        return
    });
};

//Call buildMap function to by passing in marker json  
buildMap(#{raw @hash.to_json})

``` 