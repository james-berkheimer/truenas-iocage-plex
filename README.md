# true-iocage-plex
Scripted installation of Plex Media Server in a TrueNAS jail

## Description
This is a simple script to automate installation of Plex Media Server in a FreeNAS/TrueNAS jail, following current best practices.  It will create a jail, install Plex Media Server (with or without PlexPass), configure Plex to store its preferences and metadata outside the jail, and create a cron job to update the installed packages every week using the FreeBSD `latest` repository rather than `quarterly`.

## Installation
On your TrueNAS server, change to a convenient directory, and clone this repo using `git clone`  Then create a configuration file called `plex-config` using your preferred text editor.  In its simplest form, the file would look like this:
```
JAIL_IP="192.168.1.75"
DEFAULT_GW_IP="192.168.1.1"
POOL_PATH="/mnt/tank"
RELEASE_VERSION="13.2-RELEASE"
```
These values must be set.  They correspond to:

* JAIL_IP and DEFAULT_GW_IP are the IP addresses of the jail and your router, respectively.
* POOL_PATH is the path for your main data pool's root directory.

Optional configuration values include:

* PLEX_MEDIA_PATH - The path your Plex media files are located in. If set, this script will mount this directory on `/media` inside the jail. Specify a full path, for example "/mnt/tank/Media".
* USE_HW_TRANSCODE - If set to 1, the script will attempt to configure your system for hardware transcode in Plex. Defaults to 0. Please note that your hardware has to support hardware transcode, and that setting this variable will create a startup script and load a driver. Please see the bottom of this README file for a link to hardware requirements and instructions for Plex hardware transcode on TrueNAS.
* PLEX_CONFIG_PATH - The path to store your Plex metadata and configuration.  Defaults to `$POOL_PATH/plex_data`.
* NETMASK - The netmask, in bits, for the network the Plex jail will be on. Defaults to `24`, which is `255.255.255.0`.
* USE_BETA - If set to 1, the script will download and install the beta version of Plex Media Server.  Defaults to 0.
* RELEASE_VERSION - Set the freebsd release version to use for the Plex jail.  If not set, the script will use the current system version.


$PLEX_CONFIG_PATH need not exist before running this script; if it doesn't, the script will create it.  The script will also set ownership of that directory to the user/group IDs for Plex Media Server.  If this directory already exists, it **must not** be using Windows permissions.

Note that if the script creates $PLEX_CONFIG_PATH, it will create it as a **directory**, not as a dataset.  This means that it will not appear in, e.g., the Storage section of the TrueNAS GUI, where you could easily see how much space it's using, compression ratio, etc.  If you want these capabilities, you should create the dataset before running the script, and then ensure that $PLEX_CONFIG_PATH is set appropriately.

Once you've prepared the configuration file, run one of the following scripts:

* **FreeNAS:** `./plex-jail-freenas.sh`

* **TrueNAS:** `./plex-jail-truenas.sh`.

It should run for a few minutes and report completion.  You can then add storage to your jail as desired (see [Uncle Fester's Guide](https://www.familybrown.org/dokuwiki/doku.php?id=fester112:jails_plex#configure_a_mount_point) for one example), and log in to configure your media server.

Note that if you are interested in hardware transcode, instructions for setting this up in TrueNAS 12.0 or later can be found at https://github.com/kern2011/Freenas-Quicksync .
