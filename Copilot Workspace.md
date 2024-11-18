**My First Experience with GitHub Copilot Workspace**

**Why do we need such a thing?**

*TL;DR* - To make software development more accessible by relentlessly breaking down barriers with the assistance of AI agents.

Over the last decades, barriers to software development have significantly diminished, thanks to advancements like higher-level programming languages and powerful development tools. Modern Integrated Development Environments (IDEs) come equipped with features such as auto-completion, syntax highlighting, code suggestions, linters, and many more. These allow developers to focus on problem-solving rather than struggling with intricate language details.

The arrival of Generative AI has taken this accessibility to new heights by enabling the automatic generation of program code from natural language, opening up software development to an even broader audience. However, executing the code, testing it, and getting it reviewed through source code hosting platforms still require knowledge of how to set up a development environment, how to execute Git commands, and create a pull request, which remains a challenge for beginners. This is where [GitHub Copilot Workspace](https://gh.io/copilot-workspace) comes in, offering a streamlined solution by providing you with AI agents to brainstorm, generate code, create pull requests, and obtain terminal commands to build, test, and execute the software. With AI-native Cloud Development Environments like GitHub Copilot Workspace or [Replit](https://replit.com) — to name another one — you don't have to learn how to use a complex IDE anymore, since everything is provided to you in a modern web user interface.

With GitHub Copilot Workspace, you don't even have to write code anymore; you simply start by formulating your feature request or bug for your favorite project in natural language with a GitHub issue. A GitHub issue serves as a specification for Copilot Workspace. You can brainstorm with the AI agents on how to solve your issue. It provides you with a summary which helps you understand the necessary file changes, and you can ask further questions to get a better understanding. It provides you with a couple of suggested questions which help you discover and learn about the repository and related topics. By getting a better understanding, you can step-by-step add more details to your issue. When you are satisfied with the outcome of your brainstorming session, you can generate a plan for the file changes which you can modify and then let Copilot Workspace implement it.

This game-changer was first [announced](https://www.linkedin.com/posts/ashtom_github-copilot-workspace-welcome-to-the-activity-7190743877035700224-CRtx) by GitHub's CEO Thomas Dohmke in spring 2024 and was rapidly picked up by influencers like [Jeff Delaney](https://youtu.be/S_RorY_FRvo?si=3VbhcTu-zD3IjC_8) and [Rob Bos](https://youtu.be/N64ozm3x88k?si=OEqysoKwDSo59wJt). Half a year later, my excitement for Generative AI in IT hasn't waned. I've made it a habit to use various Copilots every day in both my professional and personal life to boost productivity and accelerate learning. At the GitHub Universe 2024 opening keynote, the passionate energy from [Orange Dynamite](https://youtu.be/dSf8QOjazrQ?t=1841) 🍊🧨 ignited my enthusiasm to dive right in. Luckily, [GitHub Next](https://githubnext.com) opened up the technical preview for a lot more people, and I received access too. In the following, I want to share my first experience with it.

The issue I selected for experimentation is [Carz #48](https://github.com/A5H73Y/Carz/issues/48), which describes a bug for a Minecraft server plugin I already know from my Minecraft times. The first experience was a bit disappointing, since it ended in the error message "Unexpected error generating plan: Unexpected end of JSON input". Others already reported the same message in the [Discord channel](https://discord.com/channels/735557230698692749/1296372448018563145/1296372448018563145), and it got fixed quickly.

The Minecraft bug describes a scenario where cars can't climb out of tiny gaps. Opening the bug in the Copilot Workspace leads directly into a brainstorming session, where the current behavior and the proposed solution are described.

**What the AI found out about the current behavior:**

```
- No, cars cannot climb out of the tiny gaps in dirt path, soul sand, farmland, and honey blocks.
- The `src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java` file handles the vehicle update event and checks if the car should start climbing.
- The `calculateIsClimbable` method in `src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java` determines if the car should start climbing based on the block below and the block ahead.
- The current implementation does not account for the tiny gaps in dirt path, soul sand, farmland, and honey blocks, causing the car to get stuck.
```

The block below and the block ahead are factors that decide if the car is able to climb out of tiny gaps. What's a bit confusing is that concrete blocks are mentioned. This is error-prone since listing the concrete blocks instead of focusing on their common attributes leads to missing out on other similar blocks or potential new blocks in Minecraft updates. It would be good to know what exactly makes them different from other blocks to cover more cases. Anyway, what does the AI propose as a solution?

**AI's Proposed Solution:**

```
- The `src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java` file handles the vehicle update event and checks if the car should start climbing.
- The `calculateIsClimbable` method in `src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java` has been updated to account for these blocks.
- The method now checks for the tiny gaps in dirt path, soul sand, farmland, and honey blocks and allows cars to climb out of them.
```

While the AI suggests updating the method to include these blocks, the solution appears somewhat limited, as it lacks detail on how to handle the climbing mechanics generically, which is essential for future-proofing against new block types. To delve deeper, I asked the AI to explain the issue description.

**Please explain the issue description**

```
The issue description explains a problem where cars in the game cannot climb out of small gaps in certain block types. Here are the key points:

- When a car enters an area of dirt path, soul sand, farmland, or honey blocks, it gets stuck against a full block instead of climbing the small height difference.
- The height difference for dirt path and farmland is 0.0625 blocks, and for soul sand, it is 0.125 blocks.
- The car behaves as if it is hitting a wall and cannot move forward.

This issue affects the gameplay by preventing cars from moving smoothly over these block types. The expected behavior is for the car to climb the small height difference and continue moving. The problem likely lies in the code that handles car movement and block interactions.
```

The AI provided a concise explanation of the issue, highlighting the specific block types and the nature of the problem, that helped in understanding the root cause which seems to be the missing differentiation between full blocks and partial blocks. So the next question asked about the differences between these.

**What are the different dimensions of full and partial blocks?** 

```
The different dimensions of full blocks and partial blocks in the context of the Carz plugin are as follows:

Full blocks: These blocks have a height of 1.0. Examples include grass blocks, stone blocks, and other solid blocks. 🧱
Partial blocks: These blocks have a height less than 1.0. Examples include:
Dirt path: 0.0625 🛤️
Soul sand: 0.125 🏜️
Farmland: 0.0625 🌾
Honey blocks: 0.0625 🍯
```

In case you wonder, the emojis are from the experimental 'Use emoji in topic and explorations' feature, which you can enable in the top toolbar. The AI clarified the distinction between full and partial blocks by their height but made some mistakes for partial blocks.

The correct values are (source: https://github.com/PrismarineJS/minecraft-data/blob/master/data/pc/1.15/blockCollisionShapes.json):

```
Soul sand: 0.875
Dirt path: 0.9375
Farmland: 0.9375
Honey blocks: 0.9375
```

To develop a more future-proof solution, I asked following:

**How can partial blocks be handled in a generic way?**


Now it get's really interesting since, the brainstorming agent suggests three different approaches:

1. *Use block height mapping*

```
Create a mapping of block types to their respective heights. This can be done in a configuration file like 
src/main/java/io/github/a5h73y/carz/configuration/impl/BlocksConfig.java
 📏
Use this mapping to determine the height of a block when checking if a car can climb it. This can be implemented in the getBlockHeight method in 
src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java
 🏞️
Update the calculateIsClimbable method in 
src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java
 to use the block height mapping when determining if a block is climbable 🧗
```

2. *Implement a block type interface*

```
Create an interface for block types that includes methods for getting the block height and checking if the block is climbable. This can be done in a new file like src/main/java/io/github/a5h73y/carz/model/BlockType.java (ADD) 🛠️
Implement this interface for each block type, including partial blocks like dirt path and soul sand. This can be done in files like src/main/java/io/github/a5h73y/carz/model/DirtPathBlock.java (ADD) and src/main/java/io/github/a5h73y/carz/model/SoulSandBlock.java (ADD) 🌱
Update the calculateIsClimbable method in 
src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java
 to use the block type interface when determining if a block is climbable 🧩
```

3. *Use block data attributes*

```
Utilize block data attributes to store information about block heights and climbability. This can be done using the BlockData class in
src/main/java/io/github/a5h73y/carz/utility/PluginUtils.java
 🗃️
Update the getBlockHeight method in
src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java
 to use block data attributes when determining the height of a block 📊
Update the calculateIsClimbable method in
src/main/java/io/github/a5h73y/carz/listeners/VehicleListener.java
 to use block data attributes when checking if a block is climbable 🔍
```

Since I am looking for a dynamic solution without static block type checks, the third approach using the block data attributes stored in NBT ([Named Binary Tag](https://minecraft.fandom.com/wiki/NBT_format)) seems the most promising. Unfortunatelly the bounding box which is used for collision detection is not stored in NBT.

After undersanding more about collission detection and block mechanics in Minecraft by trial and error, I finally found the [Bukkit API](https://hub.spigotmc.org/stash/projects/SPIGOT/repos/bukkit/commits/d0d6ee2829d1d4000685dee2c56f88278571bbc8#src%2Fmain%2Fjava%2Forg%2Fbukkit%2FWorld.java?t=581) which guided me to ask the right instructions into the prompt:

- Use ray tracing (only for server versions >= v1.14; `PluginUtils.getMinorServerVersion()` >= 14) to calculate the fractional Y coordinate of the block below, then subtract it from 1 to determine the height of partial blocks as an alternative to the block type-based approach in `getBlockHeight`.
- Update the `twoBlocksAhead` location's Y coordinate in `onVehicleUpdate` method to use the height of the block below the vehicle.

Here you can see the solution which Copilot Workspace provided and it workes out of the box: https://copilot-workspace.githubnext.com/A5H73Y/Carz/issues/48?shareId=cb21d665-2e19-49b1-a179-4e98b04049d4

When you open the link, you can build the Jar by clicking on the "Open commands" icon at the upper right toolbar and pressing the light bulb for the command you want to execute, as depicted below.

![Open Commands](images/open_commands.png)

*The AI will generate the necessary command which can be executed in the underlying GitHub Codespace, automatically initiated by Copilot Workspace with the required build tools. There is no manual setup of a development environment anymore. Everything is accessible from Copilot Workspace through a web browser—even on a smartphone—eliminating the need for a local setup.*

**Tips:**

- *Crafting clear requirements or issue descriptions — including considerations of the "ilities" — is essential. This helps the AI better understand your needs and provides more accurate solutions by addressing all crucial aspects of software quality in its responses.*
- *Try out variants of questions by adding phrases like "explain it in detail," "explain it by leaving out implementation details," or "explain it to me like I'm 10."*
- *Entering the same question does not always lead to the same response. Sometimes it gives better results when starting a new session and entering it again. When you're stuck, starting from scratch can help.* 😉
- *When you have further questions about a response from a brainstorming session, copy and paste the response into the prompt and append your question to it.*
- *You can paste in code snippets or pseudocode which demonstrate an approach to your brainstorming prompt to guide you on how to integrate it.*
- *The API usage proposals from tha AI are lacking version information. In my case I had to specify clearly to use the ray tracing API only for minor versions greater or equal to 14.*

---

My first experience with GitHub Copilot Workspace was enlightening, offering a glimpse into the future of AI-native software development. It demonstrated how AI and human creativity can collaborate to tackle complex challenges. While the AI agents provided valuable insights and practical solutions, the initial proposal for my selected issue wasn't entirely robust against potential new blocks in Minecraft updates. Still, I'm excited to see how this technology evolves, empowering not just professional developers but anyone curious about software to achieve extraordinary things.
