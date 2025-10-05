# :soccer: RoboCup Soccer Simulator Server

[![CircleCI](https://circleci.com/gh/rcsoccersim/rcssserver/tree/master.svg?style=svg)](https://circleci.com/gh/rcsoccersim/rcssserver/tree/master)
![License](https://img.shields.io/github/license/rcsoccersim/rcssserver.svg)

![image](https://user-images.githubusercontent.com/1832537/49242985-f69a3c00-f3ea-11e8-97f5-9b0bfdfc4e1c.png)

The RoboCup Soccer Simulator Server (rcssserver) is a research and educational tool for multi-agent systems and artificial intelligence. It allows 11 simulated autonomous robotic players to play soccer (football).

For further reading, please check [the user's manual](https://rcsoccersim.readthedocs.io/).

There are three different solutions to run the RoboCup Soccer Simulator Server.
- Download the AppImage release and run (Linux, Windows WSL)
- Build from source and run (Linux, Windows WSL)
- Use Docker Image

## :gift_heart: AppImage
#### Download AppImage
Download "rcssserver-x86_64-?.?.?.AppImage" from the (release page)[https://github.com/CLSFramework/rcssserver/releases] / latest version
or use the below command to download the latest version of AppImage:
```bash
wget $(curl -s https://api.github.com/repos/clsframework/rcssserver/releases/latest | grep -oP '"browser_download_url": "\K(.*rcssserver-x86_64-.*\.AppImage)' | head -n 1)
```

#### Install dependency and update permission

then you need to update the permission of the AppImage and install fuse:
```bash
sudo apt update
sudo apt-get install fuse
chmod +x rcssserver-x86_64-*.AppImage
```

#### Run

to run the AppImage:
```bash
./rcssserver-x86_64-?.?.?.AppImage
```

## :wrench: Build From Source

rcssserver is implemented by C++14 and depends some libraries.
Make sure you have the required dependencies installed on your system:

- g++ (which supports C++17)
- autoconf
- automake
- libtool
- flex
- bison
- boost >= 1.44

In the case of Ubuntu 20.04 or 22.04, the following commands will resolve all dependencies:

```
sudo apt update
sudo apt install build-essential automake autoconf libtool flex bison libboost-all-dev
```

Then, download the latest rcssserver tarball from the [releases section](https://github.com/rcsoccersim/rcssserver/releases).
Extract it and from the rcssserver directory execute:

```bash
tar xzvfp rcssserver-x.x.x.tar.gz
cd rcssserver-x.x.x
./configure
make
```

This will build the necessary binaries to get you up and running.
`rcssserver/src/rcssserver` is the binary for the simulator server.

The simulator server manages the actual simulation and comunicates with client programs that
control the simulated robots.
To be able to run, the binary needs to find shared libraries which are created when you build rcssserver.
This means you must either install the server (make install) or run it from `rcssserver/src`.

A sample client can be found at `rcssserver/src/rcssclient`.

To see what is actually happening in the simulator, you will need to start a
simulator monitor, which needs to be installed separately ([rcssmonitor](https://github.com/rcsoccersim/rcssmonitor), or any other third party monitor).

To playback games that you have recorded or downloaded, you will need to start the log player.
[rcssmonitor](https://github.com/rcsoccersim/rcssmonitor) can be used for this purpose.

The version 17.0.0 or later support [CMake](https://cmake.org/).
If CMake is prefered or problems with the above procedure, try the following commands at the top of the project directory:

```bash
cd rcssserver-x.x.x
mkdir build
cd build
cmake ..
make
```

## :gear: Configuring

Before building rcssserver, you will need to run the `configure` script located in the root of the distribution directory.
The default configuration will set up to install the server components in the
following location:

`/usr/local/bin`        for the executables

You may need administrator privileges to install the server into the default
location.  This locations can be modified by using configure's `--prefix=DIR`
and related options.  See `configure --help` for more details.
```bash
./configure --prefix=YOUR_INSTALLATION_DIR
```

The server has several features that can be enabled or disabled at configure time
by using the `--enable-FEATURE[=ARG]` or `--disable-FEATURE` parameters to
`configure`.  `--disable-FEATURE` is equivalent to `--enable-FEATURE=no` and
`--enable-FEATURE` is equivalent to `--enable-FEATURE=yes`.  The only valid values
for `ARG` are `yes` and `no`.

`--enable-fast_scanner=yes` will enable a fast building but (very) large scanner for the coach language.
You will need to have `flex` installed and you will need to manually remove the `coach_lang_tok.cpp` file in the `rcssserver/rcss/clang` directory.
This is disabled by default.
I found the actual speed of the parser show only minimal improvement when using this option on my system, but this may not be true on your system.
All I can suggest is to test it on your system and decide for yourself if the speed increase justifies the increase in size of the executable.

`--enable-rcssclient=yes` will enable the building of rcssclient, a sample
client program.  This is enabled by default.

`--enable-debug=yes` will enable the building of the modules with debugging
information.  This is disabled by default.

Once you have successfully configured the monitor, simply run `make` to build the sources.

If CMake is chosen, `ccmake` command is available for the configuration:
```bash
cd build
ccmake ..
```

## :hammer_and_wrench: Building

Once you have successfully configured the server, simply run `make` to build
the sources.

## :package: Installing

When you have completed building the server, its components can be installed
into their default locations or the locations specified during configuring by
running
```bash
make install
```
Depending on where you are installing the server, you may need special permissions.

If you install the server under the location you specified, you may need to set the environment variable `PATH`.
Otherwise, you have to enter the full path to the executable when you execute the server.
In the case of Linux system, the recommended way is to add the following line at the bottom of `~/.profile`:
```
PATH="YOUR_INSTALLATION_DIR/bin:$PATH"
```
Then, log out and log in again.

You may also need to set the library path before executing the server.
In most Linux systems, you can modify the library path by editing `/etc/ld.so.conf` or the environment variable `LD_LIBRARY_PATH`.
The recommended way is to use `LD_LIBRARY_PATH` because all users can edit this variable without extra privileges.
Add the following line at the bottom of `~/.bashrc`:
```
LD_LIBRARY_PATH="YOUR_INSTALLATION_DIR/lib:$LD_LIBRARY_PATH"
export LD_LIBRARY_PATH
```
Then, open a new terminal.

## :wastebasket: Uninstalling

The server can also be easily removed by entering the distribution directory and
running
```bash
make uninstall
```

This will remove all the files that where installed,
but not any directories that were created during the installation process.

In the case of CMake, find `install_manifest.txt` under the build directory, then execute:
```bash
xargs rm < install_manifest.txt
```

## :arrow_forward: Using the Server

To start only the server either type `./rcssserver` from the directory
containing the executable or `rcssserver` if you installed the executables
in your PATH.
```bash
rcssserver
```
rcssserver will look in your home directory for the configuration files:

- ~/.rcssserver/server.conf
- ~/.rcssserver/player.conf
- ~/.rcssserver/CSVSaver.conf
- ~/.rcssserver-landmark.xml  (optional)

If these files do not exist they will be created and populated with default values.

To start the sample client, type `./rcssclient` or `rcssclient` as above.  Then type
`(init sample)`.  This will connect the sample client to the server.  You can then
type in client command to move the client around the field. You will also need a
monitor to be able to see whats happening on the field.

If you installed the server and the monitor successfully, you can use the
`rcsoccersim` script. To start the simulator (server and monitor) either type:

```bash
rcsoccersim
```

## :incoming_envelope: Contributing

For bug reports, feature requests and latest updates, please goto
https://github.com/rcsoccersim/rcssserver and open an issue or a pull request.

> The RoboCup Soccer Server Maintainance Group


## Updates for Foxsy

### Player types in log files

```bash
(player_type (id 0)(player_speed_max 1)(stamina_inc_max 1)(player_decay 1)(inertia_moment 1)(dash_power_rate 1)(player_size 1)(kickable_margin 1)(kick_rand 1)(extra_stamina 1)(effort_max 1)(effort_min 1)(kick_power_rate 1)(foul_detect_probability 1)(catchable_area_l_stretch 1)(unum_far_length 1)(unum_too_far_length 1)(team_far_length 1)(team_too_far_length 1)(player_max_observation_length 1)(ball_vel_far_length 1)(ball_vel_too_far_length 1)(ball_max_observation_length 1)(flag_chg_far_length 1)(flag_chg_too_far_length 1)(flag_max_observation_length 1)(dist_noise_rate 1)(focus_dist_noise_rate 1)(land_dist_noise_rate 1)(land_focus_dist_noise_rate 1)(name nader)(t-number 15.000000))
(player_type (id 1)(player_speed_max 2)(stamina_inc_max 2)(player_decay 2)(inertia_moment 2)(dash_power_rate 2)(player_size 2)(kickable_margin 2)(kick_rand 2)(extra_stamina 2)(effort_max 2)(effort_min 2)(kick_power_rate 2)(foul_detect_probability 2)(catchable_area_l_stretch 2)(unum_far_length 2)(unum_too_far_length 2)(team_far_length 2)(team_too_far_length 2)(player_max_observation_length 2)(ball_vel_far_length 2)(ball_vel_too_far_length 2)(ball_max_observation_length 2)(flag_chg_far_length 2)(flag_chg_too_far_length 2)(flag_max_observation_length 2)(dist_noise_rate 2)(focus_dist_noise_rate 2)(land_dist_noise_rate 2)(land_focus_dist_noise_rate 2)(name ronaldo)(t-number 7.000000))
(player_type (id 2)(player_speed_max 1.05)(stamina_inc_max 47.075)(player_decay 0.337566)(inertia_moment 3.43916)(dash_power_rate 0.00565417)(player_size 0.3)(kickable_margin 0.793198)(kick_rand 0.193198)(extra_stamina 70.6056)(effort_max 0.917578)(effort_min 0.517578)(kick_power_rate 0.027)(foul_detect_probability 0.5)(catchable_area_l_stretch 1.18533)(unum_far_length 20)(unum_too_far_length 40)(team_far_length 125.096)(team_too_far_length 125.096)(player_max_observation_length 125.096)(ball_vel_far_length 20)(ball_vel_too_far_length 40)(ball_max_observation_length 125.096)(flag_chg_far_length 20)(flag_chg_too_far_length 40)(flag_max_observation_length 125.096)(dist_noise_rate 0.0125)(focus_dist_noise_rate 0.0125)(land_dist_noise_rate 0.00125)(land_focus_dist_noise_rate 0.00125))
```

The server can receive a file path as an argument to the set player types.

```bash
./rcssserver --server::hetero_path=PATH/hetero.json
```

The json will be looklike this:

```json
{
    "0":{
        "player_speed_max": 1,
        "stamina_inc_max": 1,
        "player_decay": 1,
        "inertia_moment": 1,
        "dash_power_rate": 1,
        "player_size": 1,
        "kickable_margin": 1,
        "kick_rand": 1,
        "extra_stamina": 1,
        "effort_max": 1,
        "effort_min": 1,
        "kick_power_rate": 1,
        "foul_detect_probability": 1,
        "catchable_area_l_stretch": 1,
        "unum_far_length": 1,
        "unum_too_far_length": 1,
        "team_far_length": 1,
        "team_too_far_length": 1,
        "player_max_observation_length": 1,
        "ball_vel_far_length": 1,
        "ball_vel_too_far_length": 1,
        "ball_max_observation_length": 1,
        "land_vel_far_length": 1,
        "land_vel_too_far_length": 1,
        "flag_max_observation_length": 1,
        "dist_noise_rate": 1,
        "focus_dist_noise_rate": 1,
        "land_dist_noise_rate": 1,
        "land_focus_dist_noise_rate": 1,
        "name": "nader",
        "t-number": 15
    },
    "1":{
        "player_speed_max": 2,
        "stamina_inc_max": 2,
        "player_decay": 2,
        "inertia_moment": 2,
        "dash_power_rate": 2,
        "player_size": 2,
        "kickable_margin": 2,
        "kick_rand": 2,
        "extra_stamina": 2,
        "effort_max": 2,
        "effort_min": 2,
        "kick_power_rate": 2,
        "foul_detect_probability": 2,
        "catchable_area_l_stretch": 2,
        "unum_far_length": 2,
        "unum_too_far_length": 2,
        "team_far_length": 2,
        "team_too_far_length": 2,
        "player_max_observation_length": 2,
        "ball_vel_far_length": 2,
        "ball_vel_too_far_length": 2,
        "ball_max_observation_length": 2,
        "land_vel_far_length": 2,
        "land_vel_too_far_length": 2,
        "flag_max_observation_length": 2,
        "dist_noise_rate": 2,
        "focus_dist_noise_rate": 2,
        "land_dist_noise_rate": 2,
        "land_focus_dist_noise_rate": 2,
        "name": "ronaldo",
        "t-number": 7
    }
}
```

### Real Example

#### Run separately

- Run server
  
```bash
./rcssserver --server::hetero_path=../hetero.json
```

- Run left team
  
```bash
./start.sh -t left -s left -p 6000 -j '{"version":1,"formation_name":"433","player_type_ids":[4,7,8,9,10,13,14,15,0,1,2]}'
```

- Run right team

```bash
./start.sh -t right -s right -p 6000 -j '{"version":1,"formation_name":"532","player_type_ids":[35,34,33,32,31,30,29,28,27,26,25]}'
# or
./start.sh -t right -s right -p 6000 -j '{"version":1,"formation_name":"532","player_type_ids":[17,16,15,14,13,12,11,10,9,8,7]}'
```

#### Run toghether

```bash
# set PATH to your start.sh
export BinPATH=/home/nader/workspace/foxsy/FoxsyCyrus2DBase/build/bin

./rcssserver --server::hetero_path=../hetero.json --server::team_l_start=\"${BinPATH}/start.sh -t left -s left -p 6000 -e temp -j {@qq@version@qq@:1@c@@qq@formation_name@qq@:@qq@433@qq@@c@@qq@player_type_ids@qq@:[4@c@7@c@8@c@9@c@10@c@13@c@14@c@15@c@0@c@1@c@2]}\" --server::team_r_start=\"${BinPATH}/start.sh -t right -s right -p 6000 -e temp -j {@qq@version@qq@:1@c@@qq@formation_name@qq@:@qq@532@qq@@c@@qq@player_type_ids@qq@:[35@c@34@c@33@c@32@c@31@c@30@c@29@c@28@c@27@c@26@c@25]}\"

#or

./rcssserver --server::hetero_path=../hetero.json --server::team_l_start=\"${BinPATH}/start.sh -t left -s left -p 6000 -e temp -j {@qq@version@qq@:1@c@@qq@formation_name@qq@:@qq@433@qq@@c@@qq@player_type_ids@qq@:[4@c@7@c@8@c@9@c@10@c@13@c@14@c@15@c@0@c@1@c@2]}\" --server::team_r_start=\"${BinPATH}/start.sh -t right -s right -p 6000 -e temp -j {@qq@version@qq@:1@c@@qq@formation_name@qq@:@qq@532@qq@@c@@qq@player_type_ids@qq@:[17@c@16@c@15@c@14@c@13@c@12@c@11@c@10@c@9@c@8@c@7]}\"
```

Results:

- player names should be `L0` to `L17` and `R0` to `R17` (check in log files)
- t-shirt numbers should be from `10` to `27` and `0` to `17` (check in log files)
- left team: (check in monitor)
  - goalie should be type `4` very small
  - defenders should be type `7,8,9,10` size 4
  - midfielders should be type `13,14,15` size 2.5
  - forwards should be type `0,1,2` size 1
  - In cout we should see `substituteTo: 1 4`, `substituteTo: 2 7`, ...
- right team: (check in monitor)
  - goalie should be type `35`
  - defenders should be type `34,33,32,31,30` (player 2 should be `34`)
    - num2 size 5 type 34
    - num3 size 4 type 33
    - num4 size 4 type 32
    - num5 size 3 type 31
    - num6 size 3 type 30
  - midfielders should be type `29,28,27`
  - forwards should be type `26,25`
    - num11 should be type 25
  - In cout we should see `substituteTo: 1 35`, `substituteTo: 2 34`, ...
