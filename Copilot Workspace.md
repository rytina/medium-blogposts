**My first experience with GitHub Copilot Workspace**

Why do we need such a thing?

TL;DR - To make software development more accessible by relentlessly breaking down barriers with the assistance of AI agents.

Over the last decades, barriers to software development have significantly diminished, thanks to advancements like higher-level programming languages and powerful development tools. Modern Integrated Development Environments (IDEs) come equipped with features such as auto-completion, syntax highlighting, code suggestions, linters and many more. These allow developers to focus on problem-solving rather than struggling with intricate language details.

The arrival of Generative AI has taken this accessibility to new heights by enabling the automatic generation of program code from natural language, opening up software development to an even broader audience. However executing the code, testing it and getting it reviewd through source code hosting platforms still requires the knowledge of how to setup a development environment, how to execute Git commands and create a pull request which remains a challenge for beginners. This is where [https://gh.io/copilot-workspace](GitHub Copilot Workspace) comes in, offering a streamlined solution by providing you with AI agents to brainstorm, generate code, create pull requests and obtaining terminal commands to build, test, and execute the software. With AI-native Cloud Development Environments like GitHub Copilot Workspace or [replit](https://replit.com) - to name another one - you don't have to learn how to use a complex IDE anymore, since everything is provided to you in a modern web user interface. 

With GitHub Copilot Workspace you don't even have to write code anymore, you simply start by formulating your feature request or bug for your favourite project in natural language with an GitHub issue. A GitHub issue serves as a specification for Copilot Workspace. You can brainstorm with the AI agents how to solve your issue. It provides you with a summary which helps you to understand the neccessary file changes and you can ask further questions to get a better understanding. It provides you a couple of suggested questions which helps you to discover and learn about the repository and related topics. By gettting a better understanding you can step-by-step add more details to your issue. When you are satisfied with the outcome of you brainstorming session, you can generate a plan for the file changes which you can modify and then let Copilot Workspce implement it.

This gamechanger was firstly [announced](https://www.linkedin.com/posts/ashtom_github-copilot-workspace-welcome-to-the-activity-7190743877035700224-CRtx) by GitHub's CEO Thomas Dohmnke in spring 2024 and was rapidly picked up by influencers like [Jefff Delaney](https://youtu.be/S_RorY_FRvo?si=3VbhcTu-zD3IjC_8) and [Rob Bos](https://youtu.be/N64ozm3x88k?si=OEqysoKwDSo59wJt). Half a year later, my excitement for Generative AI in IT hasn't waned. I've made it a habit to use various Copilots every day in both my professional and personal life to boost productivity and accelerate learning. At the GitHub Universe 2024 opening keynote, the passionate energy from [Orange Dynamite](https://youtu.be/dSf8QOjazrQ?t=1841) 🍊🧨 ignited my enthusiasm to dive right in. Luckily [GitHub Next](https://gh.io/next) opened up the techincal preview for a lot more people and I received access too. In the follwoing I want to share my first experience with it.

The issue I selected for experimentation is [Carz](https://github.com/A5H73Y/Carz/issues/48), which describes a bug for a Minecraft server plugin, I already know from my Minecraft-times.

The first experience was a bit dissapointing, since it ended in the error messege "Unexpected error generating plan: Unexpected end of JSON input". Others already reported the same message in the Discord channel https://discord.com/channels/735557230698692749/1296372448018563145/1296372448018563145 and it got fixed quickly.

The bug describes a szenario, where cars can't clib out of tiny gaps. Opening the bug in the Copilot Workspace leads directly into a brainstorming session, where the current behaviour and the proposed solution is described.

What the AI found out about the current behaviour:

```
The calculateIsClimbable method in src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java determines if the car should start climbing based on the block below and the block ahead.

The current implementation does not account for the tiny gaps in dirt path, soul sand, farmland, and honey blocks, causing the car to get stuck.
```

The block below and the block ahead are things that decide, if the car is able to climb out of tiny gaps. What is a bit confusing, is that concrete blocks are mentioned. This is error prone, since listing the concrete blocks instead of focusing on their common attributes leads to missing out other similar blocks or when new blocks are added in Mincraft updates. It would be good to know what exactly makes them different to other blocks to cover more cases. Anyway, what does the AI proposes as an solution:


```
The calculateIsClimbable method in src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java has been updated to account for these blocks.

The method now checks for the tiny gaps in dirt path, soul sand, farmland, and honey blocks and allows cars to climb out of them.
```

This sounds quite reosonable but as mentioned it feels to be error prone and does not give a good understanding about the climb mechanic which is crucial for finding and debugging edge cases. Therefore I typed in the following question in the brainstorm sections's bottom prompt field: What are the conditions, based on Mincraft's game physics to allow climbing. Please describe it on an abstract level.

```
To allow climbing in the Minecraft game physics, the following conditions must be met:

- The climb mechanic checks the block in front of the Minecart for a valid material.
- If there are no specified climb blocks, all solid blocks are climbable.
- It verifies if there is space above the block to climb to.
- If the conditions are met, the car will elevate up the block.
- If no specific climb blocks are defined, every material is considered climbable.
- The climb strength can be configured, which determines the height the car can climb.

These conditions are defined in the docs/tutorials/block-types.md file.
```

Based on that answer, the block in front of the Minecart and the space above are the conditons which allow the car to climb. But how is the block in front determined?

Entering this question gives the following answer:

```
The block in front is determined in the VehicleListener class (src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java) within the onVehicleUpdate method. Here are the steps involved:

- The player's location is cloned and the pitch is set to 0.
- A new location, twoBlocksAhead, is calculated by adding the player's direction multiplied by 2 to the player's location. The Y-coordinate of twoBlocksAhead is set to the maximum of the player's Y-coordinate plus 1 and the Y-coordinate of twoBlocksAhead.
- The block at twoBlocksAhead is then checked to determine if it is climbable.
```

Why is the player's location cloned and what is the pitch?

```
The player's location is cloned and the pitch is set to 0 in the VehicleListener class in the onVehicleUpdate method. This is done to ensure that the car's movement is calculated based on a level direction, ignoring any vertical tilt the player might have. Here are the details:

- The player's location is cloned to create a new Location object that can be modified without affecting the player's actual location.
- The pitch is set to 0 to ensure that the direction vector used for the car's movement is horizontal, ignoring any vertical tilt the player might have.
- This helps in calculating the car's movement direction and speed accurately, ensuring smooth and predictable car behavior.

The relevant code can be found in the src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java file. The onVehicleUpdate method handles the car's movement updates, including the cloning of the player's location and setting the pitch to 0.
```


What is the code for checking if there is space above?

The code for checking if there is space above is located in the `src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java` file. The relevant code is in the `onVehicleUpdate` method, which calculates if the car should start climbing. The method checks if the block above the car is of type AIR to determine if there is space above.

The `onVehicleUpdate` method is responsible for updating the vehicle's state.
The method calculates if the car should start climbing by checking the block above the car.
If the block above the car is of type AIR, it allows the car to climb.
The specific code snippet is as follows:

Location above = twoBlocksAhead.add(0, 1, 0);

```
// if the block above it is AIR, allow to climb
if (above.getBlock().getType() == AIR) {
    vehicleVelocity.setY(carz.getConfig().getClimbBlockStrength());

    vehicleVelocity.setX(playerLocationVelocity.getX() / 8.0);
    vehicleVelocity.setZ(playerLocationVelocity.getZ() / 8.0);
}
```

This code checks if the block above the car is of type AIR and allows the car to climb if there is space above. The calculateIsClimbable method is also used to determine if the block below is climbable.


...
This can be achived in an easy way by clicking on the "Open commands" icon at the upper right toolbar and by pressing the light bulb for the command you want to execute as depicted below.

images/open_commands.png

The AI will generate the neccessary command which can be executed in the underlying GitHub Codespace, which is automatically initiated by Copilot Workspace with the required build tools. There is no manual setup of a development environment anymore. Everything is accessible from Copilot Workspace through a web browser — even on a smartphone — eliminating the need for a local setup.


