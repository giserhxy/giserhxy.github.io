<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="initial-scale=1,maximum-scale=1,user-scalable=no" />
    <title>Intro to MapView - Create a 2D map | Sample | ArcGIS Maps SDK for JavaScript 4.29</title>
    <style>
      html,
      body,
  #viewDiv {
    padding: 0;
    margin: 0;
    height: 100%;
    width: 100%;
   }

  header {
    background-image: url('https://img0.baidu.com/it/u=1454707025,273122326&fm=253&fmt=auto&app=138&f=PNG?w=667&h=500'); /* 页眉背景图片地址 */
    background-repeat: no-repeat;
    background-size: cover;
    color: black;
    padding: 0;
   }

  header h1 {
    margin: 0 10px;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
    font-family: '黑体', sans-serif; /* 修改字体为黑体 */
    text-align: left; /* 大标题左对齐 */
    float:left;
   }

  header h2 {
    margin-top: 3px; /* 小标题距离大标题3像素 */
    color: #999999; /* 设置小标题颜色 */
    text-align: left; /* 小标题也左对齐 */
    font-size:14px;
    float:left;
   }

  header a {                  /* 加入超链接 */
    color: #8B8682;
    text-decoration: none;
    font-weight: bold;
    font-size: 12px; /* 设置链接字体大小 */
    margin-right: 10px; /* 链接距离右边10像素 */
    position:relative;
    left:10px;
   }

  header a:hover {
    color: black; /* 鼠标悬停时链接文字颜色变黑 */
   }

      }
  </style>
    <link rel="stylesheet" href="https://js.arcgis.com/4.29/esri/themes/light/main.css" />
    <script src="https://js.arcgis.com/4.29/">
  </script>

    <script>
      require(["esri/layers/FeatureLayer",
               "esri/Map","esri/views/MapView",
               "esri/widgets/BasemapGallery",
               "esri/widgets/ScaleBar",
               "esri/widgets/Search",
               "esri/widgets/Home",
               "esri/widgets/LayerList",
               "esri/widgets/Legend",
               "esri/layers/GraphicsLayer",
               "esri/widgets/Sketch",
               "esri/widgets/AreaMeasurement2D",
               "esri/widgets/DistanceMeasurement2D"
               ], 
      (FeatureLayer,Map,MapView,BasemapGallery,ScaleBar,Search,Home,LayerList,Legend,GraphicsLayer,Sketch,AreaMeasurement2D, DistanceMeasurement2D) => {
        //创建要素图层
        const layer = new FeatureLayer({
        url:"https://www.geosceneonline.cn/server/rest/services/Hosted/南安交通公共事件查看图层/FeatureServer"      //输入地图URL
        });
        const map = new Map({
          basemap: "topo-vector"
        });
        map.add(layer);                           //要素图层加入地图
       
        const view = new MapView({
          container: "viewDiv",
          map: map,
          zoom: 14,                                //范围设置为14
          center: [118.385, 24.965]                  // 中心点坐标设为南安
        });
        
        
        //比例尺和微件位置
        const scaleBar = new ScaleBar({
          view: view,
          unit: "dual"
        });
        view.ui.add(scaleBar, {
          position: "bottom-left"
        });
        
        //搜索框和微件位置
        const searchWidget = new Search({
        view: view
        });
        view.ui.add(searchWidget, {
        position: "top-right",
        index: 2
        });
        
        //家微件
        let homeWidget = new Home({
        view: view
        });
        view.ui.add(homeWidget, "top-left");
        
        //图层列表和微件位置
        let layerList = new LayerList({
        view: view
        });
        view.ui.add(layerList, {
        position: "top-left"
        });
        
        //底图库部件和微件位置
        const basemapGallery = new BasemapGallery({
          view: view
        });
        view.ui.add(basemapGallery, {
          position: "top-left"
        });
        
        //图例和微件位置
        let legend = new Legend({
        view: view
        });
        view.ui.add(legend, "bottom-right");
        
        //加入绘制功能
        const graphicsLayer = new GraphicsLayer();
        view.when(() => {
        const sketch = new Sketch({
        layer: graphicsLayer,
        view: view,
        creationMode: "update"
        });
        view.ui.add(sketch, "top-right");
      });
 view.ui.add("topbar", "top-right");

// create the measurement widgets and hide them by default
const distanceMeasurement2D = new DistanceMeasurement2D({
  view,
  visible: false
});
const areaMeasurement2D = new AreaMeasurement2D({
  view,
  visible: false
});

// event listener for distance measurements
document.getElementById("distanceButton").addEventListener("click", function() {
  setActiveWidget(null);
  if (!this.classList.contains("active")) {
    setActiveWidget("distance");
  } else {
    setActiveButton(null);
  }
});

// event listener for area measurements
document.getElementById("areaButton").addEventListener("click", function() {
  setActiveWidget(null);
  if (!this.classList.contains("active")) {
    setActiveWidget("area");
  } else {
    setActiveButton(null);
  }
});

function setActiveWidget(type) {
  switch (type) {
    case "distance":
      areaMeasurement2D.visible = false;
      distanceMeasurement2D.visible = true;
      distanceMeasurement2D.viewModel.start();
      setActiveButton(document.getElementById("distanceButton"));
      break;
    case "area":
      distanceMeasurement2D.visible = false;
      areaMeasurement2D.visible = true;
      areaMeasurement2D.viewModel.start();
      setActiveButton(document.getElementById("areaButton"));
      break;
    case null:
      areaMeasurement2D.visible = false;
      distanceMeasurement2D.visible = false;
      break;
  }
}

function setActiveButton(selectedButton) {
  // focus the view to activate keyboard shortcuts for sketching
  view.focus();
  let elements = document.getElementsByClassName("active");
  for (let i = 0; i < elements.length; i++) {
    elements[i].classList.remove("active");
  }
  if (selectedButton) {
    selectedButton.classList.add("active");
  }
}
});
      </script>
  </head>
  <body>
    <header>
        <h1>泉州市南安公共事件</h1>
        <h2>这是关于南安公共事件的编辑与查看地图</h2><br/>
        <a href="https://www.askci.com/news/finance/20170120/16165388653.shtml" target="_blank">点这里进入web应用</a> 
    </header>
    <div id="viewDiv"></div>
    <div id="topbar">
        <button class="action-button esri-icon-measure-area" id="areaButton" type="button" title="Measure area"></button>
        <button class="action-button esri-icon-measure-line" id="distanceButton" type="button" title="Measure distance between two or more points"></button>
      </div>
  </body>
</html>
