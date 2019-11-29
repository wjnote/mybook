# GeoJSON
GeoJSON 是一种对各种地理数据结构的进行编码的格式，GeoJSON对象可以表示几何，特征或者特征集合，GeoJSON支持下面几何类型： 点， 面，线， 多点， 多线， 多面和几何图形

GeoJSON 里得特征包含一个几何对象和其他属性，特征集合表示一系列特征

一个完整的GeoJSON数据结构总是一个对象
```js
{ "type": "FeatureCollection",
  "features": [
    { "type": "Feature",
      "geometry": {"type": "Point", "coordinates": [102.0, 0.5]},
      "properties": {"prop0": "value0"}
      },
    { "type": "Feature",
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [102.0, 0.0], [103.0, 1.0], [104.0, 0.0], [105.0, 1.0]
          ]
        },
      "properties": {
        "prop0": "value0",
        "prop1": 0.0
        }
      },
    { "type": "Feature",
       "geometry": {
         "type": "Polygon",
         "coordinates": [
           [ [100.0, 0.0], [101.0, 0.0], [101.0, 1.0],
             [100.0, 1.0], [100.0, 0.0] ]
           ]
       },
       "properties": {
         "prop0": "value0",
         "prop1": {"this": "that"}
         }
       }
     ]
   }

```
