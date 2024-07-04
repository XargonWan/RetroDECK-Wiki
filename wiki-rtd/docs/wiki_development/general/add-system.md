# Add a new system to RetroDECK

This is a WIP document to show how the Ruffle emulator was added RetroDeck. From initial testing of the emulator, to integrating Ruffle into Flatpack build process and then how to integrate Ruffle into RetroDeck Configuration system for saving game data, resetting/moving config etc.

## Overview

Article assumes that the defaults path for RetroDECK are

That you have looked at [the build local](build-locally.md){:target="_blank"}  document as this continues from that introduction.

## Preparation

How well do you know the emulator you are looking to add to RetroDECK. 

 * Can it run fullscreen
 * Does it save config files
 * Does it need a default config file
 * What libraries and assets does it need.
 * Does it need bios files
 * Controller support
 * Command line arguments for redirecting configs or saves etc

## Tasks Involved

### Clone RetroDeck

- Fork and clone the main [RetroDeck project.](https://github.com/XargonWan/RetroDECK/fork){:target="_blank"} 
- Adding an emulator would be classed as a **new feature** so create a branch based of the label feat, ie: `feat/new_emulator_name`
- An example for **ruffle** can be seen [here](https://github.com/monkeyx-net/RetroDECK_UK/tree/feat/ruffle){:target="_blank"} 
- Initial testing can be done via the [debug mode](/wiki_development/general/debug-mode/#retrodeck-in-debug-mode){:target="_blank"} 
- You will be able to manually run via emulator and test functionality
- To get the emulator added to the manifest an example of ruffle is shown below as simple example manifest

### Editing the manifest file 

```bash linenums="1"
 
# Ruffle - START

  - name: ruffle
    buildsystem: simple
    build-commands:
      - cp -p ruffle "${FLATPAK_DEST}/bin/"
      - chmod +x "${FLATPAK_DEST}/bin/ruffle"
    sources:
      - type: archive
        strip-components: 0
        url: https://github.com/ruffle-rs/ruffle/releases/download/nightly-2024-07-02/ruffle-nightly-2024_07_02-linux-x86_64.tar.gz
        sha256: a410ce8956723a0043d1744ef9b519debc978faa2b5868953acc548e44586d15

# Ruffle - END
 
```

 - Please note lines 1 and 14 as remarks/labels having these help with logging and fund the emulator within the file.
 - Line 3 is a label
 - Lines 4 indicates a simple build type, the simple build type is just executing the given `build-commands`, ie downloading and copying files rather than compiling and building the project. The preference is to compile and build emulators to  support the Flatpak process in producing increased compatibly and customisation. Ruffle is a rust project and under constant development. It is hoped to make this a compiled project in the future. 
 - A simple project is also a good starting point to Flatpak builds.
 - Lines 5 to 7 shows the build process kinked to the downloaded ruffle file.
 - Lines 10-12 are defining the sources:
   - type: `archive`, this means that the archive is download and extracted without any explicit manifest action, `file` type source instead would just download the file without extracting it. 
   - Please note the strip-component: 0 that was required as ruffle has no folder structure as part of the archive . See the [flatpak-builder-command-reference.html](https://docs.flatpak.org/en/latest/flatpak-builder-command-reference.html){:target="_blank"} for more.
   - url: obviously the url to download
   - sha256: the sha256sum of the file, this is mandatory

> **NOTE:** In some rare cases we might don't know the sha (or even the url) in advance, for example when an emulator is having only one release on GitHub that is being kept updated without a release history.
[This guide](automate-emulator-with-placeholders.md){:target="_blank"}  is explaining how to use the placeholders instead of urls or sha in case the this data cannot be foreseen.
Fort his purpose we have implemented an automation to get that data from the repository itself to being used only as a final resource as we prefer to control the version of each module for proper troubleshooting and version control.

### Build the RetroDECK Flatpak

If you have added the necessary files to the manifest

Move you new yml code to just after key dependencies in the manifest. So that you do not have to wait until near the end of the build to check your manifest actions.

Not even ES-DE should be needed to test the additional emulator. In fact it will not show up in ES-DE. See [here for more.](#add-emulatorsystem-to-es-de)

Build using  [self hosted runner](build-locally/#build-locally-installed-github-runner){:target="_blank"}  is the recommended method. Can also build via a [bash script](build-locally/#build-locally-via-bash-script){:target="_blank"} 

If you run via self hosted it easier to track the build and any issues/errors are logged. 
Other than human error with the code the most frequent issue is with sha256 issues due to issue with download or a version change. So there sha256 no longer matches.

If the build completes without errors it should produce an artifact that you can download and test

### Backup your key data and configs

Make a copy of `~/.var/app/net.retrodeck.retrodeck/` folder and name it `old.net.retrodeck.retrodeck`
Make a full back up or partial backups of the retrodeck folder normally under `~/retrodeck` or sdcard/other drive.
On retrodeck folder backups:

Generally, very few things would target the roms folder, but the other folders could be targeted for various scripts. Our recommendation would be to back up the full ~/retrodeck folder, but as a tester you can decide how much you want to risk.

### Install and Test the built Flatpak

Remove all the installed versions of RetroDECK, to be sure uninstall both in the system and userland:

`flatpak uninstall --user net.retrodeck.retrodeck` and press (y) yes to remove RetroDECK. If you have more then one version installed for some reason choose to remove all versions.

`flatpak uninstall --system net.retrodeck.retrodeck` and press (y) yes to remove RetroDECK. If you have more then one version installed for some reason choose to remove all versions.

Download and unzip the artifact to a folder of your choice and then run this command in the same folder and the unarchived file.

`flatpak install --user --bundle --noninteractive -y RetroDECK-cooker.Flatpak`

On first run it will ask you to upgrade

If it then asks to upgrade the cooker as well. Then say no.

### Add emulator/system to ES-DE

In order for your new system to show in ES-DE it need to be added to ES-DE, which has been forked by the project.

Fork and clone the RetroDECK ES-DE [Repo](https://github.com/XargonWan/RetroDECK-ES-DE/fork)

Once cloned cd into your cloned directory and create a new branch and then 

```bash
cd retrodeck-main/resources/systems/linux
```

There are two files in that folder and the relevant ruffle sections are shown below. There is a good chance that the emulator or system you are adding is already there and just needs to have the remarks removed. If not you can use this as an example of what needs adding.

`es_system.xml`
```xml
<system>
       <name>flash</name>
       <fullname>Adobe Flash</fullname>
       <path>%ROMPATH%/flash</path>
       <extension>.swf .SWF</extension>
       <command label="Ruffle (Standalone)">%EMULATOR_RUFFLE% %ROM%</command>
       <!-- <command label="Lightspark (Standalone)">%EMULATOR_LIGHTSPARK% &ndash;&ndash;fullscreen %ROM%</command> -->
       <platform>flash</platform>
       <theme>flash</theme>
</system>
```

In the rules file I added an entry for the ruffle-wrapper.sh and added the <!-- RetroDECK --> remark to make it easier to compare for differences when merges are made from ES-De source project. A wrapper script was required as ruffle has most options set from cli switches.

`es_find_rules.xml`
```xml
<emulator name="RUFFLE">
       <!-- Adobe Flash player Ruffle -->
       <rule type="systempath">
           <entry>ruffle</entry>
       </rule>
       <rule type="staticpath">
           <entry>/app/bin/ruffle-wrapper.sh</entry>   <!-- RetroDECK -->
           <entry>~/Applications/ruffle/ruffle</entry>
           <entry>~/.local/share/applications/ruffle/ruffle</entry>
           <entry>~/.local/bin/ruffle/ruffle</entry>
           <entry>~/bin/ruffle/ruffle</entry>
       </rule>
</emulator>
```

### Editing the Manifest Part 2

Towards the end of the retrodeck manifest build is a simple manifest name: retrodeck. This setup some final configuration including copying the wrapper script for launching ruffle. See below.

```yml
      # Ruffle wrapper
      - cp ${FLATPAK_DEST}/retrodeck/emu-configs/ruffle/ruffle-wrapper.sh /app/bin/
      - chmod +x /app/bin/ruffle-wrapper.sh
```
The contents of the script are also shown below.

```bash
#!/bin/bash

source /app/libexec/global.sh

#Check if Steam Deck in Desktop Mode
if [[ $(check_desktop_mode) == "true" ]]; then
    ruffle --graphics vulkan --config /var/data/ruffle --save-directory $saves_folder/ruffle --fullscreen "$@"
else
    ruffle --graphics gl --config /var/data/ruffle --save-directory $saves_folder/ruffle --fullscreen "$@"
fi
```

Ruffle wrapper script sources the /app/libexec/global.sh to reference the $saves_folder variable. it also allows the use if check_desktop_mode function so that a different graphics preference can be set for Steam Deck in game mode.

```
Other important variables referenced in the global.sh are:-

```bash
RETRODECKHOMEDIR  is $rdhome (aka retrodeck folder)
RETRODECKSAVESDIR is $saves_folder (aka retrodeck/saves)
RETRODECKROMSDIR  is $roms_dir (aka retrodeck/roms)
```

These are used by the configurator tool to move key folders and reset them. More on that tool soon.


###  Configs for Ruffle

Be sure to clean your own data (your paths, your own specific configs), and when you need to insert a path remember that Ruffle will not know what $saves_folder is, so you have to put some placeholders, for example:

Also check that any folder referenced is created on first run or pre created

Ruffle requires the following folder:- 
 *$rdhome/saves folder
 * var/data/ruffle
 * config data in emu_configs/ruffle and the wrapper script

preferences.toml is also in emu configs explain in setup and reset?

```toml
graphics_backend = "vulcan"
```

To clean your own specific data you can move away your actual config files and make the software (Ruffle) create an empty one.
You can then compare them (I like Meld to do this) and move in the clean one only the worthy settings.

path `functions/prepare_component.sh`
```bash linenums="1"
 if [[ "$component" =~ ^(ruffle|all)$ ]]; then
  component_found="true"
    if [[ "$action" == "reset" ]]; then # Run reset-only commands
      log i "----------------------"
      log i "Preparing Ruffle"
      log i "----------------------"
      if [[ $multi_user_mode == "true" ]]; then # Multi-user actions
        log d "Figure out what Ruffle needs for multi-user"
      else # Single-user actions
        # NOTE: TODO
        rm -rf "/var/data/ruffle"
        create_dir "/var/data/ruffle"
        cp -fvr "$emuconfigs/ruffle/"prefernces.toml "/var/data/ruffle" # component config
        # No bios for ruffle or other settings. Showed for documentation purposes
        set_setting_value "$ruffleconf" "pref-path" "ruffle"
      fi
      # Shared actions
      dir_prep "$saves_folder/ruffle" # Multi-user safe?
    fi
    if [[ "$action" == "postmove" ]]; then # Run only post-move commands
      dir_prep "$saves_folder/ruffle" # Multi-user safe?
      # No bios for ruffle or other settings. Showed for documentation purposes
      set_setting_value "$ruffleconf" "pref-path" "ruffle"
    fi
  fi
```

1. Line 1 - place the component name there, in this case ruffle|all (all lowercase), this means that this script is running when you reset "ruffle" or "all" the components.
1. Line 4-6 ensure it is logged and that part is for Ruffle
1. Line 7 - actions that are executed on multi user mode ONLY
1. Line 9 - single user action: what is not multi user but single user ONLY
1. Line 17 - shared actions: something that is working regardless of the multi user mode or not, this is the part that interests you more: put all the logic here (which logic? I'll tell you later)
1. Line 20 - action to be executed only when the folder are moved with the tool (like we want to move the folder and tell the configuration files where is the folder located now)
1. For ruffle you can just populate the postmove and the shared actions, shared actions as you can see are inside the if [[ "$action" == "reset" ]]; then without any other check.
This if [[ "$action" == "postmove" ]]; then # Run only post-move commands instead, is out of the reset function.
1. The reset function is called when the component is installed (aka the first time a user installs RetroDECK) and when a reset is triggered from the configurator.
1. Then you need to add it to the cli, as you can reset components via cli.

```bash
retrodeck.sh
```
retrodeck.sh

```bash linenums="1"
    --reset-component*)
      echo "You are about to reset one or more RetroDECK components or emulators."
      echo "Available options are: es-de, retroarch, cemu, dolphin, duckstation, gzdoom, melonds, pcsx3, pico8, ppsspp, primehack, rpcs3, ryujinx, xemu, vita3k, ruffle, mame, all"
      read -p "Please enter the component you would like to reset: " component
      component=$(echo "$component" | tr '[:upper:]' '[:lower:]')
      if [[ "$component" =~ ^(es-de|retroarch|cemu|dolphin|duckstation|gzdoom|mame|melonds|pcsx2|ppsspp|primehack|ryujinx|rpcs3|vita3k|ruffle|xemu|all)$ ]]; then
        read -p "You are about to reset $component to default settings. Enter 'y' to continue, 'n' to stop: " response
        if [[ $response == [yY] ]]; then
          prepare_component "reset" "$component" "cli"
          read -p "The process has been completed, press Enter key to start RetroDECK."
          shift # Continue launch after previous command is finished
        else
          read -p "The process has been cancelled, press Enter key to exit."
          exit
        fi
      else
        echo "$component is not a valid selection, exiting..."
        exit
      fi
      ;;
```

Add ruffle to retrodeck.sh which allows for the reset process.

### Final Configuration

Some final functions are also carried by the configurator.sh file.

Look for all references to ruffle.

A key one to add and check is to launch the emulator in standalone mode.