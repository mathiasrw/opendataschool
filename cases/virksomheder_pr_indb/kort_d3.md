Kort med [d3](http://d3js.org/)
===

D3 er et javascript bibliotek, der primært anvendes til at lave visualiseringer til webbrowsere. D3 har god understøttelse for at producere kort. Nedenstående er et eksempel på det foregående kort, men udviklet med D3 og SVG elementer. Datasættet er det samme og hentes direkte fra CartoDBs SQL API.

Forsøg selv at lave en html-fil fra følgende kodeeksempel. Og indsæt et link til din egen cartodb-bruger.

Se Eksempel [her](/../../assets/d3.html) Prøv også at zoome med scroll hjulet.

```html

<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
    <title>Kort med d3</title>
    <script src="http://d3js.org/d3.v3.min.js"></script>
    <script src="http://d3js.org/d3.geo.projection.v0.min.js"></script>
    <script src="http://d3js.org/topojson.v0.min.js"></script>

    <style type="text/css">

    body{
        background:white;
    }
    svg {
      width: 960px;
      height: 500px;
      background: none;
    }
    svg:active {
      cursor: move;
      cursor: -moz-grabbing;
      cursor: -webkit-grabbing;
    }
    .globe {
      fill: black;
      fill-opacity: 1;
      stroke: #111;
      stroke-width:1px;
    }
    #denmark path {
      stroke: #333;
      stroke-linecap: round;
      stroke-linejoin: round;
    }
    </style>
  </head>
  <body>
    <h1>Virksomheder pr. person</h1>
    <script type="text/javascript">

    var denmark = 'denmark';


    var svg = d3.select("body")
            .append("svg")
            .call(d3.behavior.zoom()
                .on("zoom", redraw))
            .append("g");

            // Define the color scale for our choropleth
    var fill = d3.scale.linear()
            .domain([0.0841347229055777,0.0915873241438113, 0.0995412160259702, 0.105005357416195, 0.110218717549325, 0.118296046582899, 0.194428969359331])
            .range(["#FFFFB2","#FED976","#FEB24C", "#FD8D3C", "#FC4E2A", "#E31A1C", "#B10026"]);


    // Our projection.
    var xy = d3.geo.mercator()
              .scale(4500)
              .center([11,56.3]);

    var path = d3.geo.path()
                .projection(xy)

    svg.append("g").attr("id", "denmark");

    //sql.execute("SELECT ST_Simplify(the_geom,0.01) as the_geom, pop2005 as population FROM {{table_name}} WHERE the_geom IS NOT NULL", {table_name: denmark})

    d3.json("http://virkdata.cartodb.com/api/v2/sql?q=SELECT the_geom, virk_person FROM virk_pr_person WHERE the_geom IS NOT NULL&format=topojson&dp=5", function(collection) {
      //var subunits = topojson.object(collection);
      var tj = {kommuner: {type: "GeometryCollection", geometries: []}};
      for (i in collection.objects){
        tj.kommuner.geometries.push(collection.objects[i])
      }
      collection.objects = tj

      svg.select("#denmark")
        .selectAll("path")
        .data(topojson.object(collection, collection.objects.kommuner).geometries)
        .enter().append("path")
        .attr("fill", function(d) { return fill(d.properties.virk_person); })
        .attr("stroke-width", "0.1px")
        .attr("fill-opacity", "1")
        .attr("d", path.projection(xy));
            });

    function redraw() {
      svg.attr("transform", "translate(" + d3.event.translate + ")scale(" + d3.event.scale + ")");
    }

    </script>
  </body>
</html>

```

[Læs mere om d3](http://d3js.org/)