# Stacked Area Chart
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",//json引用路徑//
  "description": "A basic stacked area chart example.",//描述//
  "width": 500,//圖表寬度//
  "height": 200,//圖表高度//
  "padding": 5,//視窗內距//

  "data": [    //資料參數//
    { 
      "name": "table",      //設定table為該筆資料的name//
      "values": [       //table中的資料，以x和y設定量，以c設定集群//
        {"x": 0, "y": 28, "c":0}, {"x": 0, "y": 55, "c":1},
        {"x": 1, "y": 43, "c":0}, {"x": 1, "y": 91, "c":1},
        {"x": 2, "y": 81, "c":0}, {"x": 2, "y": 53, "c":1},
        {"x": 3, "y": 19, "c":0}, {"x": 3, "y": 87, "c":1},
        {"x": 4, "y": 52, "c":0}, {"x": 4, "y": 48, "c":1},
        {"x": 5, "y": 24, "c":0}, {"x": 5, "y": 49, "c":1},
        {"x": 6, "y": 87, "c":0}, {"x": 6, "y": 66, "c":1},
        {"x": 7, "y": 17, "c":0}, {"x": 7, "y": 27, "c":1},
        {"x": 8, "y": 68, "c":0}, {"x": 8, "y": 16, "c":1},
        {"x": 9, "y": 49, "c":0}, {"x": 9, "y": 15, "c":1}
      ],
      "transform": [
        {
          "type": "stack",
          "groupby": ["x"],
          "sort": {"field": "c"},
          "field": "y"
        }
      ]
    }
  ],

  "scales": [
    {
      "name": "x", //x軸名稱//
      "type": "point", //設定類型為折線圖或散佈圖//
      "range": "width", 
      "domain": {"data": "table", "field": "x"} //定義資料中的table來源，取x軸//
    },
    {
      "name": "y", //y軸名稱//
      "type": "linear", //設定類型為線性圖表//
      "range": "height",
      "nine": true, "zero": true, //數值是否為0//
      "domain": {"data": "table", "field": "y1"} //定義資料中的table來源，取y1軸//
    },
    {
      "name": "color", //設定名稱color//
      "type": "ordinal", //以顏色區分兩組資料集//
      "range": "category", //資料集的範圍//
      "domain": {"data": "table", "field": "c"}  //定義資料中的table來源，取c//
    }
  ],

  "axes": [ 
    {"orient": "bottom", "scale": "x", "zindex": 1},//x軸設定,orient位置,scale取值//
    {"orient": "left", "scale": "y", "zindex": 1}//y軸設定,orient位置,scale取值//
  ],

  "marks": [
    {
      "type": "group",//圖表類型，group使用集群//
      "from": { //標記資料來源//
        "facet": { //區分各分組標記//
          "name": "series",
          "data": "table",
          "groupby": "c" //以c做基準//
        }
      },
      "marks": [ //[文本標記//
        {
          "type": "area",
          "from": {"data": "series"},
          "encode": {
            "enter": {
              "interpolate": {"value": "monotone"},
              "x": {"scale": "x", "field": "x"},
              "y": {"scale": "y", "field": "y0"},
              "y2": {"scale": "y", "field": "y1"},
              "fill": {"scale": "color", "field": "x"}
            },
            "update": {        //套用數據以及資料更新，調用更新集//
              "fillOpacity": {"value": 1}
            },
            "hover": {  //游標選取時使用函數//
              "fillOpacity": {"value": 0.5} //游標選取時的變淺效果//
            }
          }
        }
      ]
    }
  ]
}

