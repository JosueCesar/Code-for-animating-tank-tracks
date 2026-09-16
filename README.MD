How to make animation for tracked vehicle/tank tracks (free code sample)
https://www.reddit.com/r/godot/comments/1oeuqwa/how_to_make_animation_for_tracked_vehicletank/

Recently I made post about my progress with animating tracked tractor, which you can see by link bellow, and here I will explain how I did it

https://www.reddit.com/r/godot/comments/1oe582h/finally_i_have_some_nice_trackstank_tracks/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button

So first I want to clarify that this post is specifically about animation part, actual physical part can be what ever, personally I used raycast based vehicle, but you can do standard VehicleBody + VehicleWheel like in this tutorial:

https://youtu.be/2RmJow2YAuc?si=b5gqU5F14ZU1ckJi

Essentially you just need vehicle that has a lot of wheels and turning works by breaking and switching direction. Only thing that you will need to add on top of that is simple script that will rotate sprockets with same speed as wheels, because in my system speed of rotation of tracks and rollers depends on speed of sprocket rotation, that way it was easyer for me to make them synchronized

Btw English is not my native language so terminology might be off

For animating roller arms and springs I just use two scripts and both of them are simply aim on some target by rotating around chosen axis. Roller arm has its origin in place of its rotation axis and will aim on hidden mesh, in case of VehicleBody it will be one that you use one added to VehicleWheel, in my case I have logic in my raycast code for mesh that follows physical wheel center. Reason why this mesh should be hidden is because it will move only up and down, but roller on arm will move along the radius, radius which is equal to roller arm length, and easiest way to achieve this on radius movement is to simply connect visible mesh as a child of roller arm, and make it rotate via code.

Now in my tractor you can also see that I have spring between two roller arms, that can also stretch and moves it tact with arms. To achieve this, spring connected as a child of first arm, it also has its origin in place where it should visibly connect to arm, and have a script that will aim and stretch it to Marker3D that is child of second arm

So node structure will look like this:

r/godot - How to make animation for tracked vehicle/tank tracks (free code sample)
Now about tracks. So initial setup and part of the code comes from this video, plank mesh part specifically

https://youtu.be/Gfpnxg-jne4?si=DrSmXkALek2iJUWA

Thus for track I have a Path3D with MultiMeshInstance3D as a child (make sure that transform format of multi mesh is set to 3D). All I have to do is to somehow dynamically change Path3D and move MultiMesh

To change the path I use raycast. In my code I have an array for RayCast3D, and code will connect first point of Path3D to first ray in array, second point to the second ray and so on, so each ray should have a point for it. and I have ray roughly every 0.2-0.3 meters. Now those points position will be set to ray collision point, therefore target position of ray will be max extend of corresponding point of path, and whey ray is colliding this point will be in position of collision. So yes, track absolutely don’t care about actual physics or position of other meshes, all they do is adjust to terrain via collisions of ray, so keep that in mind, for example you need to set your physical wheel diameter to include thickness of track, otherwise rollers will sink into track

In my setup I have 11 rays, since I also use them as raycast wheels, 1 and 11 is not connected to path they just for front and back track grip, 2 and 10 are used for angled parts of track, and all in between directly in contact with ground, and I deliberately made them with slightly different length of ray, so track will visually sag when not it contact with ground

r/godot - How to make animation for tracked vehicle/tank tracks (free code sample)
To add some inertia to tracks, first I make so points of path will not just snap to collision points, but will do that with delay, and second I created an array in which I can add some points that is not used for rays, and they will move up and down depending of sprocket rotation direction, that way I can make top part of track visually sag when moving forward and to stretch them when moving backwards

To set spacing between links I just have export var for their amount and I simply divide path length by amount of links and than place link on that distance, with some adjustments to prevent unwanted movement

The only thing that is left is to make everything move synchronously with wheels. As I said in the beginning In script for vehicle you need to add function that will rotate sprockets according to how wheels are rotating. Now in Path3D script I also have functions that will get rotation from sprocket and then calculate how much other parts should move, for rollers it will be sprocket rotation times some coefficient (I manually calculate it by sprocket diameter / roller diameter = needed coefficient, and set them thru export vars), I have separate export var for front roller and two arrays for ground and top rollers. To move MultiMesh I use very similar thing that guy in that “path based mesh generation”, there he had offset for planks movement, in my code I just made it so that offset will be corrected by sprocket rotaion, I have var for distance per rotation of sprocket, each rotation of sprocket will move tracks on that distance (to find this distance it’s against simple manual math, sprocket diameter * 3.14)

I understand how hard is to make it work, especially when you noob like me, so here you can just steal my code, there only 3 scripts there, that do all of that, note that they are mix of tutorials, ChatGPT and lack of my coding skills, so most likely suboptimal, but at least it’s free and :) here link to repository

https://github.com/AnderHominem/Code-for-animating-tank-tracks

Also If liked this post, I would be really happy if you also will watch this video, would be very useful

https://www.youtube.com/watch?v=dQw4w9WgXcQ&list=RDdQw4w9WgXcQ&start_radio=1

If I forget something, I will add this later in comments
