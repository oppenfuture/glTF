# OFT_texture_highPrecisionNormal

## Contributors

* Bei Chu, OppenFuture Technologies, chubei@oppentech.com

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

## Overview

This extension allows the usage of normal texture with precision higher than 8 bit.

This extension can only be added in [`normalTextureInfo`](/specification/2.0/README.md#reference-normaltextureinfo).

See [`lower8BitTexture`](#lower8bittexture) for example of usage.

### JSON Schema

TODO: Links to the JSON schema for the new extension properties.

## `lower8BitTexture`

This parameter is a [`textureInfo`](/specification/2.0/README.md#reference-textureInfo) object. It contains RGB components in linear space. When present, it should be combined with [`normalTexture`](/specification/2.0/README.md#materialnormaltexture) to act as a 16 bit tangent space normal map. The `normalTexture`'s texel represents the higher 8 bits of the XYZ components, while the texel of `lower8BitTexture` represents the lower 8 bits of the XYZ components. The resulting red [0 to 65535] maps to X [-1 to 1], green [0 to 65535] to Y [-1 to 1], blue [32768 to 65535] to Z [1/65535 to 1].

This type of extension is specifically designed for platforms that don't support 16 bit textures. Implementations can pass `normalTexture` and `lower8BitTexture` separately to shader and combine them using shader code. For example with GLSL:

```cpp
vec3 normalVector = tex2D(normalTexture, texCoord) * 2.0 - 1.0;
vec3 lower8bitNormalVector = tex2D(lower8BitNormalTexture, texCoord) * 2.0 - 1.0;
normalVector = normalVector * (256.0 / 257.0) + lower8bitNormalVector / 257.0;
```

The `texCoord` property of `lower8BitTexture` must be the same as the `texCoord` of `normalTexture`. If `texCoord` is not present in `normalTexture`, it should not be present in `lower8BitTexture` either.

### Example

```json
{
  "normalTexture": {
    "scale": 2,
    "index": 3,
    "texCoord": 1,
    "extensions": {
      "OFT_texture_highPrecisionNormal": {
        "lower8BitTexture": {
          "index": 4,
          "texCoord": 1
        }
      }
    }
  }
}
```

## Known Implementations

* TODO: List of known implementations, with links to each if available.
