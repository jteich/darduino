Dockerized Arduino IDE
======================

This project is about installing and running the Arduino IDE from a docker image.

You do not have to pollute your original operating system,
with installing the several libs and the app itself.
Instead you only need docker to be installed.

At the same time you can keep your projects on your disk,
that the arduino docker container will reach through volumes.

This solution was made based on the
[Running GUI apps with Docker](http://fabiorehm.com/blog/2014/09/11/running-gui-apps-with-docker/)
blog post written by Fábio Rehm.


## Use the container

Run this if you want the container to be removed after the session:

```bash
    docker run \
        -it \
        --rm \
        --network=host \
        -e DISPLAY=$DISPLAY \
        -v $HOME/.Xauthority:/home/developer/.Xauthority \
        --device /dev/ttyUSB0:/dev/ttyUSB0 \
        -v $HOME/Arduino:/home/developer/Arduino \
        jteich/darduino:latest \
        arduino
```

Or you can use `docker-compose` script like:

```yml
version: "3.4"

services:
  arduino:
    image: tombenke/darduino
    container_name: arduino
    network_mode: "host"
    environment:
      - "DISPLAY=${DISPLAY}"
    volumes:
      - "/tmp/.X11-unix:/tmp/.X11-unix"
      - "/dev/ttyUSB0:/dev/ttyUSB0"
      - "${HOME}/topics:/topics"
    command: "arduino"
    privileged: true
```

or just simply run the `./arduino.sh` shell script, which contains the command listed above.

In case you want to make changes, then start the container without the `--rm` switch, 
and execute the `commit` and `push` docker commands.

## Running under macOS

(current as of macOS Monterey)
1. Install XQuartz
   Get the installation file from https://www.xquartz.org/index.html or install with Brew using  `brew update && brew install xquartz`
2. Start XQuartz
3. Go to menu > Preferences > Security
4. Ensure that "Allow connections from network clients" is selected
5. Shutdown and restart XQuartz
6. In console, run `xhost + 127.0.0.1`
At this point, XQuartz should allow connections from the Docker contianer

## Building the Container

(only necessary if you want to make changes to the container, otherwise, you can just use the prebuild image)
1. `docker build -t darduino .`
If you do this, you will need to update the container name in the run script; in other words, replace `jteich/darduino:latest` with `darduino`

### ESP8266 Board Manager usage

Starting with 1.6.4, Arduino allows installation of third-party platform packages using Boards Manager.

1. Start Arduino and open Preferences window.
2. Enter `http://arduino.esp8266.com/stable/package_esp8266com_index.json`
   into Additional Board Manager URLs field.
   You can add multiple URLs, separating them with commas.
3. Open __Boards Manager__ from __Tools__ > __Board__ menu and install esp8266 platform.
4. Select your ESP8266 board from __Tools__ > __Board__ menu after installation.
   For `ESP8266-12F`, select the __Generic ESP8266 Module__.


## References

- [Running GUI apps with Docker](http://fabiorehm.com/blog/2014/09/11/running-gui-apps-with-docker/)
- [Ubuntu Package Search](http://packages.ubuntu.com/)
- [Install the Arduino Software (IDE) on on Linux](https://www.arduino.cc/en/Guide/Linux/)
- [How to Install AVRDude 6.3 in Ubuntu 16.04, Ubuntu 14.04](http://ubuntuhandbook.org/index.php/2017/01/install-avrdude-6-4-ubuntu-16-04/)
