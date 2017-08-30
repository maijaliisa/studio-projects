
<!DOCTYPE html>
<html>
<head>
	
	<title>Recent Supreme Court Civil Rights Cases</title>

	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	
<!-- 
THESE ARE CALLS TO DIFFERENT EXTERNAL FILES
LISA HAS A CSS STYLESHEET THAT YOU NEED
THE NEXT IS THE JAVASCRIPT LIBRARY FOR LEAFLET
AND THE FINAL ONE IS THE LIBRARY FOR JQUERY, WHICH I USE FOR THE PULLDOWN MENU.
 -->

    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.0.3/dist/leaflet.css" integrity="sha512-07I2e+7D8p6he1SIM+1twR5TIrhUQn9+I6yjqD53JQjFiMf8EtC93ty0/5vJTZGF8aAocvHYNEDJajGdNx1IsQ==" crossorigin=""/>
    <script src="https://unpkg.com/leaflet@1.0.3/dist/leaflet.js" integrity="sha512-A7vV8IFfih/D732iSSKi20u/ooOfj/AGehOKq0f4vLT1Zr2Y+RX7C+w8A1gaSasGtRUZpF/NZgzSAu4/Gc41Lg==" crossorigin=""></script>

	<script src="https://code.jquery.com/jquery-2.1.1.min.js"></script>
<!-- 
THESE ARE CSS STYLES WHAT YOU SHOULD FREELY PLAY WITH
THE STYLES THAT BEGIN WITH # RELATE TO IDS, THE ONES THAT BEGIN WITH . RELATE TO CLASSES
WHEN YOU GOT EVERYTHING WORKING YOU CAN PLAY WITH THESE AND ADD TO THESE 
TO MAKE YOUR FONTS AND LAYOUTS NICE AND LOVELY
 -->

	<style>
::-webkit-scrollbar {
    width: 0px;  /* remove scrollbar space */
    background: transparent;  /* optional: just make scrollbar invisible */
}
	#map {position: absolute; top: 10px; left: 10px; width: 850px; height: 500px; z-index:1; }
	#articlePlace {position: absolute; left: 860px; top: 8px; margin:15px; width: 400px; height: 500px; overflow: scroll;
    overflow-x: hidden; z-index:5;} 
    #select-menu{position: absolute; left: 80px; top: 30px;  z-index:10;} 
.info { padding: 8px 8px; font: 14px/16px , Helvetica, sans-serif; background: white; background: rgba(255,255,255,0.8); box-shadow: 0 0 15px rgba(0,0,0,0.2); border-radius: 5px; } .info h4 { margin: 0 0 5px; color: #777; }
.legend { text-align: left; line-height: 18px; color: #555; } .legend i { width: 18px; height: 18px; float: left; margin-right: 8px; opacity: 0.7; }</style>
</head>

<!-- THE BODY OF THE HTML BEGINS HERE -->

<body>
  
</div>
-->
<!-- 
THE TWO DIVS BELOW ARE SUPER IMPORTANT: THEY ARE WHERE THE MAP WILL GO, AND YOUR ARTICLE TEXT WILL GO
YOU WILL CERTAINLY WANT TO STYLE THESE, BOTH THEIR PLACEMENT AND CONTENT 
WHEN YOU'RE DONE WITH ALL THE FANCY FUNCTIONALITY
 -->

<div id='map'></div>
<div id='articlePlace'> <strong>Mapping Recent SCOTUS Civil Rights Cases</strong>
<p>Out of the sixty four Supreme Court cases in the 2016/2017 Supreme Court session, nine were about Civil Rights. Six of these nine cases ended with a unanimous ruling (8-0 or 9-0 based on when Gorsuch was brought on).

In the last two years the Supreme Court has heard 32 Civil Rights cases, ranging from voting rights issues to police brutality. Exploring where these cases occured provides an interesting look at the evolving space of Civil Rights law in the United States. 
 
<p>Since 1948, most Supreme Court cases fell into the following categories:
<li>Criminal Procedure      1949</li>
<li>Economic Activity       1676</li>
<li>Civil Rights            1402</li>
<li>Judicial Power          1186</li>
<li>First Amendment          667</li>

For this project, I wanted to isolate and explore just the Civil Rights cases in the last two years based on state of origin.

<strong>Most popular word used by Justices in these cases:</strong>
<ul><li>Chief Justice Roberts: counsel</li> 
<li> Justice Alito: police</li> 
<li> Justice Breyer: district
<li> Justice Ginsburg: employee
<li> Justice Gorsuch: discrimination
<li> Justice Kagan: question
<li> Justice Kennedy: warrant
<li> Justice Sotomayor: officer

<!-- 
THIS IS WHERE THE BROWSER LOADS IN YOUR GEO JASON INFORMATION
MAKING IN A JAVASCRIPT FILE, RATHER THAN A GEO JASON FILE
ALLOWS YOU TO LOAD IT LOCALLY WITHOUT DEALING WITH SETTING UP SERVERS ON YOUR MACHINE.
 -->

<script src="https://unpkg.com/leaflet@1.1.0/dist/leaflet.js" > </script>
<script type="text/javascript" src="points-json.js"> </script>


<!-- 
HERE BEGINS ALL THE SCRIPT THAT SETS UP THE MAP 
ALL THE COMMENTS FROM HERE WE'LL BE IN JAVASCRIPT COMMENTS //
 -->

<script type="text/javascript">

// THIS INITIALIZES THE FIRST VARIABLE CALLED MAP
// THERE IS NO SET VIEW HERE, BECAUSE WE ARE USING FITBOUNDS()
// TO AUTOMATICALLY CENTER AND ZOOM ON OUR POINTS

	var map = L.map('map')

// THIS SETS THE BASE LAYER OF THE MAP -- USING BACKGROUND TILES

	L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
		maxZoom: 4,
		attribution: 'Map data &copy; <a href="http://openstreetmap.org">OpenStreetMap</a> contributors, ' +
			'<a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
			'Imagery © <a href="http://mapbox.com">Mapbox</a>',
		id: 'mapbox.outdoors'
	}).addTo(map);


// THESE LINES ADD THE LITTLE INFO WINDOW IN THE UPPER RIGHT CORNER 
// (YOU CAN CHANGE WHERE GOES BY EDITING THE STYLES FOR #INFO)
// NOTE THAT "L.CONTIROL()" -- MEANS THAT THIS IS A LEAFLET METHOD
// FOR CREATING A CONTROL WINDOW.

	var info = L.control();

	info.onAdd = function (map) {
		this._div = L.DomUtil.create('div', 'info');
		this.update();
		return this._div;
	};

	info.update = function (props) {
		this._div.innerHTML = '<h4>Civil Rights Court Case</h4>' +  (props ?
			props['Case Name_y']
			: 'Explore each Civil Rights case by state');
	};

	info.addTo(map);


// HERE ARE YOUR STYLES FOR THE SHAPES--THERE'RE A LOT OF DIFFERENT CONTROLS
// EVERYTHING THAT IS SET AS A VALUE MEANS THAT THAT'S STYLE IS FOR EVERY SINGLE POINT
// THESE LINES:
// 			radius: feature.properties.radius,
// 			fillColor: feature.properties.color
// SETS THE COLOR AND RADIUS BASED ON WHAT YOU PUT IN YOUR GEOJSON DOC "color:" and "radius:"
// YOU COULD THEORETICALLY DO THAT FOR EVERY SINGLE ONE OF THE STYLES

	function style(feature) {
		return {
			weight: 0.2,
			opacity: 1,
			color: 'blue',
			fillOpacity: 0.2,
			radius: feature.properties.radius,
			fillColor: feature.properties.color
		};
	}
// THIS FUNCTION CONTROLS WHAT HAPPENS WHEN YOU HOVER OVER A SHAPE
// IT CHANGES SOME OF THE STYLES (HIGHLIGHTING THAT SHAPE)
// AND IT ALSO UPDATES THE TEXT INSIDE THE INFOBOX

	function highlightFeature(e) {
		var layer = e.target;

		layer.setStyle({
			weight: 5,
			color: '#666',
			fillOpacity: 1
		});

		if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
			layer.bringToFront();
		}

		info.update(layer.feature.properties);
	}

// THIS FUNCTION CHANGES THE ARTICLE TEXT ON THE RIGHT SIDE OF THE PAGE
// IT IS CALLED WHENEVER YOU CLICK ON A SHAPE

	function updateArticle(e) {
		var layer = e.target;
		var props = layer.feature.properties;
		var articleDIV = document.getElementById("articlePlace");
		articleDIV.innerHTML = '<br />' + '<strong>CASE NAME: </strong>' + props['Case Name_y'] + '<br />' + '<strong>MOST FREQUENT WORDS: </strong>' + props.WordsString + '<br />' + '<br />' + '<strong>FINAL VOTE: </strong>' + props.Decision + '<br />' + '<strong>COURT LEANING: </strong>' + props['Court Leaning'] + '<br />' + '<br />'  + '<strong>SUMMARY: </strong>' + props.Summary
	}

// THIS PART IS A LITTLE COMPLICATED
// IT IS CREATING A LAYER FOR YOUR MAP CALLED points
// IT IS SETTING ALL OF THE ACTIONS THAT ARE GOING TO HAPPEN WHEN YOU HOVER AND CLICK ON YOUR POINTS
// NOTE THE fitBounds() AT THE END -- THAT CENTERS AND ZOOMS THE MAP BASED ON YOUR POINTS



	function resetHighlight(e) {
		points.resetStyle(e.target);
		info.update();
	}

	function zoomToFeature(e) {
		map.fitBounds(e.target.getBounds());
	}

	function onEachFeature(feature, layer) {
		layer.on({
			mouseover: highlightFeature,
			mouseout: resetHighlight,
			click: updateArticle
		});
	}

	points = L.geoJson(pointsData, {
		style: style,
    pointToLayer: function(feature, latlng) {

        return new L.CircleMarker(latlng);

    },
    onEachFeature: onEachFeature
	}).addTo(map);
map.fitBounds(points.getBounds(), {padding: [120,120]});

</script>
<script>
// BELOW IS A JQUERY FUNCTION THAT THE TEXT CHANGES IN THE DROP-DOWN MENU
// AND EXECUTES CHANGES ON THE MAP BASED ON THE GROUP ID OF YOUR SHAPES

	$(document).ready(function () {
	    $('#select-menu').change(function () {
		var selectedGroup = $('#select-menu').val();
		
		points.eachLayer(function (layer) {
			if (selectedGroup == 0)
		    {
		    	map.addLayer(layer);
		    } else if (layer.feature.properties.group_id != selectedGroup) {
			// If the layer's id is different from the selected one, remove it from the map
			map.removeLayer(layer);
		    }
		    else {
			// Otherwise add it do the map
			map.addLayer(layer);
		    }
		});

	});
	});
</script>
