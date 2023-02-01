# UE from Source <!-- omit in toc -->

Building Unreal Engine from source may be a dauting task, but it's not as hard as it seems. Honestly, it requires more patience and attention than anything else. This guide aims to help you through the entire process, from downloading the source code to building the Engine itself.

## Table of Contents

- [Building from source](#building-from-source)
  - [Before we begin](#before-we-begin)
    - [A note on Angelscript](#a-note-on-angelscript)
  - [Step-by-step](#step-by-step)
    - [Accessing the source code](#accessing-the-source-code)
    - [Downloading the source code](#downloading-the-source-code)
    - [Installing the prerequisites](#installing-the-prerequisites)
    - [Enabling Windows Debugging Tools](#enabling-windows-debugging-tools)
    - [Commencing the build!](#commencing-the-build)
    - [Running the Editor](#running-the-editor)
  - [Troubleshooting](#troubleshooting)
- [Creating an Installed Build](#creating-an-installed-build)
  - [What is an Installed Build?](#what-is-an-installed-build)
  - [Before we begin](#before-we-begin-1)
  - [Step-by-step](#step-by-step-1)
    - [Building the Automation Tool](#building-the-automation-tool)
    - [Creating the Install Build script](#creating-the-install-build-script)
    - [Running the script](#running-the-script)
    - [Distributing your fresh Installed Build](#distributing-your-fresh-installed-build)
  - [Troubleshooting](#troubleshooting-1)
- [General references](#general-references)
- [FAQ](#faq)

# Building from source

## Before we begin

Make sure you have the following:

- Approximately 240GB of free space, ideally in an SSD. _This includes Visual Studio and the dependencies._
- At the very least 2h of your time. _Can be way more depending on your hardware and any issues you find along the way._
- **Lots of patience and attention.**

Also, at the time of writing, the procedure below was only tested on **Windows 11** with **Visual Studio 2022** targeting **Win64 Development/Shipping** builds, in the following machine:

> **i9 11900K** Processor
>
> **64 GB** RAM DDR4
>
> **1TB** SSD Samsung QVO 870

Note that most mid-end+ machines will do the trick&mdash;don't worry too much about it. However, the more CPU and RAM you have, the quicker the process will be.

If you're still unsure your machine can handle it, please refer to [this post](https://docs.unrealengine.com/4.27/en-US/Basics/InstallingUnrealEngine/RecommendedSpecifications/).

### A note on Angelscript

If you are aiming to build [Unreal-Angelscript](https://github.com/Hazelight/UnrealEngine-Angelscript), the procedure documented next is _almost_ the same, with the exception of the source code you need to download. But don't worry about it for now&mdash;follow the steps below and the differences will be properly flagged as you go.

If you are unsure as to why you would want to build from source for Angelscript, please refer to the [FAQ](#faq) section.

If you are reading this having nothing to do with Angelscript, then you can safely ignore this section and all the references to Angelscript for the rest of the document&mdash;they are not relevant to you.

## Step-by-step

**Please, make sure you follow all the steps listed below in order, with your greatest attention.**

### Accessing the source code

Regardless whether you're aiming Angelscript or a vanilla Unreal Engine version, you need to have access to the Epic Game's organization on GitHub. In order to get that, please refer to [Epic's official guide](https://www.unrealengine.com/en-US/ue-on-github).

### Downloading the source code

Now that you have access to Unreal Engine's source code, it's time to download it.

1. Create a new folder on your machine, preferably in a not-so-nested path. An ideal place would be something like `C:\UE-Git`. The name does not really matter, just try to keep the _path_ short to avoid issues later.

2. Head to the [Unreal Engine's Repository](https://github.com/EpicGames/UnrealEngine).

👼 _Angelscript people: **instead** of the link above, please use [this one](https://github.com/Hazelight/UnrealEngine-Angelscript)._

3. Click on the green button that says "Code" and then click on "Download ZIP".

![Image](/img/1675215340842.png)

4. Extract the ZIP file to the folder you created in **step 1**.

### Installing the prerequisites

First, if you still don't have Visual Studio 2022, [download it from here](https://visualstudio.microsoft.com/downloads/). For what it's worth, the **Community Edition** was used in this procedure.

During install, through the Visual Studio Installer, you may land at a screen similar to this:

![Image](/img/1675216355570.png)

_If you already have Visual Studio 2022 installed, then just open Visual Studio Installer and click "Modify" on your Visual Studio 2022 entry to get to the window above._

Now, these are the Workloads you want to install (or make sure it's installed):

- **Desktop development with C++**
- **Game development with C++**
- **.NET desktop development**

_If you didn't notice, the "Workloads" I am referring to is the very first tab on the window above._

_You probably don't need each and every of the individual components the Workflows above install, but I didn't have success by nitpicking them despite all my efforts. I was only successful by installing the Workloads above in their entirety. Please, if you have more intel on this, file an issue._

**But wait, there's more!** To increase our chances of success, we're going to install a few more things. Open the `Individual Components` tab, located right next to the `Workloads` tab, and make sure the following are, or will be, installed:

- **Windows 10 SDK (10.0.18362.0)**
- **.NET 6.0 Runtime (Long Term Support)**
- **.NET Framework 4.6.2 targeting pack**

This is important because a common mistake during the build process is that you don't have the right SDKs/versions installed. The versions I'm referring to are the ones mentioned by Epic itself on their [Unreal Engine 5.1 Release Notes](https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/):

![Image](/img/1675224956011.png)

### Enabling Windows Debugging Tools

_I'd like to acknowledge I'm not really sure everybody needs this step because some other tutorials didn't mention it. However, I had to do it myself in order to get the build to work, so I'm including it here just in case._

1. Open your Windows Search bar and search for "Settings" and open it:

![Image](/img/1675217410414.png)

2. Open the "Apps" tab:

![Image](/img/1675217450686.png)

3. Click on "Installed apps":

![Image](/img/1675217476550.png)

4. Search for `10.0.18`. You should see `Windows Software Development Kit - Windows 10.0.18362.1`, like so:

![Image](/img/1675217549025.png)

_If you are not seeing this app, make sure you installed the `Windows 10 SDK (10.0.18362.0)` as described by the [Installing the prerequisites](#installing-the-prerequisites) step._

1. Click on the elipsis (...) and then click on "Modify". This window may show up:

![Image](/img/1675217600279.png)

6. Mark the "Change" option and then click on "Next". In the window that just showed up, mark the `Debugging Tools for Windows` checkbox and click on "Change":

![Image](/img/1675217674311.png)

### Commencing the build!

_This step is required for both Angelscript and/or vanilla Unreal Engine._

At this point, you should have everything you need to build from source. Let's get to it!

1. Open the folder you unzipped the source code to. You should see a file called `Setup.bat`. Double click on it to run it. _This may take a while, so be patient. It usually takes 10-20 minutes to me._
2. Once the `Setup.bat` finishes, you should see a new file called `GenerateProjectFiles.bat`. Double click on it to run it. _This should be quick._
3. Now open the `UE5.sln` file that was just created. This is the Visual Studio solution file for Unreal Engine. _It's in the same folder as the `GenerateProjectFiles.bat` file._
4. Now that you're on Visual Studio, you need to select the `Development Editor` configuration. You can do that by clicking on the dropdown below the `Build` and `Debug` menus at the very top of the window:

![Image](/img/1675218131117.png)

5. You also need to select the `Win64` platform. You can do that by clicking on the dropdown right next to the `Development Editor` configuration:

![Image](/img/1675218176340.png)

6. You're ready to build! At your right sidebar, you should see a `Solution Explorer` panel. Uncollapse the `Engine` folder and you should see the `UE5` project:

![Image](/img/1675218316035.png)

7. Right-click on it and select `Build`.

8. Now it's tea time! This process will take a while, so be patient and go grab a cup of tea. It may take anywhere from 1:30 to 3 hours or even more, depending on your machine.

### Running the Editor

1. Phew! You made it! Let's first certify everything worked as expected. At this point, your Visual Studio should be displaying something like this in the `Output` panel:

![Image](/img/1675221317749.png)

2.  Before you run the editor, right click on the `UE5` project and select `Set as Startup Project`:

![Image](/img/1675218655214.png)

3. To finally run your juicy new editor, right click again on the `UE5` project and select `Debug` > `Start new instance`. This should open the Unreal Engine 5.1 Splash Screen.

Note that the very first time you run the editor, it will take a little while to load. This is normal. It is indexing stuff, compiling shaders, etc, so be patient&mdash;the next time you run it, it should be faster.

## Troubleshooting

If you're having any kind of trouble with the build process:

1. [Make sure you have enough space.](#before-we-begin)
2. [Make sure your computer meets the minimum requirements.](https://docs.unrealengine.com/4.27/en-US/Basics/InstallingUnrealEngine/RecommendedSpecifications/)
3. [Make sure you have the right versions of the prerequisites installed.](#installing-the-prerequisites)
4. [Make sure you granted the right permissions to the folder you are compiling to.](https://learn.microsoft.com/en-us/answers/questions/696965/windows-11-folder-read-write-permissions-dont-work)
5. Make sure you are building a pristine copy of the source code. Don't try making changes to the Engine prior to the building process.
6. Avoid long paths. Try to keep the path to the source code folder as short as possible. For example, if you are compiling to `C:\Users\YourName\Documents\UnrealEngine`, try compiling to `C:\UnrealEngine` instead.
7. _If you are cloning the source code via Git,_ make sure you are doing it from the [`release` branch](https://github.com/EpicGames/UnrealEngine/tree/release). Also, it's advised to clone using `--depth 1` to avoid downloading the entire history of the repository and save some time and space.

If none of the above works, please open an issue on this repository and I'll try to help you out. If you're seeking a swifter solution, you can also try asking for help on the [Unreal Slackers Discord Server](https://unrealslackers.org/) via the `#engine-source` channel.

**Ultra important: try to be as specific as possible when describing your problem.** Save the time of people willing to help you, and they'll save you the time of having to wait for a response. **_Also, be patient._**

# Creating an Installed Build

Alright. You've built the engine from source. It was a long and arduous journey, but you made it. Now, you want to start creating your game on the top of it but you realised that Billy, your teammate, also needs the same engine you now have.

Then you also realised that the weight of this engine of yours is 180GB+ and you have no clue how to send it to Billy.

Now what?

Well, you have plenty of options.

- You can literally zip your 180GB+ and send it over.
- You can ask Billy to build the engine from source himself, but hey, Billy doesn't even remember what Visual Studio is.
- **Or you can create an Installed Build!**

## What is an Installed Build?

The quickest example of an Installed Build is the retail version of Unreal Engine&mdash;the one you download from the Epic Games' Launcher.

Installed Builds are compiled versions of the Engine that can be distributed to other people out-of-the-box. They are smaller than the source code version and mostly only requires you to zip it and send it about.

_Note to AngelScript people: this guide also applies to you as is!_

## Before we begin

Ok, so you want to create an Installed Build. That's great! But before you do that, you need to know a few things:

- You need to comply with all the things described by the [Building from Source](#building-from-source) section, as well as having the engine built from source successfully.
- You'll need ≈100GB of (extra) free space, ideally in an SSD. _Yes, all that space to build the engine from source plus ≈100GB._
- You'll need **lots** of patience. In my experience, this process takes at least twice as much time as building the engine from source. It's a very slow process, so keep calm. _Note that the time may vary depending on your hardware._]
- If you have the luxury of having yet another extra ≈180GB lyring around, I'd recommend you backing up your source code folder before venturing into Installed Builds. The first time I generated the Installed Build, it failed silently and messed up my source code folder. In short, I had to start the process of building from source all over again. I was definitely not happy, specially after waiting for more or less 4 hours to finish the Install Build process only to have it double fail: one for the Install Build itself and another for the engine build.

**_Most of the credits here go to Joe, from Asset Pack Games, who published [an excellent video](https://www.youtube.com/watch?v=YX85Z57_T0k) on how to create an Installed Build. I've just tested it myself with the latest version of the Engine (5.1 at the time of writing), tweaked a few things and, of course, ported to a text-based guide._**

## Step-by-step

### Building the Automation Tool

1. Open `UE5.sln` in Visual Studio.
2. At the right sidebar, you should see a `Solution Explorer` panel. Uncollapse the `Programs` folder and locate the `AutomationTool` project:

![Image](/img/1675232310023.png)

3. Right-click on it and select `Build`. _It should be quick._

### Creating the Install Build script

1. Open the folder where your engine is located. If you followed the steps in the [Building from Source](#building-from-source) section, it should be located at `C:\UE-Git`.
2. Create a new file called `GenerateInstalledBuild.bat` and paste the following code into it:

```batch
echo off

:: The actual script, which will generate the Installed Build using the Automation Tool and the BuildGraph.
cd Engine\Binaries\DotNET\AutomationTool AutomationTool.exe BuildGraph -target="Make Installed Build Win64" -script=Engine/Build/InstalledEngineBuild.xml -set:HostPlatformOnly=true

echo.

:: Just a friendly reminder to the user that the process is done.
echo. Install Build Generate Script complete

:: The `pause` below prevents the window from closing immediately after the script finishes running.
pause
```

_Important: make sure this file is sitting at the same level as the `Engine` folder, at the same level of the `UE5.sln` or the `GenerateProjectFiles.bat` files._

This script is really important. It's the script that will actually generate the Installed Build. Let's zoom in in the most important part of it:

```batch
cd Engine\Binaries\DotNET\AutomationTool AutomationTool.exe BuildGraph -target="Make Installed Build Win64" -script=Engine/Build/InstalledEngineBuild.xml -set:HostPlatformOnly=true
```

Now, breaking it down:

- `cd Engine\Binaries\DotNET\AutomationTool` changes the current directory to the Automation Tool's folder.
- `AutomationTool.exe` is the actual Automation Tool executable.
- `BuildGraph` is the command to generate the Installed Build itself.
- `-target="Make Installed Build Win64"` specifies the target the BuildGraph will run against. In this case, `Win64` (it could be `Mac`, for example).
- `-script=Engine/Build/InstalledEngineBuild.xml` is the path to the file that describes all the default parameters BuildGraph will be using to generate the Installed Build.
- `-set:HostPlatformOnly=true` is a flag that will tell the BuildGraph to build only for the current platform. If you're in a 64-bit Windows machine, it will only build for 64-bit Windows.

All these options are officially covered by the [Using an Installed Build](https://docs.unrealengine.com/5.0/en-US/using-an-installed-build-of-unreal-engine/) documentation. It's quite outdated, but still a good reference. I recommend you to read it.

_If you want to learn more about the most up-to-date parameters, it would be best if you check the `Engine/Build/InstalledEngineBuild.xml` file where most parameters are described._

To translate this into a more human-readable version, the script above is saying:

> Hey, Automation Tool! I want you to generate an Installed Build for the Win64 platform, using the defaults specified by `Engine/Build/InstalledEngineBuild.xml` with the exception that I want to target only my current platform (which is also Win64, hehe.).

All of the above to say: **customise this script as much as you like to fit your needs.**

3. Save the file and close it.

### Running the script

Fasten your belt because it's show time.

1. Run the `GenerateInstalledBuild.bat` script you just created.
2. Wait.
3. Wait.
4. Wait.
5. & wait.

This process is _really_ long. Just to give you a glimpse, I tried it a few times with different parameters and it took me:

- 1st run: ≈4:30h
- 2nd run: ≈2:59h (had a few things cached from the prev run)
- 3rd run: ≈6:53h (clean cache, different parameters from the last two runs)

If everything went well, you should see a `LocalBuilds` folder at the same level as the `Engine` folder as well as no errors in the console.

### Distributing your fresh Installed Build

Now that you have your Installed Build, you can distribute it out and about. And there's nothing magical here, really. You just zip the `LocalBuilds/Engine` folder and you should be good to go.

## Troubleshooting

1. [Make sure you went through the Building from Source section](#building-from-source).
2. [Make sure you went through the Troubleshooting from the Building from Source section](#troubleshooting).
3. It's very possible that it will cost you a lot of time until an error from the InstallBuild script pops up. One way to mitigate that is by forcing an expected error to happen. For example, if you're building for `Win64`, you can force the script to fail by changing the `-target` parameter to `-target="Make Installed Build Mac"`. This way, you'll know if the script is working or not much quicker than waiting for the whole process to finish.

# General references

- [Unreal Slackers Discord Server](https://unrealslackers.org/)
- [Unreal Engine Recommended Specifications](https://docs.unrealengine.com/4.27/en-US/Basics/InstallingUnrealEngine/RecommendedSpecifications/)
- [Using an Installed Build](https://docs.unrealengine.com/5.0/en-US/using-an-installed-build-of-unreal-engine/)
- [UE5 Tutorial - Creating a Custom Install Build](https://www.youtube.com/watch?v=YX85Z57_T0k)

# FAQ

<details>
<summary><strong>What "building from source" means?</strong></summary>
<p>

In a nutshell, there are two versions of the engine: the retail one, which is that one you get straight from Epic Games' Launcher; and there's the "built from source" version, which is more robust and allows you to actually make changes to the Engine itself.

With that aside, building from source means getting the actual code that runs Unreal Engine and bring it to your machine so you can have your "own Unreal Engine" version.

If you want to dive deeper into a more technical explanation of it, please [refer to this StackOverflow post](https://softwareengineering.stackexchange.com/questions/301245/what-does-it-mean-to-build-from-source).

</p>
</details>

<details>
<summary><strong>Should I bother building from source?</strong></summary>
<p>

Here's a quick list with some of the most common reasons why you want to build Unreal Engine from the source:

- If you're building a game with dedicated servers,
- If you want to make changes to the Engine itself,
- If you want to contribute to the Engine's source code by fixing bugs and/or introducing new features,
- If you are up to Angelscript (if you want to use it with Unreal Engine 5.1)

</p>
</details>

<details>
<summary><strong>Why is UE from source way more weighty than the retail one?</strong></summary>
<p>

The retail version of Unreal Engine is a pre-built version of the engine. It is a binary that is ready to be used. This means that it is already compiled and optimized for your machine. It is also stripped of all the source code and other files that are not necessary for the engine to run, such as debugging symbols, documentation, etc.

In case it's still unclear, imagine that you're buying a car. You can either buy a car that is already built and ready to be used, or you can buy a car that is still in pieces and you need to assemble it yourself: you'd need all the parts, the machinery, the tools, the knowledge, etc. The latter is the "built from source" version of the car, whereas the former is the retail version.

</p>
</details>

<details>
<summary><strong>Why it all takes so long to compile/build?</strong></summary>
<p>

Unreal Engine is a very complex piece of software. It is not just a game engine, it is a whole ecosystem of tools, libraries, and frameworks that are all interconnected. It also has a very large codebase, with millions of lines of code.

By compiling/building it for the first time, it's expected that it takes a while because nothing is cached yet. The next time you compile/build it though, it should be faster.

</p>
</details>

<details>
<summary><strong>Why should I avoid long path names?</strong></summary>
<p>

This is a limitation of Windows. You can read more about it [here](https://docs.microsoft.com/en-us/windows/win32/fileio/naming-a-file#maximum-path-length-limitation).

However, if you are on **Windows 10 1607 or later**, you can enable long paths by following [this guide](https://docs.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=cmd#enable-long-paths-in-windows-10-version-1607-and-later).

</p>
</details>

<details>
<summary><strong>Will I lose progress if I stop building during the process?</strong></summary>
<p>

No. The build process is incremental, which means that it will only build the parts that have changed since the last build.

In other words, if you want to stop the process to continue later, you can do so without losing any progress unless you delete the intermediate files (located at `Engine/Intermediate`).

</p>
</details>

<details>
<summary><strong>Where to put my C++ code?</strong></summary>
<p>

UE built from source is no different from the retail version in this regard. You can put your C++ code in the `Source` folder of your project.

</p>
</details>

<details>
<summary><strong>What project should I build?</strong></summary>
<p>

Only if you are changing Engine code is that you should build the `UE5` project. If you are only developing your game, you should build your game project instead.

</p>
</details>

<details>
<summary>
<strong>Where should I put my plugins in my built from source Engine?</strong>
</summary>
<p>

By default, when installing plugins via the Epic Games Launcher, they are installed in the `Engine/Plugins` folder of your retail Engine installation (usually at `C:\Program Files\Epic Games\UE_5.1\Engine\Plugins`).

I personally don't like this approach. I prefer to keep my plugins inside my project folder, so they are always versioned with it.

That said, you can choose to install your plugins in the `Engine/Plugins` folder of your built from source Engine installation, _however_ I highly recommend you to create a `Plugins` folder inside your project folder and install your plugins there instead.

</p>
</details>

<details>
<summary>
<strong>From what branch should I clone the Unreal Engine source from?</strong>
</summary>
<p>

The safest branch is the [`release`](https://github.com/EpicGames/UnrealEngine/tree/release) one. Also, do it using the `--depth=1` flag, as it will make the cloning process quite faster, like so:

```bash
git clone git@github.com:EpicGames/UnrealEngine.git --depth=1
```

_This does not apply to `Unreal-Angelscript`. In that case, you'd be using `angelscript-master`._

</p>
</details>

<details>
<summary>
<strong>Can I build from source using Rider?</strong>
</summary>
<p>

Yep. You can use Rider to build from source. However, you'd still need the steps described by this document and Visual Studio itself.

To build Unreal Engine using Rider, you'd need to open the `UE5.sln` file in it and then build from there.

I tried building in both Visual Studio and Rider and gotta say that I felt no difference in terms of performance. The only difference I noticed is that in a specific situation, Rider gave me a better error message when one of my builds failed, although I can't confirm if that was only a one-time thing.

</p>
</details>

<details>
<summary>
<strong>Can I delete the "Intermediate" folder?</strong>
</summary>
<p>

This is a tricky question.

Technically speaking, _of course_. However...

Both your project and the Engine have an "Intermediate" folder each. If you delete the one from your project, you'll need to recompile your project. If you delete the one from the Engine, you'll need to recompile the Engine.

Doing it for your project is not that of a big deal depending on the size of it, but doing it for the Engine is not for the faint. At this point, you may know that compiling the Engine is a very exhaustive process.

The Intermediate folder is a cache of sorts. It contains all the intermediate files that are generated during the build process. If you delete it, you'll lose your "build cache", hence you'll need to recompile everything from scratch as if you were building it for the first time.

In short, if you are not sure if you should delete it, don't.

</p>
</details>

<details>
<summary>
<strong>What is "Derived Data Cache"?</strong>
</summary>
<p>

I'm not the best person to explain this because I don't fully understand it myself. What I know thus far is that one of its roles is to cache shaders.

If you delete Derived Data Cache, or DDC for short, you'll need to recompile all your shaders, which you may be already aware is a quite time-consuming process.

You can read more about it [here](https://docs.unrealengine.com/4.27/en-US/ProductionPipelines/DerivedDataCache/) and [here](https://dev.epicgames.com/community/learning/tutorials/bL60/unreal-engine-derived-data-caches-ddcs).

</p>
</details>

<details>
<summary><strong>What is Unreal Angelscript?</strong></summary>

According to their [own website](https://angelscript.hazelight.se/):

> UnrealEngine-Angelscript is a set of engine modifications and a plugin for UE5 that integrates a full-featured scripting language. It is actively developed by [Hazelight](https://www.hazelight.com/), creators of [It Takes Two](https://www.it-takestwo.com/), which was shipped with the majority of its gameplay written in angelscript. The unreal plugin that integrates angelscript is open source, and has received contributions from several studios in Stockholm and globally.

In my opinion, Angelscript makes programming in Unreal Engine _funnier_ than ever.

- Most of the changes you make to your Angelscript code are _properly_ hot-reloaded, meaning almost instantenous feedback on the changes you write. (Please, don't confuse this with the doomed Hot Reload feature for C++ [that was replaced by Live Coding in UE5](https://forums.unrealengine.com/t/ue5-breaking-and-noteworthy-changes/264964))
- From the scripting aspect, you benefit from code diffing&mdash;hardly achievable with Blueprints.
- You mostly no longer have to deal with the verbosity and wackiness of C++, making programming more accessible and enjoyable to the average/beginner programmer.
- _Have I mentioned most of the changes you make don't even require you to leave [PIE](https://docs.unrealengine.com/5.0/en-US/play-in-editor-settings-in-unreal-engine/)?_

Ultimately I'd like to note that Angelscript **does not** replace Blueprints or C++ by any means. Instead, it's a complementary tool that can be used to speed up the development process and make it more enjoyable. _C++ is still the de-facto "the sky is the limit" language, as well as the go-to place for performance-critical code._

_A big thank you to [Hazelight](https://www.hazelight.com/) for not only making it possible, but also for sharing it all with the community. Also, thank you Eren for presenting it to me!_

</details>

<details>
<summary><strong>What does Unreal Angelscript have to do with building from source?</strong></summary>
<p>

1. Hazelight's Unreal Engine Angelscript is not just a plugin. It's a fork of the Engine itself with custom modifications, needed to make it work with Angelscript. As such, you need their custom Unreal Engine version if you want to use Angelscript.

2. As the time of writing this, there's no binary version for Unreal Engine Angelscript, meaning you have to build it from source yourself. _If you're okay using 5.0.2, you can skip all of this and download the binary version from [here](https://github.com/Hazelight/UnrealEngine-Angelscript/releases/tag/v5.0.2-angelscript)._

</p>
</details>

<details>
<summary><strong>My Installed Build is still immense! Why?</strong></summary>
<p>

Unfortunately I don't have a good answer for this.

I tried my best to make the Installed Build as small as possible myself, but it's still big for me nonetheless&mdash;reaching no less than 100GB.

What I can say though is that the more platforms you target to build to, the bigger the Installed Build will be. For example, if you target Windows, Linux, and Mac, your Installed Build will be bigger than if you only target Windows.

However, some benefits still:

1. It's still smaller than the size of the engine built from source.
2. Nobody has to build the engine themselves, which is a huge time-saver.
3. By zipping the Installed Build, you can at least make the download even smaller and more accessible.

At the end of the day, your teammates will thank you for it.

</p>
</details>

<details>
<summary><strong>Should I create a new Installed Build if I change anything at the Engine level?</strong></summary>
<p>

Yes.

If you are constantly changing things at the Engine level, I'd say perhaps Installed Builds are not for you. Instead, you should consider asking your teammates to build the Engine from source themselves.

</p>
</details>

<details>
<summary><strong>Do I need Client/Server when generating an Installed Build?</strong></summary>
<p>

It depends.

Yes if you want to build a dedicated server. No if you don't.

</p>
</details>

<details>
<summary><strong>Do I need DDC when generating an Installed Build?</strong></summary>
<p>

I don't know. I mean, I tried with and without DDC and as far as I can tell, I didn't notice any difference.

In fact, the longest time I spent generating an Installed Build was when I was building _without_ DDC&mdash;which took me about 7 hours. However, I can't remember all the parameters I used at that run, so I can't say for sure whether it was because of the lack of DDC or not.

</p>
</details>

<details>
<summary><strong>What about Unreal Binary Builder?</strong></summary>
<p>

I suppose you may be referring to [this](https://github.com/ryanjon2040/Unreal-Binary-Builder) project, right?

I've tried it myself, but it seems it lacks support for Unreal Engine 5.1 and requires Visual Studio 2019. Other than that, it seemed to be a lovely tool that would ease the entire process of building Unreal Engine from source and creating an Installed Build.

PRO TIP: It's licensed under [MIT](https://github.com/ryanjon2040/Unreal-Binary-Builder/blob/master/LICENSE.md), so if you know C# and are willing to support it, go ahead and do so&mdash;the community would definitely appreciate it!

</p>
</details>

<details>
<summary><strong>Why yet another guide?</strong></summary>
<p>

Glad you asked!

1. I could not find a guide that was up-to-date with the latest Unreal Engine 5.1 release. Most of the guides I found were outdated and/or didn't work for me.
2. People, including myself, keep asking the same questions over and over when it comes to building Unreal Engine from source. I wanted to created not only a step-by-step guide, but also a central place where people can find answers to their questions when it comes to building Unreal Engine from source.
3. I wanted to create an open-source material that people can contribute to, so we can keep it up-to-date and make it always better.

</p>
</details>
