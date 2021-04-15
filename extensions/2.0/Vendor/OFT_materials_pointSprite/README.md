# OFT_materials_pointSprite

## Contributors

* Wenjie Liu, OppenFuture Technologies, liuwenjie@oppentech.com

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

这个扩展定义了用于展示sprites的material. 可用于展示宝石的glitter效果等。

## Extending Materials

The common sprite material is defined by adding the OFT_materials_pointSprite extension to any glTF material.

See [`pointSize`](#pointSize) and [`pointTexture`](#pointTexture) for example of usage.

## `pointSize`
参数`pointSize`用于设置shader中`gl_PointSize`的值，来控制显示到屏幕空间的point的大小。
这里为了实现glitter的效果, 参考代码如下
```cpp
vRandom = rand(position.xy);
float dotNV = dot(viewDir, n);
// cos func to liner func
vIntensity = 1.0 - acos(dotNV);
// map Threshold ~ 1.0 to 0 ~ 1.0
vIntensity = vIntensity < Threshold ? 0.0 : (vIntensity - Threshold) / (1.0 - Threshold);
// sharp the curve
vIntensity = pow(vIntensity, 1.1);
gl_PointSize = ((vRandom * 0.2 + 1.8) * pointSize) * vIntensity * (0.5/-mvPosition.z);
```
> `vRandom`是一个0-1的随机值，用于随机point的大

> `vIntensity`代表强度，是用相机视线和normal点乘的结果来表达

> 除`-mvPosition.z`是为了实现point的透视效果（近大远小）

## `pointTexture`
This parameter is a [`textureInfo`](/specification/2.0/README.md#reference-textureInfo) object.
用于`gl_PointCoord`的采样，参考代码如下：
```cpp
vec2 rotateUv = rotateUV(gl_PointCoord, vRandom * vIntensity * PI * 0.6);
gl_FragColor = gl_FragColor * texture2D( pointTexture, rotateUv );
```
其中`rotateUv()`是一个旋转uv的函数，用于表现point随强度旋转的效果，实现方式如下：
```cpp
vec2 rotateUV(vec2 uv, float rotation) {
    float mid = 0.5;
    return vec2(
        cos(rotation) * (uv.x - mid) + sin(rotation) * (uv.y - mid) + mid,
        cos(rotation) * (uv.y - mid) - sin(rotation) * (uv.x - mid) + mid
    );
}
```


### Example

```json
{
    "materials": [
        {
            "name": "sprites",
            "extensions": {
                "OFT_materials_pointSprite": {
                    "pointSize": 50,
                    "pointTexture": {
                      "index": 4,
                      "texCoord": 1
                    }
                }
            }
        }
    ]
}
```

### PointSprite

The following parameters are contributed by the `OFT_materials_pointSprite` extension:
|                                  | Type                                                                            | Description                            | Required             |
|----------------------------------|---------------------------------------------------------------------------------|----------------------------------------|----------------------|
|**pointSize**                     | `integer` [1-200]                                                               | The point size.                        | Yes, default: `50`   |
|**pointTexture**                  | [`textureInfo`](/specification/2.0/README.md#reference-textureInfo)             | The point texture.                     | Yes                  |




