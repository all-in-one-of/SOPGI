# SOPGI ( Manual and general Infos)
A SideFX Houdini `VEX raytracer` with photon mapping global illumination and full recursive reflections and refractions.
Tested on SideFX Houdini 16.x.x

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_cover.jpg)
`Very high poly count geometry processed with SOPGI at high settings. Viewport is set to Hidden Wireframe`


### SOPGI work on the geometry points, so to viz the result properly set your viewport to HIDDEN LINE INVISIBLE mode ( WIREFRAME ) 
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_intro_new.jpg)

# DISCLAIMER
- SOPGI is a personal side project to teach me
 how to do mostly global illumination with photons
 and many other things I learned on the way.
 It try to be correct enough to make things look good.

- It also doesn't want to be a renderer.
 It is mainly a solution to store lighting, shading and GI
 data into the geometry to use later for things
 like any other attribute.
 
- You will want to use it on simple scenarios like an object
 and some surrounding to bounce light around.
 Not for large open environment or complex interior scenes.
 It is after all an utility set of nodes to generate data
 to use later along the pipe.
 
- It require some tweaking there and there,
 but if you get the hand of it and you are willing to work
 your way up from lower to higher settings it can produce
 some great images/solution and with all the render data stored per point
 you will be able to do some nifty tricks down the line in Houdini.
 
- For animation, you will need a toons of photons
 to remove the noise/flicker in the GI solution.
 It is just an almost brute force solution after all.
 
- It's been architected to give you the best result as quick as
 I could possibly make it so it is to be considered
 a very Novel approach in many areas.
 
- Everything in here is more for educational purpose
 so do not expect a SolidAngle Arnold render quality 
 and performance out of this.
 
- Some of the functionality have been implemented in a
 simple fashion.
 Please update this repository if you add or improve anything.
 
- Some part have been coded earlier and some other later in time
 so some may be a bit incorrect from a pure mathematical point of view
 then some other as I was learning on the go.
______________________________________________________

# CONTENTS
SOPGI is comprised of a set of nodes to generate and store
a Raytrace, GI, ShadowMaps and Recursions
on a per point level.

Every aspect of the render data
will be stored on a per points attributes
for later use as you would with any other attributes.

SOPGI Node's list:
- # SOPGI_ARay
- # SOPGI_BRDF
- # SOPGI_CornellBox
- # SOPGI_Light
- # SOPGI_RAYviz
______________________________________________________

# SOPGI_Aray
This node is the core renderer.
It will generate all the render data and output the final attributes value. 
It will also comp out the final result and store it on the `Cd` channel for you ro review.
The second optional output, will output only the photonmap points.

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_aray_modularity.jpg)
`Inside of the SOPGI_ARay node. Its been coded per components in a modular way.`

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_aov_attrib.jpg)
`Final SOPGI result followed from Left to Right by: BRDF, AO, GI and Shadows samples.`
______________________________________________________

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_aray_node_menus.jpg)
`SOPGI_ARay node parametrs It is subdivided in 3 main Tabs`

- #### ARay Core:
Will give you the ability to change the number of samples to use for the Raycasting calculation.
For example you can change the number of photons or AmbientOcclusion rays to use.
There are also optimization parameters you can tweak to speed up the calculation and
every parameter responsible for speeding up the process
are marked with an asterisk character `*` in front of their name.

- #### ARay SamplesWork:
`SOPGI RECURSIONS are calculated on a per point basis and so not affected by "ARay SamplesWork".`

Since everthing get stored on photons, SOPGI ARay create a point cloud
with all the necessary attributes and everything get transferred to the Geometry
using point clouds mechanism. This tab let you control how those values get
transferred like the point cloud Radius, precision and Gaussian Blur filter to fine tune
the final solution and light irradiance.
#### This can heavily impact the final quality.

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_samples.jpg)

`To keep the tool performace as high as I could, I opted not to use "Shadow Rays" 
but instead implement "Shadow Maps" as they are quicker and those are generated
from point clouds
and so affected by ARay SamplesWork as well.`



- #### ARay Compositing:
This tab let you tweak the way every single render data get composited together.
Since this happen after the raycasting, depending on your geometry tessellation,
it can be as fast as realtime.

`In this implementation the "Compositor" node is very simple but eventually
it will get expanded to do some nifty tricks with the available Render Data.`
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_compositor_new.jpg)
`SOPGI calculate RECURSIONS after the "Compositor" node complete.`

______________________________________________________

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_useBRDF.jpg)
The " `USE BRDF` " option on the main SOPGI ARay's node option tab will disable all the heavy calculation
and only output Diffuse and Specular to work with the Recursive Reflections and Refractions.
This way you can preview Recursions very quickly and tweak Specular and Diffuse 
before moving into tweaking GI etc.

More info about this node soon.
______________________________________________________


# SOPGI_BRDF
This node will prepare a geometry to be plugged and be processed by the SOPGI ARay node.
It will attach all the shading attributes so in some way this node is your Material Shader.
From here you will control the apparence and the Recursive side of it.

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_brdf_param.jpg)
`SOPGI_BRDF node parameters`

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_brdf_types.jpg)
`SOPGI Aray node set to "use BRDF" only.`

and some more advanced example using all the features:
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_examples.jpg)
`SOPGI examples/hip files included in this package.`

For Reflection or Refractions, you can only have full mirrors or full transparent shader or a mix of both.
______________________________________________________
It does include for DIFFUSE:
#### Lambert, Oren Nayar

For SPECULAR:
#### Blinn Phong, Cook Torrance, GGX, GGX1, GGX2
______________________________________________________
You can also apply textures to your objects as long as they get stored into point
attributes and inside `v@diffuseColor` SOPGI ARay will pick them up automatically and use
them in the computation.
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_textures.jpg)
`Simple anti-alias noise applied to the cornellBox.`
______________________________________________________

# SOPGI CornellBox
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_cornellBox_small.jpg)

This node is a simple utility node that will let you quickly generate
a CornellBox geometry and tweak its colors.
It will also let you create some simple geometry inside the box 
like a sphere and some standard Houdini geo and they will also be all animated 
so the sphere and other geo will rotate inside the CornellBox
for you to test out SOPGI render feautures.

______________________________________________________

# SOPGI_Light
This node will let you convert any Geo into a SOPGI Light to be plugged into the SOPGI ARay node.
In this implementation LIghts are very simple, they have a color, and intensity, a distance radius and samples count.

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_light_param.jpg)

`SOPGI_Light node parameters`

![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_light.jpg)
`Increasing the light distance/radius.`

______________________________________________________

# SOPGI_RAYviz
This node is an utility node to let you visualize Light Rays along their traced path.
It include light bounces so you can specify how many light bounces you want to viz.
Since Recursions has been implemented later, this node does not take into account
transparent/IOR geometry.
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_RAYviz_new.jpg)

# Some more example images.
![alt text](https://github.com/alexnardini/SOPGI/blob/master/img/SOPGI_into_new.jpg)
`Caustics: since SOPGI recursions have been coded later,
the photon map generation algorithm does not take into account refractive objects yet and so we
get caustic photons by reflections only.`

