# Network Share Reconnecter
This project contains a **VB script** and a **Windows Scheduler Task** to automatically reconnect disconnected network shares / drives on login or unlock (if they are available or become available in the near future). But why? The reason for creating this project is that attached network drives are under Windows often not correctly reconnected on startup, restart or resume from hibernate / sleep. This still happens with the latest release *Windows 10 Fall Creators Update* or in my case especially with this release.

Many claim that the reconnect problem leads back to a timing problem during login and the establishment of the network connection over WLAN but there is no official statement from Microsoft as far as I know. There are many registry hacks out there which do not work and do not improve the described behaviour. Moreover this problem already exists for years so I now decided to create a reconnecter which reacts on network availability and share accessability.

## Reconnect offline network shares
Example for a reconnect of a drive which was wrongly recognized by Windows as offline.

![1](/screenshots/drives_off_on.png)

## Reconnect not-connected network shares
Example for a reconnect of a drive which was recognized by Windows as not-connected.

![2](/screenshots/drives_nc_on.png)

## Prerequisites
* Windows XP, 7, 8, 8.1, 10
* little knowledge on Windows Task Scheduling

## Download / Installation
1. [Download v1.3.8](https://github.com/thexmanxyz/network-share-reconnecter/releases/download/v1.3.8/nsr.v1.3.8.zip) of the Network Share Reconnecter Package.
2. Extract the files.
3. Modify the sample configuration in the **share_reconnect.vbs** script file.
   * At least modify `hostname`, `sharePaths` and `shareLetters` (see also the [configuration section](https://github.com/thexmanxyz/network-share-reconnecter/blob/master/src/share_reconnect.vbs#L43-L45) and [#1](https://github.com/thexmanxyz/network-share-reconnecter/issues/1)).
   * Multiple servers can be easily added and configured.
   * Be aware that every host has to support ping, otherwise please deactivate script ping execution ([#9](https://github.com/thexmanxyz/network-share-reconnecter/issues/9))
4. Copy **share_reconnect.vbs** to a self defined directory.
5. Start Windows Task Scheduler - manually or with **taskschd.msc**.
6. Import **Network_Share_Reconnecter.xml** - there are different versions for Windows 7 and 10.
7. Modify the Scheduler Task.
   * At least change the path to the script which you have chosen before (or do it previously in the **Network_Share_Reconnecter.xml**).
   * (Optional) Change the user for the defined triggers, by default all computer users will be affected by the script.
   * (Optional) Extend or modify the Scheduler Task depending on your favor and preferences.

## Configuration and Parameters
Here a short description of the available parameters which can be configured:

* Server Configuration
  * `hostname` - IP or hostname of the remote server **(must be modified)**
  * `sharePaths` - all share paths on the server **(must be modified)**
  * `shareLetters` - the share / drive letters for the defined paths **(must be modified)**
  * `persistent` - should *net use* create a persistent share **(yes/no)**
  * `user` - username for *net use* authentication if required **(optional)**
  * `password` - password for *net use* authentication if required **(optional)**
  * `secure` - defines whether the HTTP or HTTPS protocol should be used **(URI only)**
  
* Global Script Configuration
  * `pingEnabled` - defines whether the script should use ping availability check
  * `pingWait` - wait time after failed server ping
  * `pingTimeout` - how many milliseconds pass before the ping is canceled
  * `pingCtn` - how many pings per access request should be executed before giving up
  * `pingDefaultSrv` - use common server if target service rejects pings **(URI only)**
  * `netUseWait` - wait time after failed *net use*
  * `netUseCtn` - how many *net use* fails per reconnect are allowed before giving up
  * `reconWait` - wait time after failed availability check
  * `reconAdaptive` - boolean to enable automatic reconnection intensity instead of static timeout
  * `serverRetryCtn` - how many overall reconnection tries should be executed
  * `debug` - enable or disable debug messages on current reconnection state

### UNC Sample Configuration
If your share is accessible over an UNC path like `\\192.168.1.1\path\to\share` use:

`createUncSrvConfig("192.168.1.1", Array("path\to\share"), Array("Z:"), "yes", "", "")`


### URI Sample Configuration
If your share needs to be accessed over HTTP(S) like `http://my.webserver.com/path/to/share` use:

`createUriSrvConfig("my.webserver.com", Array("path/to/share"), Array("Z:"), "yes", "", "", false)`

## Features
* Automatic reconnection of network drives and shares on logon or unlock of the workstation.
* Stealth script execution (operates without exposing any prompts or windows - except when debug is enabled :P).
* Self determines server and share availability and accessability.
* Variable and flexible configuration of polling, timeouts and fallback handling.
* Configuration of multiple servers together with their shares.
* Fast ICMP ping checks and adaptive intensity.
* Scheduling task included (`.xml`) for easy import.
* optimized, adaptive and fast reconnection in comparison to simple scripts.

## Future Tasks
- [ ] net use analyze to better handle failure states
- [ ] load script config from XML
- [ ] install and configuration app (script)
- [ ] permanent task / hook on event?

## Known Issues
There might be an issue which results in an occurring tray icon which states *Could not reconnect all network drives*: 

![3](/screenshots/tray_issue.png)

If you want to get rid of this tray icon and the corresponding message you can try to use the `.reg` files included in this [repository](https://github.com/thexmanxyz/network-share-reconnecter/tree/master/registry). They might also get merged into one of the next releases. There is one file to apply the fix and a second one to restore the previous / original state. Please see also [#11](https://github.com/thexmanxyz/network-share-reconnecter/issues/11) for more approaches to fix this issue.

## by [thex](https://github.com/thexmanxyz)
Copyright (c) 2019, free to use in personal and commercial software as per the [license](/LICENSE).
