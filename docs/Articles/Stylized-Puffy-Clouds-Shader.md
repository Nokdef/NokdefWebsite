---
template: article.html
title: ☁️ Puffy Clouds Shader
---
<span style="color:#71c174;font-size:2em;font-weight:bold">Stylized Puffy Clouds Shader</span>  
<span style="color:#4cae4f;font-size:.75em">A quick way of getting fluffy anime-esque clouds using a simple noise and vertex manipulation. Reasonably cheap technique, perfect for eerie backgrounds.</span>
___
# What are we building?
Here is a small preview for the final result of this shader. This scene is dressed up with some pretty crystals and ambience particles to display what it could be used for, but we'll be focusing on the clouds for this particular article.

<div style="padding-bottom: 56.25%; position: relative;"><iframe width="100%" height="100%" src="https://www.youtube.com/embed/Vk2qhqPASqo?autoplay=1&loop=1&modestbranding=1&mute=1&playlist=Vk2qhqPASqo&rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%;"><small>YouTube embedding powered by <a href="https://embed.tube">embed.tube</a></small></iframe></div>
Here you can see the clouds in isolation, using more neutral elements to showcase the depth fade:

<div style="padding-bottom: 56.25%; position: relative;"><iframe width="100%" height="100%" src="https://www.youtube.com/embed/x99FqxiKzlc?autoplay=1&fs=0&loop=1&modestbranding=1&mute=1&playlist=x99FqxiKzlc&rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%;"><small>YouTube embedding powered by <a href="https://embed.tube">embed.tube</a></small></iframe></div>
!!! warning "Before you read..."
Please keep in mind, <span style="color:red;font-size:1em;font-weight:bold">we'll be using Amplify Shader Editor in this tutorial</span>, but the knowledge and concept behind it can be easily converted to Shadergraph, HLSL or Unreal's material editor.
How do I stop the info block?

How do I stop the info block?