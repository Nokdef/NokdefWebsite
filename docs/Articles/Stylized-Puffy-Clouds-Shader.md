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
![Wrinkles](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-4.png){ width="700" }
<figcaption> Wrinkles highligted.</figcaption> </figure>
The rule of thumb is that the brighter our noise, the more our clouds will "puff out", and the darker the noise, the less. 

**This means that white pixels will have most influence over our vertex data, and black pixels will have the least.** 
### Let's make the layer function
To avoid writing the same code on Amplify 4 times, let's make a shader function, I'll call mine Clouds Layer;

<figure markdown>
![Creating Shader](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-5.png){ width="900" }</figure>
Inside the function, make a new **Noise Generator** node. You'll notice it will be completely gray. We need to feed the inputs for this node to make anything show up. 

Let's start with the scale, this increases or decreases the size of the noise relative to the box, essentially how "zoomed in" we are. Make a new **Function Input** and name it **Noise Scale**.
!!! info "How do I name my Function Input?"
    You can change the name/settings of any node in Amplify by selecting the node and changing its settings on the left-side panel.

Finally, if you plug a **Texture Coordinates** node onto the UV input, you'll see that as you change the default value of the **Noise Scale** input, the noise preview will change accordingly. I'll leave it at 5 for now. 

Your graph should look something like this now: 

<figure markdown>
![Creating Shader](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-6.png){ width="900" }</figure>

Now, to add extra complexity, let's duplicate everything except the **Noise Scale** and add them together by plugging both noises onto an **add** node.

This might seem redundant for now, but we want to make these noises move over time. So let's create a new **Function Input** and name it **Noise Speed**.

Our idea is to offset the noises by a given multiplier (**Noise Speed**) over time, then make one of the noises slightly slower, thus making our final addition a mishmash of different speed noises. 

We can do this by multiplying our **Noise Speed** by a **Time** node, I'm also using the **Time** node on a .1 scale, since I want the clouds to be very slow moving.

Then we gotta ensure one of the noises is faster than the other, I did this by multiplying the bottom speed by .5, thus halving it.

<figure markdown>
![Creating Shader](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-7.png){ width="900" }</figure>
!!! question "Why **Divide** at the end?"
    The noise generated ranges from 0 to 1. 0 means the vertex will be unmoved, while a value of 1 will raise the vertex upwards to generate the fluffiness. Since we're adding 2 noises together that have values peaking at 1, we'll inevitably have some parts of the result that will extrapolate the limit of 1 (2, 1.5, etc). 
    When we divide these values, we'll ensure that the highest possible value is always 1.

This would already work really well for a cloud layer. However, I'd like to add some extra knobs we can tune later.

First, let's add the option to make some darker parts of our noise influence the vertex data more meaningfully.

As you know, black pixels have no influence on the vertex data of our clouds, so we can turn these black pixels into negative pixels, then we can turn those negative pixels back into positive values. We can achieve this through **Remap** and **Abs**.

All remap does, is that it turns a given range of values into another given range of values linearly, think of it as a cross-multiplication operator for your shaders.

So, we can turn our black pixels into negative values by declaring a node structure like so: 

<figure markdown>
![Creating Shader](https://raw.githubusercontent.com/Nokdef/NokdefWebsite/main/docs/StylizedPuffyClouds-8.png){ width="900" }</figure>
This means, our previous pixels with a value of 0 (black) are now -1.
Make sure to turn the **Noise Remap** into a **Function Input** so we can change its value later on our materials. 

Remember I mentioned we'd turn the negative values onto positive values, so we could add that extra data as vertex displacement later? Well, to do it, you can simply add an **Abs** node after remap.

!!! info "How does **Abs** work?"
    It gives you the mathematical Absolute value or Modulus of a given number. It is essentially "how far from zero is this number?" indicator. Meaning that the Modulus of <span style="color:orange;font-size:1em">3</span> and <span style="color:orange;font-size:1em">-3</span> is <span style="color:orange;font-size:1em">3</span>! Since both are <span style="color:orange;font-size:1em">3</span> numbers away from zero!

!!! question "Why not use **Negate** or Multiply it all by -1?"
	Essentially, we use **Abs** when we're not sure if the number is positive or negative. In our case, we'll never truly know if each individual pixel we're operating is positive or negative, so this rules out **Negate** as a viable option. As for multiplying the pixels by -1, we'd need to check for each individual pixel if they are negative before doing it, which is prohibitively expensive. Thankfully, basic arithmetics come in clutch with **Abs** to save the day.
