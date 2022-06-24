# Horizon Graph
{
  "$schema": "https://vega.github.io/schema/vega/v5.json", //json引用路徑//
  "description": "A horizon graph, which preserves resolution by layering slices of an area chart.", //描述//
  "width": 500, //圖表寬度//
  "height": 100, //圖表高度//

  "signals": [
    {
      "name": "layers",       
      "value": 2,             //下拉選單預設值//
      "on": [{"events": "mousedown!", "update": "1 + (layers % 4)"}],    //滑鼠點擊效果,點擊前往下一層//
      "bind": {"input": "select", "options": [1, 2, 3, 4]}    //設定下拉選單項數及內容//
    },
    {
      "name": "height",
      "update": "floor(200 / layers)"       //圖表高度,隨著資料更動//
    },
    {
      "name": "vheight",
      "update": "height * layers"           //圖表資料高度//
    },
    {
      "name": "opacity",
      "update": "pow(layers, -2/3)"         //圖表資料疊加呈現//
    }
  ],

  "data": [    //資料參數//
    {
      "name": "layer_indices",  //設定table為該筆資料的layer_indices//
      "values": [0, 1, 2, 3], //以陣列表示，相當於[ {"data": 5}, {"data": 3}, {"data": 8}, {"data": 1} ]//
      "transform": [   
        {"type": "filter", "expr": "datum.data < layers"}, //針對資料做篩選//
        {"type": "formula", "expr": "datum.data * -height", "as": "offset"} //處理數據轉換，formula- 使用公式表達式擴展具有派生字段的數據對象//
      ]
    },
    {
      "name": "table",  //設定table為該筆資料的name//
      "values": [     ////table中的資料，以x和y設定量//
        {"x": 1,  "y": 28}, {"x": 2,  "y": 55},
        {"x": 3,  "y": 43}, {"x": 4,  "y": 91},
        {"x": 5,  "y": 81}, {"x": 6,  "y": 53},
        {"x": 7,  "y": 19}, {"x": 8,  "y": 87},
        {"x": 9,  "y": 52}, {"x": 10, "y": 48},
        {"x": 11, "y": 24}, {"x": 12, "y": 49},
        {"x": 13, "y": 87}, {"x": 14, "y": 66},
        {"x": 15, "y": 17}, {"x": 16, "y": 27},
        {"x": 17, "y": 68}, {"x": 18, "y": 16},
        {"x": 19, "y": 49}, {"x": 20, "y": 15}
      ]
    }
  ],

  "scales": [
    {
      "name": "x",  //x軸名稱//
      "type": "linear", //設定類型為線性圖表//
      "range": "width",
      "zero": false, "round": true,
      "domain": {"data": "table", "field": "x"}  //定義資料中的table來源，取x軸//
    },
    {
      "name": "y",
      "type": "linear",
      "range": [{"signal":"vheight"}, 0],
      "nice": true, "zero": true,
      "domain": {"data": "table", "field": "y"}   //定義資料中的table來源，取y軸//
    }
  ],

  "axes": [
    {"orient": "bottom", "scale": "x", "tickCount": 20} //x軸位置，tickcount為軸上項數//
  ],

  "marks": [  //文本標記//
    { 
      "type": "group", //圖表類型，group使用集群////
      "encode": { 
        "update": { //數據或顯示屬性更新，就會調用該集合//
          "width": {"field": {"group": "width"}},
          "height": {"field": {"group": "height"}},
          "clip": {"value": true}
        }
      },
      "marks": [
        {
          "type": "group",    //圖表類型，group使用集群//
          "from": {"data": "layer_indices"},    //標記資料來源//
          "encode": {
            "update": {
              "y": {"field": "offset"}
            }
          },
          "marks": [    //文本標記//
            {
              "type": "area",
              "from": {"data": "table"},
              "encode": {
                "enter": {  //首次顯示時調用該集合，相當於預設//
                  "interpolate": {"value": "monotone"},
                  "x": {"scale": "x", "field": "x"},
                  "fill": {"value": "steelblue"}
                },
                "update": {
                  "y": {"scale": "y", "field": "y"},
                  "y2": {"scale": "y", "value": 0},
                  "fillOpacity": {"signal": "opacity"}
                }
              }
            }
          ]
        }
      ]
    }
  ]
}

