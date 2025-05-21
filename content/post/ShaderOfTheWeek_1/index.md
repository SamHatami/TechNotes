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
