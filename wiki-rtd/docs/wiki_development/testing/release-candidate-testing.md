# Release Candidate: Cooker Testing

If you are on `SteamOS` like on the Steam Deck make sure you are in `Desktop Mode` before you begin.

### Do not use the the Rowan Skye Method

We do not recommend to blindly install the Cooker without reading this guide and follow the instructions. To just install and hope for the best  is the `Rowan Skye Method`. <br>
This is only recommended for chaotic blue draconic wolfkin testers living on the edge with nothing to lose.

## Step 1: Take some Backups

First take some backups:

- Make a copy of `~/.var/app/net.retrodeck.retrodeck/` folder and name it `old.net.retrodeck.retrodeck`
- Make a full back up or partial backups of the retrodeck folder normally under `~/retrodeck` or sdcard/other drive.

**On retrodeck folder backups:**

Generally, very few things would target the roms folder, but the other folders could be targeted for various scripts. Our recommendation would be to back up the full `~/retrodeck` folder, but as a tester you can decide how much you want to risk.

**Disclamer:**

The RetroDECK Team also had never had any major data loss so far and we do Cooker updates almost daily.

## Step 2: Download the Cooker

- Download the latest Cooker release from: [RetroDECK Cooker: Latest](https://github.com/XargonWan/RetroDECK-cooker/releases/latest) to your download folder.
- Make a note of the `<version_id>` that is a randomly generated word, example: `cooker-X.X.Xb-HelloWorld`, in this example it's `HelloWorld`.

## Step 3: Terminal

Open the terminal window.

### Remove and Install

- `flatpak remove net.retrodeck.retrodeck` and press (y) yes to remove RetroDECK. If you have more then one version installed for some reason choose to remove all versions.
- `flatpak install --user --bundle --noninteractive -y "~/Downloads/RetroDECK-cooker.flatpak"` this will install RetroDECK Cooker.

## Step 4: Upgrade

**IMPORTANT:**

Under **NO** circumstances press the `Full Wipe and Fresh Install` button. That is **NOT** to be confused with Configurators `Reset RetroDECK` option. This in fact a `Nuke Everything` option, it will remove all of RetroDECK including all ROMS/BIOS/Downloaded Media and everything else you have in RetroDECK's folders...forever.

### Upgrading RetroDECK

- After the installation is complete type:  `flatpak run --command=sh net.retrodeck.retrodeck` followed by `retrodeck`.
- Press the `Upgrade` button and keep the installation moving until all the steps are complete and RetroDECK will launch.
- Quit RetroDECK again and save all the output from the terminal into a text file.

### Upgrading from Cooker to Cooker.

Always stay on the latest Cooker release, note that the Cooker auto updater might be bug out sometimes. It might be better just to download the latest Cooker file github.

After doing the upgrade make sure you are on the right Cooker version via the ES-DE menu.

Minor releases are released to fix reported issues / bugs.

If a new minor release is out test if the issues are still there and if they are report to the RetroDECK Team via the Beta-Testing channel on discord.

You can always follow the lastest Cooker releases there as well, with direct links to download them.

---

## 0.8.0b upgrade & testing

Check the [0.8.0b Patch Notes](../../wiki_rd_versions/version_0.8.0b/0.8.0b.md) for all the changes.

### When upgrading from 0.7.6b Stable to 0.8.0b Cooker

**IMPORTANT:**

Check for the message `"In version 0.8.0b, the following changes were made that required config file updates/reset or other changes to the filesystem:"` in the terminal. This is a sign that tell you the post update migration is running, this is crucial for RetroDECK's upgrade to fully happen.

This is what should happend in the post migration.

```
- Remove any of the following files, if they exist:
	"$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_config.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_generic_standard.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_ps3_dualshock3.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_ps4_dualshock4.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_ps5_dualsense.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_steam_controller_gordon.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_neptune.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_switch_pro.vdf"
    "$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_xbox360.vdf"
	"$HOME/.steam/steam/controller_base/templates/RetroDECK_controller_xboxone.vdf"
- The line "[nintendo_button_layout]" should be renamed "[abxy_button_swap]" in retrodeck.cfg
- The folder "/var/config/emulationstation" should be renamed to "/var/config/ES-DE" (internal Flatpak paths)
- ES-DE should be put through a reset process (verify via logs)
- MAME should be put through a reset process (verify via logs)
- Vita3K should be put through a reset process (verify via logs)
- GZDOOM should be put through a reset process (verify via logs)
- If the folder ~/retrodeck/.logs exists, it should be renamed to ~/retrodeck/logs
- The RPCS3 saves should be migrated by the following steps:
	- The folder ~/retrodeck/backups should be created if it doesn't already exist
	- A backup of current RPCS3 save data should be created at ~/retrodeck/backups/(date and time backup started)_rpcs3_save_data.zip
	- The symlink and real folders for save data should be switched:
		- The folder at ~/retrodeck/saves/ps3/rpcs3 should now be real
		- The folder at ~/retrodeck/bios/rpcs3/dev_hdd0/home/00000001/savedata should now be a symlink back to ~/retrodeck/saves/ps3/rpcs3
- Rename the folder ~/retrodeck/bios/switch/registered to ~/retrodeck/bios/switch/firmware
- Default ROMs folders should be recreated by ES-DE to add new systems
```


---

## Step 5: Start testing

If you are on `SteamOS` like on the Steam Deck return to `Game Mode` for this step.

If you are on `Linux Desktop` make sure you launch RetroDECK via Steam, else Steam Input won't work.

Now feel free to test all the new features of the update and report any issues, check the patch notes for what is new.

All logs will be saved in the new `retrodeck/logs` folder

Thank you! ❤️
