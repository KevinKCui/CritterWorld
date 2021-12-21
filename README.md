# Critter World Simulation
#### Fall 2021 Final Project for CS 2112 @ Cornell (Honors Object Oriented Design and Data Structures)
#### by Kevin Cui, Bryan Lee, and Noah Schiff

___
### Project Overview

The goal of this project was to simulate a world of critters with the capability to move, eat, reproduce, evolve, and die. To that end, we had several goal milestones for this project:
* Recursive descent parser and interpreter for a simple critter language (context-free grammar with 16 production rules)
* Model and controller for critter world simulation
* Graphic user interface to visually represent the model

We split up the major components into different packages, and team members worked individually on them in order to modularize our program and ensure loose coupling. Additionally, we created a rigorous test suite of parameterized, random, blackbox, and glassbox testing to check the validity of all modules. In total, this project took around 12,000 lines of code in Java and was completed over the span of a month and a half.
</br>
</br>
*Note: in accordance with Cornell University's Academic Integrity Policy, we cannot show any of our code. Still, we would like to provide a demonstration of our graphic user interface and highlight some features that we are especially proud of.*
____
### Graphic User Interface
Our GUI was implemented in JavaFX and was designed with user experience as the utmost priority behind functionality.
</br>
<p align="center">
<img width="862" alt="Picture of GUI" src="https://user-images.githubusercontent.com/40209975/146696510-a0606dac-d576-48c9-8ed6-fabecc461a4c.png">
</p>
As seen above, our GUI used a muted, pastel color scheme and was separated into three sections. The leftmost section contains the controls and details of the simulation, allowing a user to load in a new world, set the simulation speed, toggle features related to the world, view information about the current world state, and pop out a help menu. The middle section displays a view of the simulation as well as two buttons to zoom in and out. Further, the middle view supports scrolling, enabling a user to better navigate large worlds. Finally, the rightmost section has information about a highlighted critter, including its attributes, last rule/action executed, and program (a.k.a. its genome or brain). This section is collapsible and is only shown when a user clicks on a hexagon containing a critter.
</br>
Going into more detail regarding the world view, critters are represented as colored circles with an arrow indicating the direction they are facing. Critters of different species are visualized with different colors (determined through a hash table, mapping species name to RGB color). Food is represented as a yellow-orange hexagon labeled by the amount of food at that location. Rocks and out-of-bound spaces are represented as gray hexagons. Finally, empty spaces in the world are represented as light green hexagons.

</br>
In action, a simulation of the critter world running continuously looks like this:
</br>
</br>
<p align="center">
<img width="750" alt = "GUI in action"src="https://user-images.githubusercontent.com/40209975/146692687-536df529-c7bb-46c3-a8b4-93eef1dd1b8b.gif">
</p>

The simulation speed indicates how many turns are drawn per second, and it can be set anywhere from 0 all the way up to 1,000,000. Our rendering of the view is hard-capped at 30 frames per second, however, so at high speeds (i.e. >100) there is potential for the calculation of updated world objects to take longer than the 1/30 of a second, causing issues with the GUI. One such problem would be lag if the simulation controller became backlogged with world state updates, and drawing was only done after all the updates had been completed. Another potential issue would be invalid world renderings if the simulator tried to calculate steps from a no-longer valid world state and then tried to draw it. 
</br>
Ultimately, we didn't have to deal with these issues as we solved them during design, using **multithreading** and **synchronization** techniques. First, we split up the drawing and simulation onto different threads (a rendering thread and background worker threads) to improve efficiency. Second, we made the simulator only do calculations on a clone of the current world state and pass back a read-only version to the view controller at specified turns (predetermined through the speed). In this way, even as a user navigated the world (through scrolling or zooming) at high simulation speed, the GUI stayed clean and smooth.
</br>
</br> 

**ASCII Art vs GUI**

Due to the nature of the project assignment, we had to create our world simulation before the GUI. It would be nearly impossible to debug the simulation without some form of visual representation, so for that reason, we also created a basic ASCII art representation of the world that was displayed in the console. Because we implemented our code with a MVC design pattern in mind, doing this was very easy. Below is a side-by-side comparison of the same critter world, the left being the ASCII art version and the right being our final GUI representation.
<p align = "center">
  <img width="179" height = "330" alt="ASCII Art" src="https://user-images.githubusercontent.com/40209975/146839109-e697e023-2154-47e1-92c7-47316346b367.png">
  ___________
  <img width="400" height = "330" alt="GUI Parallel" src="https://user-images.githubusercontent.com/40209975/146839149-1bbeefcb-328b-45f8-ad5f-baa42475547f.png">
</p>  
</br>

**Example Critters**

Another aspect of the project was to write programs for specific critter behavior. These programs would be parsed into an AST when they were loaded in, and this AST would be interpreted at each time step, determining the action the critter should take at that given time. 

One such program we wrote was for a critter that moved in a growing hexagon spiral. Theoretically, on an infinite rock-less world, this critter would eventually reach every possible spot. Below is our program for the critter, affectionally named "Spiral Sam", and a demonstration of it in action.
<p align = "center">
  <img width="800" alt="Spiral Program" src="https://user-images.githubusercontent.com/40209975/146840895-4ab05754-b7a3-4641-a949-aa247aa3af3f.png">
  <img width = "750" alt = "Spiral Demo" src =https://user-images.githubusercontent.com/40209975/146841174-fcfbad6c-b772-41f5-a00b-41b4e01c1d32.gif>
</p>
</br>
</br>
Here is another critter, named "Ruthless Ricky", that wanders around the world and attacks other critters of different species. Below is its program followed by a demo of it in a small world with other critters (Ricky is the purple critter).
<p align = "center">
  <img width="800" alt="Ruthless Program" src="https://user-images.githubusercontent.com/40209975/146858908-400e4295-c56a-4c6d-9cc2-29eda71f9422.png">
  <img width = "750" alt="Ruthless Demo" src = "https://user-images.githubusercontent.com/40209975/146855978-6340ef0e-1941-446e-ada8-2af863c88fd7.gif">
</p>
</br>
</br>

And finally, we wrote a critter named "Bouncing Borat" that bounces back and forth. Its program is below, along with a demo of two Borats bouncing.
<p align = "center">
  <img width="503" alt="Bounce Program" src="https://user-images.githubusercontent.com/40209975/146858719-351327bc-d031-4cdd-b291-5f66be3b3804.png">
  <img width = "750" alt = "Double Bounce" src = "https://user-images.githubusercontent.com/40209975/146858263-533f3021-5e18-41f7-af10-b7674bf69ecf.gif">
</p>

____
### Testing
We tested the edge cases of the GUI by clicking all over the place in the GUI and trying actively to break it. Besides testing out the features and edge cases ourselves, we also conducted alpha and beta-testing with friends and family. Using their feedback, we made functionality more user-friendly and flattened the learning curve to use our system. In this way, testing the GUI was relatively straightforward.
</br>

Testing that parsing and interpreting was working, however, was significantly more challenging and time-consuming. This is where the bulk of our JUnit testing took place, as we created corner case unit tests programs and asserted that they maintained our abstract syntax tree invariant when parsed. We also used parameterized testing to create many thousand random programs and parse them, checking that each parsed program generated a valid AST. Beyond manually creating parse trees, we also developed a visualizer to make the AST testing process easier. Below is the visualization of the AST of "Bouncing Borat" shown in the previous section.
<img width="1440" alt="Bounce AST" src="https://user-images.githubusercontent.com/40209975/146847379-697379d2-ea54-46d5-b95c-fe45f4245811.png">
</br>
</br>
Besides the visualizer, another way we tested the AST was using **fault injection** that was built into critter simulation logic. As context, when critters reproduce, there is a chance their genome, or program, will mutate. There are various kinds of mutation, but regardless, all of them are valid changes to an AST and should not break our AST invariant. Thus as an extra reinforcement to our tests, we parsed in a known valid program, recursively called random mutations, and ensured that at each step of the way, we had a valid AST. Through this extensive testing, we became confident that all of our components were working and that our critter world simulation worked seamlessly.
