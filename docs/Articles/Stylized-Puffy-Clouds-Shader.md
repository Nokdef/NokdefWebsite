---
template: article.html
title: ☁️ Puffy Clouds Shader
---
# <span style="color:#71c174;font-size:1em;font-weight:bold">Stylized Puffy Clouds Shader</span>  
<span style="color:#4cae4f;font-size:.75em">A quick way of getting fluffy anime-esque clouds using a simple noise and vertex manipulation. Reasonably cheap technique, perfect for eerie backgrounds.</span>

---
## What are we building?
Here is a small preview for the final result of this shader. This scene is dressed up with some pretty crystals and ambience particles to display what it could be used for, but we'll be focusing on the clouds for this particular article.

<div style="padding-bottom: 56.25%; position: relative;"><iframe width="100%" height="100%" src="https://www.youtube.com/embed/Vk2qhqPASqo?autoplay=1&loop=1&modestbranding=1&mute=1&playlist=Vk2qhqPASqo&rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%;"><small>YouTube embedding powered by <a href="https://embed.tube">embed.tube</a></small></iframe></div>
Here you can see the clouds in isolation, using more neutral elements to showcase the depth fade:

<div style="padding-bottom: 56.25%; position: relative;"><iframe width="100%" height="100%" src="https://www.youtube.com/embed/x99FqxiKzlc?autoplay=1&fs=0&loop=1&modestbranding=1&mute=1&playlist=x99FqxiKzlc&rel=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture; fullscreen" style="position: absolute; top: 0px; left: 0px; width: 100%; height: 100%;"><small>YouTube embedding powered by <a href="https://embed.tube">embed.tube</a></small></iframe></div>
!!! warning "Before you read..."
    Please keep in mind, <span style="color:red;font-size:1em;font-weight:bold">we'll be using Amplify Shader Editor in this tutorial</span>, but the knowledge and concepts behind it can be easily converted to Shadergraph, HLSL or Unreal's material editor.
    
    We'll also be using the default built-in rendering pipeline, which again,
    can be easily converted, especially since this is a Unlit shader.
---
## Graph Setup
On Unity, let's make a new surface shader on Amplify. We'll call it Puffy Clouds.
<figure markdown>
![Creating Shader](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-1.png){ width="900" }</figure>
Let's open that up and make sure some of the settings are properly set.
<figure markdown>
![Shader Settings](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-2.png){ width="500" }</figure>
Only the highlighted options need to be set accordinly, let's go over them individually.

* Shader Model - Anything above 4.6 will work here. This is just so tessellation plays nice. 
* Cast Shadows / Receive Shadows - You could leave these enabled, but I personally like to disable shadows to achieve the cel-shaded/anime-esque look.
* Render Type / Blend RGB - We just want to ensure we're doing the traditional alpha blend (Relevant for Depth Pass later).
* Tessellation - Make sure this is enabled. I use an Edge Length of 25, but this value will be exposed on a per-material basis, so we don't need to worry too much about it. 
* Depth - Make sure we're z-writing.
---
## Making the Puffiness
We achieve the puffiness of the clouds through vertex manipulation, meaning we change the position of each individual vertex of a given 3D model (in this case a plane) over time to shape it like a stylized cloud. We also apply tessellation to our cloud, which essentially generates new vertices in real time to fit our needs. This can mean a loss of control over how expensive the cloud gets, but if you are adapting this for a more performance sensitive project, you could always pre-subdivide the plane and drop the tessellation from the shader.
<figure markdown>
![Vertex Demonstration](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-3.png){ width="700" }
<figcaption> Vertex are the points in 3D space that define a model's geometry. In red we can see one being pushed upwards. This is what our shader will do, but with a lot more points.</figcaption> </figure>
So, let's make our vertex manipulation shader!

For this, we'll be using 4 layers of moving noises, the noises will control the height of each individual vertex point. Later, we'll blend all 4 layers together, which helps with the complexity of the cloud, and adds some nice "wrinkles" to the cloud. 
<figure markdown>
![Vertex Demonstration](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-4.png){ width="700" }
<figcaption> Wrinkles highligted.</figcaption> </figure>

