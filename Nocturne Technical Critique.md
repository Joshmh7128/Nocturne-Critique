Backlinked: [[Nocturne Technical & Design Critique Homepage]]
#nocturne #outpost4 
# Introduction
This page will be dedicated to a general overview of Technical Critique information regarding everything from project architecture to rendering. I'll be doing my best to cover each area in more depth within their own pages, keeping each section of this overview brief and digestible. That way members can more easily access and explore this document without having to sift through a wad of information. However, if I feel that the section can fit in three paragraphs or less, I will leave it on this page.
# Code Readability
#actionable-critique 
As a general rule in Unity & C# it is best practice to adhere to the [Common C# Code Conventions.](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)While it is not necessary from a technical standpoint, as a new developer entering the project I'm finding it quite difficult to adopt and understand aspects of the code. This ranges from the agnostic use of camel casing, lack of comments, summaries, and subsequent reasoning behind documentation. Other readability critiques include unnecessary empty lines, cramped statements, commented lines of old code, inconsistent spacing in mathematics, undescriptive and unused variables. 
**For more, check out: [[Nocturne Code Readability Assessment]]**
## Code Reviews
One quick and easy method to solving this is to run a [Code Review](https://google.github.io/eng-practices/review/reviewer/) collectively as a team. Especially as the number of developers and designers grows in size, you will need to start writing effectively readable code. Code reviews focus group attention on the project and bring a collected sense of understanding, as the entire codebase of the project slowly becomes active knowledge of all participants. 

Code Reviews may feel unnecessary at first. Try [Timeboxing](https://hbr.org/2018/12/how-timeboxing-works-and-why-it-will-make-you-more-productive) your reviews. This not only forces them into small chunks of time, but helps to keep the meeting moving. It also stops the team from wasting time. By keeping the flow heavy and moving you rarely find the meetings feeling unhelpful or bloated.
# Unity Architecture Leverage
#actionable-critique 
## Anti-Component Architecture
#actionable-critique 
As a whole Nocturne appears to be based around a significantly smaller amount of scripts than I expected, many of which depend on one another for functionality. This is a common misnomer when computer science engineers construct game projects. They cement systems together into an architectural state which cannot be undone. If each and every component of the project relies on one another it becomes impossible to manage and comfortably move objects around.

Consider rebuilding these aspects as aggregate components which can be changed out as needed. For example, the majority of the game is run out of very few classes. Consider breaking these classes into a non-dependent structure in which elements are not required. 

For example on our ``Instrument`` object which holds the ``Nocturn.cs`` component, we can break these elements down to a semi-atomic level. While it is good in some cases to have all of the elements of the instrument in one unified script in specific situations, we are immediately presented with constraints. Lowering the elements to a more object-based level would help with more comfortable instrument creation - what if I wanted to place the keys (referred to as notes I believe in the code) in the form of a drum set, rather than the patterns pre-determined by the program?

An anti-component architecture results in code which is difficult to change, replace, modify, or refactor. **It is important to note** that I am not simply saying: *Make everything an interface so you can swap out entire functions on the fly!* That may be good for specific use cases (a player's input driving a series of vehicles for instance) but in our case we want to build a series of non-dependent independent classes which *conditionally* read the rest of the game. There are a few great ways to build this architecture, one of them is to leverage the efficiency of Events in C# (mentioned below).
## Implementation Feasibility of Component Architecture
#subjective-critique 
While it may be too late in the project now to consider a change to a more aggregate philosophy of construction, it may be worthwhile to consider this in the future. At the moment manipulation of any specific integral game system results in all other systems interacting with it to throw errors. This makes it extremely difficult to rebuild the aspects of this game from the ground up in a component format.

Due to the complexity I think that this project is too far along for its aspects to be rewritten healthily in an aggregate structure, and the best choice is to stay the course with what has been built thus far. Attempting to switch architectural ideologies will simply cause anguish amongst the team.
### Component Architecture Rationale
#actionable-critique 
When building Unity the designers purposefully engineered a components system which was able to unload elements dynamically. This was to prevent similar issues that had arisen in the XNA framework in which C# garbage collection would be doing more and more work overtime to free up memory. Compared to C++, C# memory management is no where near as efficient. That being said, the fact that its garbage collection is as strong as it is automatically is nothing to scoff at.

It's important to remember that components in Unity are entire scripts being loaded and unloaded from the stack. When a GameObject is inactive it also no longer runs its Update functions. They are still kept in memory but they are not actively impacting the performance of the game.

Due to the current nature of how Nocturne is constructed and the intensity of the game's hierarchy, memory management is my biggest concern. There seems to have been very little consideration or leverage of Unity's component framework in this case.
## Scene Architecture
#actionable-critique 
Nocturne's scene setup is rooted fundamentally in the construction of only a few scenes. This is good practice for a short demo, but as time goes on you will find that this becomes quite constrictive. Large scene files are more taxing on the memory of machines as they cannot be easily [asynchronously handled.](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.LoadSceneAsync.html) 

You will find that the easiest pattern to work around this is building an additive scene loading pattern in which different aspects of play load in difference scenes asynchronously only when they are required. In many cases you can build an [asset bundle](https://docs.unity3d.com/Manual/AssetBundlesIntro.html) to more effectively load this on slower platforms. 
# Architecture Pattern
#actionable-critique 
As far as I can tell from a technical standpoint there is not a specific architectural pattern used in Nocturne to organize or link together any of the scripts in the projects. The general aspects of the project appear to be linked together on a case-by-case basis. Without a standard set for how scripts talk to one another what results in an extremely hard-coded project with an extremely high dependency on itself. This makes changing or prototyping incredibly difficult. 

In addition to a project that is difficult to prototype or change, this also results in one which is difficult to untangle. It turns into a mess of spaghetti which becomes tied together quite tightly, resulting in an inseparable pattern.
# Event Usage & Check Structure
#actionable-critique 
After reviewing the architecture of Nocturne I feel it worth mentioning that there is no use of [C# Events](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/) throughout the project. The Publisher/Subscriber model is one used commonly in games, and is baked into the majority of modern Game Engines. 
### Event Structure
For example: Unity's Event system is a Unity-wide core plugin which has their own form of C# Events. Godot's fundamental development structure is centered around their own version of this, which they call Signals.

These function exactly as they sound. Objects in the space of the program are given Events which they can Publish at an Internal level. Other Objects in the program which have Subscribed to these Events can quickly and easily receive information when these Events are Published. 

You can already imagine basic puzzles in which this would come in handy. They key point between a Check structure and an Event structure, is that Events compromise definitive stability for efficiency.
### Check Structure
Alternative to the Event Structure is the Check Structure. This is the opposite of Events, in which each element of the program which has to react to change runs a check every tick of the clock to see if their values are true. If done improperly this can be significantly more inefficient. That being said, the Check Structure makes for a significantly more reliable and well-built game. This pattern is more commonly seen in Action games, due to their high speed and need for consistency.
#### Efficient Check Structures Through Heuristics
Building an efficient Check Structure requires fast Accessibility and Readability. For this example I'll be referencing work I did on [GODWALKER](https://joshhirshfield.com/godwalker), my graduate thesis project. 
#### Example
Let's take a Player in a first person shooter. When they get hit by an enemy projectile we want them to take damage. In an Event based structure we would have the:
- Projectile Fire and Fly through the air
- Player Subscribes to that Event
- On Collision with the player, Publish their "Player Hit" Event
- Player Takes Damage & Projectile is Destroyed

This raises a few problems. Dynamically Subscribing to Events is easy enough to manage, but once those Objects are gone we won't have any references, and we'll then have to ensure we're managing that list of Events properly. If groups of Events are managed incorrectly they can sometimes misfire or false-fire on their subscribers. It also becomes more difficult to discern which bullet hit us, if we wanted to have different elemental shots for example.

The easiest Unity-based solution is to use a collider and then have all bullets check for Collisions, check, get, and hurt the player directly, then destroy. This has the game check frame by frame to see if each individual bullet has hit something. This check is extremely taxing because it taps into PhysX, causing the Physics timestep to increase.
#### Rationale
Instead we can build a [Heuristic.](https://softjourn.com/insights/heuristic-programming#:~:text=In%20computer%20science%2C%20a%20heuristic,a%20reasonable%20amount%20of%20time.) Because PhysX checks are taxing to the CPU, GPU, and Memory, let's scrap it. Instead let's create a list of active projectiles in the scene, then once each ``FixedUpdate()`` we cycle through the list checking the Distance of each projectile to the player's position. 

(Note: We are using the ``FixedUpdate()`` here rather than the ``Update()`` because it runs significantly fewer times per tick. You *can* use this pattern in the ``Update()`` function, just test beforehand to ensure it is not dampening performance)

Because we know each projectile is spherical we can apply their own individual radius as the distance to check against. We then very simple say ``if Projectiles[i].DistanceToPlayer() < Projectiles[i].radius`` and voila. This is significantly more usable than the Event pattern, and highly efficient.

We've reduced Dynamic Event Subscription & Firing to a PhysX check, all the way down to comparing two floats. 
# Project Dependencies
#subjective-critique 
This project is based incredibly heavily on third party assets and packages. While this can be good for smaller more contained projects, building dependencies on assets for commercial projects of scale can quickly become hazardous. This is mainly due to the unfamiliarity of how a third party asset functions compared to a homegrown system. 

Third party assets should be seldom used. Situations in which they are viable are when the asset is something the development team could not do within a reasonable time. For instance, volumetric lighting is not something which can be done in an effective time, even if the development team is incredibly skilled. 

When a team does end up relying on plugins, they should select a handful and then dedicate the time and energy to building a fundamental understanding of how they work. When a game begins to bring in assets from several different areas, especially when those assets are asked to talk to one another, progress begins to halt. There becomes an unneeded web of complexity and reliability that takes just as much time and effort to be made undone.
# Project Hand-Off State
#foundational-critique 
Upon receiving the project I was unable to compile on any of the major branches. This appeared to be due to the dependency structure of the project, but more-so that many crucial aspects of the project were not properly setup to track in the project's Git repository. Because I had none of the proper files or access to the plugins / packages, I was unable to resolve this. 

While I was able to resolve some of these errors by running several package wizards, at the time of writing this I am unable to resolve the problems locally. This is a crucial failure in the project's production. If the Outpost 4 plans to bring more developers on to this project in the future then this issue needs to be resolved immediately.

This problem either needs to be solved through procedure or practice.
## Automation
#subjective-critique 
While automation of this process would make onboarding of new developers faster, it should only be taken into consideration when the developer amount exceeds that of 5. The majority of manual setup can be done locally by each developer, given that they have the proper documentation available. Currently the project requires rebuilding the Shapes plugin library, ensuring the user has access to the full CurvedUI plugin, and reimports the project for Android. 

I am sure there are more aspects of project library construction which I missed. It will take a full technical markup of someone with more familiarity and understanding of all the plugins, systems, and setup of the project in order to construct a full technical markup for first-time-project-setup.
## Asset Settings Non-Transferrable
#actionable-critique 
One aspect I've found through brute-force in my (eventually successful) attempts to get the project working on Windows was that asset settings were non-transferrable through GitHub pulls. This resulted in me having to regenerate a majority of the asset dependencies locally. None of this is an impossible feat, but it should be noted for future collaborators.
## Operating System Theist
#subjective-critique 
At the moment the Unity Project favors the Mac OS operating system. This is due to the structure and nature of the way generated files are built. When launching the project on a Windows machine it can be more than difficult to properly set it up. 
# Third Party Save System
#actionable-critique 
Currently it appears that save files are being utilized through a third party asset called "Easy Save 3." As many aspects of the project are currently dependent on this, I highly recommend shifting away from an asset and building a bespoke save system from the ground up. From what I understand the game does not currently reconstruct songs reliably, resulting in a difficult end user experience.

While the asset may be something which is helpful for some games, due to the procedural creation and customization of Nocturne, the team may want to consider moving away from this setup as it appears to be built for games with a linear progression, rather than ones with dynamically user-built aspects.

In my opinion, for procedurally built aspects, there are two main methodologies to consider when building this out...
## Constructor Argument & Rationale
In many games which feature musical elements, such as *Guitar Hero, Beat Saber,* and many more, tracks are "mapped" to a specific set of notes which are played back through the song. The track is constructed in real time, then rebuilt on the spot. Constructors are a well known concept in programming. 

For Nocturne, the team may heavily want to consider the construction of a specific file-type and constructor system which is independent and built directly into the game itself. Rather than relying on a third party asset to handle the holding of elements, building a specific project file which saves and loads each block on the score will enable more complex and specific elements to be saved rather than worry about the overhead of third party assets.

The Score will have to be translated into a file, saved, and then on load it must be untranslated. One of the most difficult aspects of this methodology is going to be the testing which goes into saving and loading, as there will be countless aspects that have to be cross-referenced in order to ensure portability.
### Cloud-Service Implementation
Additionally by making the saved data into a custom text file, this opens up the ability for the files to be more easily uploaded and downloaded from cloud services. This includes cloud-save services for Steam and Epic platforms, but also opens opportunities for both the Steam Workshop and sharing songs with one another online. 
## Direct-Manipulation Argument & Rationale
Typically in DAWs which are built and used on a computer the program will be modifying the file in-memory, with the aspects of the track displaying what is currently loaded. For many games with procedural elements it is also commonplace to directly manipulate a file at runtime and then save it intermittently.

Good examples of this model are seen in city builders. SimCity 4 loads the entire map and all its data into memory, then as the player plays and manipulates the world the file is overwritten on save. This creates a more sustainably holistic form of file-management, as it is clear that the thing you are modifying is the thing which will be saved. This is lost in the Constructor model, as you are saving data which can be used to rebuild the state you are currently in, rather than the current state of the Score as a file.

While this model is more reliable and direct, it does mean potentially larger file types, and a more intense memory bandwidth. That being said, by building this holistically we can more assuredly realize a save system that reinforces the stability of Nocturne.
# File Organization
#actionable-critique 
# Repository Pulse
#actionable-critique 
-Not enough commit frequency

# Reliance on Oculus Package
#actionable-critique 
-project relies to heavily on the Oculus input package