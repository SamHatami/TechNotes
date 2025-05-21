---
title: "Shader Of The Week - 1"
description: Venture into GLSL shaders
date: 2025-05-21
categories:
  - Shaders
math: true
comments: false
---

The goal is to create a shader each week, doesn't matter how simple or complex, as long as I keep going on. No AI, just documentation and tutorials. 

This one is just a simple horisontal movement. Yay!

## What it does

- `gl_FragCoord.xy/u_resolution.xy` normalizes the pixel coordinates into the range [0.0-1.0]
- `sin(st.x*PI-u_time)` creates a wave-pattern that depends on the horizontal coordinate and time (negative time means it'll move left to right). PI adjusts the wave to move one cycle across the view. 

```GLSL
precision mediump float;

uniform vec2 u_resolution;
uniform float u_time;

float PI = 3.14159;

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(sin(st.x*PI-u_time));
    gl_FragColor = vec4(color, 1.0);
}
```


{{<glslCanvas height="400px">}}
precision mediump float;

uniform vec2 u_resolution;
uniform float u_time;

float PI = 3.14159;

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    vec3 color = vec3(sin(st.x*PI-u_time));
    gl_FragColor = vec4(color, 1.0);
}
{{</glslCanvas>}}
