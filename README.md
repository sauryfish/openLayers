# openLayers
the experience of openLayers

author:sawatariyuki, Saury

Date:2018-11-23

<!Doctype html>
<html xmlns=http://www.w3.org/1999/xhtml>
<head>                  
  <meta http-equiv=Content-Type content="text/html;charset=utf-8">
  <meta http-equiv=X-UA-Compatible content="IE=edge,chrome=1">
  <meta content=always name=referrer>
  <title>OpenLayers 3地图示例</title>
  <link href="ol/v4.6.5/ol.css" rel="stylesheet" type="text/css" />
  <script type="text/javascript" src="ol/v4.6.5/ol.js" charset="utf-8"></script>
</head>

<body>
  <div id="map" style="width: 100%"></div>
  <script>
    var vectorLayer = crtLayerWMTS('vector', "EPSG:4326", 1);
    var slzjLayer = crtLayerWMTS('slzj', "EPSG:4326", 1);

    var xyzLayer = crtLayerXYZ('vector', "EPSG:4326", 1);

    function crtLayerXYZ(type, proj, opacity){
       var layer = new ol.layer.Tile({
            source: new ol.source.XYZ({
                url : 'http://218.2.231.245/mapjs2/rest/services/MapJS/js_slzj_2017/MapServer/tile/{z}/{y}/{x}',
                projection: proj
            }),
            opacity: opacity
        });
        layer.id = type;
        return layer;
    }

    // 创建地图
    var map = new ol.Map({
      layers: [
        vectorLayer,slzjLayer,xyzLayer
      ],
      view: new ol.View({
        // 设置南京为地图中心
        center: [118.771614,32.071614],
        projection: "EPSG:4326",
        zoom:10,
        minZoom:7,
        maxZoom:19
      }),
      target: 'map'
    });

    map.on('singleclick', event => {
      console.log(event.coordinate)
    })

    map.getView().on('change:resolution', function() {
              let z = map.getView().getZoom();
              if (z >= 18) {
                //map.removeLayer(markLayerJS)
                xyzLayer.setVisible(false);
      } else {
        xyzLayer.setVisible(true);
      }
    });


    function crtLayerWMTS(type, proj, opacity){
        var projection = ol.proj.get(proj);
        var projectionExtent = projection.getExtent();
        var size = ol.extent.getWidth(projectionExtent) / 256;
        var resolutions = new Array(19);
        var matrixIds = new Array(19);
        for (var z = 1; z < 19; ++z) {
            // generate resolutions and matrixIds arrays for this WMTS
            resolutions[z] = size / Math.pow(2, z);
            matrixIds[z] = z;
        }
 
        var layer = new ol.layer.Tile({
            opacity: opacity,
            source: new ol.source.WMTS({
              url: 'http://218.2.231.246/mapservice/wmts/' + type,
              layer: type,
              matrixSet: type,
              format: 'tiles',
              projection: projection,
              tileGrid: new ol.tilegrid.WMTS({
                origin: ol.extent.getTopLeft(projectionExtent),
                resolutions: resolutions,
                matrixIds: matrixIds
              }),
              style: 'default',
              wrapX: true
            })
          });
        return layer;
    }
  </script>
</body>
  
</html>
