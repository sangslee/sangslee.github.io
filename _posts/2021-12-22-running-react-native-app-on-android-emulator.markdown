---
layout: post
title:  "Running React Native App on Android emulator"
date:   2021-12-12 00:11:35 +0900
category: React Native
---

* content
{:toc}

Docker 컨테이너에서 작성한 React Native 애플리케이션을 호스트의 Android 에뮬레이터에서 동작시키는 방법을 설명한다.

## Prerequisite

- Docker
- Android Emulator

## Installation

### Android docker image for react native

먼저 React Native 애플리케이션을 개발하기 위한 docker 이미지를 다운로드하고 컨테이너를 다음과 같이 실행시킨다.

~~~
$ docker pull reactnativecommunity/react-native-android

$ docker run -it -d --name react-native-android -p 5555:5555 -p 8081:8081 reactnativecommunity/react-native-android /bin/bash
~~~

해당 컨테이너에는 React Native와 Android 개발 환경이 구축되어 있다.

~~~
$ docker exec -it react-native-android /bin/bash

# node --version
v14.18.1

# adb --version
Android Debug Bridge version 1.0.41
Version 31.0.3-7562133
Installed as /opt/android/platform-tools/adb
~~~

## Running React Native App

### Connect to a android emulator

호스트에 설치되어 있는 에뮬레이터를 확인한 다음 실행시킨다.

~~~
$ emulator -list-avds
Andoird_8.1_x86

$ emulator -avd Andoird_8.1_x86
~~~

컨테이너에서 `adb connect`를 통해 에뮬레이터에 연결한다. 

연결 시 사용한 `host.docker.internal` 주소는 컨테이너에서 호스트로 접근하고자 할 때 사용하는 특별한 DNS 이름이다.

~~~
$ docker exec -it react-native-android /bin/bash

# adb connect host.docker.internal:5555

# adb devices
List of devices attached
host.docker.internal:5555       device
~~~

### Create a react native application

컨테이너에서 새로운 React Native 프로젝트를 생성한다.

~~~
# npx react-native init HelloReactNative
npx: installed 636 in 25.808s

               ######                ######
             ###     ####        ####     ###
            ##          ###    ###          ##
            ##             ####             ##
            ##             ####             ##
            ##           ##    ##           ##
            ##         ###      ###         ##
             ##  ########################  ##
          ######    ###            ###    ######
      ###     ##    ##              ##    ##     ###
   ###         ## ###      ####      ### ##         ###
  ##           ####      ########      ####           ##
 ##             ###     ##########     ###             ##
  ##           ####      ########      ####           ##
   ###         ## ###      ####      ### ##         ###
      ###     ##    ##              ##    ##     ###
          ######    ###            ###    ######
             ##  ########################  ##
            ##         ###      ###         ##
            ##           ##    ##           ##
            ##             ####             ##
            ##             ####             ##
            ##          ###    ###          ##
             ###     ####        ####     ###
               ######                ######

                  Welcome to React Native!
                 Learn once, write anywhere

✔ Downloading template
✔ Copying template
✔ Processing template
✔ Installing dependencies
~~~

### Run react native application

생성이 완료된 프로젝트에서 `npx react-native run-android`를 실행한다.

애플리케이션의 빌드는 성공하였으나, `adb reverse`가 실패하여 에뮬레이션에는 애플리케이션이 정상적으로 동작하지 않는다.

~~~
# cd ./HelloReactNative
# npx react-native run-android
info Running jetifier to migrate libraries to AndroidX. You can disable it using "--no-jetifier" flag.
Jetifier found 870 file(s) to forward-jetify. Using 12 workers...
info Starting JS server...
info Installing the app...

> Task :app:installDebug
Installing APK 'app-debug.apk' on 'Android SDK built for x86 - 8.1.0' for app:debug
Installed on 1 device.

BUILD SUCCESSFUL in 7s
31 actionable tasks: 3 executed, 28 up-to-date
info Connecting to the development server...
adb: error: more than one device/emulator
warn Failed to connect to development server using "adb reverse": Command failed: /opt/android/platform-tools/adb -s host.docker.internal:5555 reverse tcp:8081 tcp:8081
info Starting the app on "host.docker.internal:5555"...
Starting: Intent { cmp=com.helloreactnative/.MainActivity }
~~~

원래라면 `adb reverse`를 통해 8081 포트로 Metro와 통신을 해야 하지만, 현재 환경은 `adb connect`를 통해 USB로 연결한 상태가 아니므로 React Native 애플리케이션의 개발자 메뉴를 사용해야 한다.

[React Native 문서](https://reactnative.dev/docs/running-on-device#method-2-connect-via-wi-fi-1){:target="_blank"}에서 설명한 바와 같이 `in-app Developer menu`를 통해 서버의 주소와 포트를 설정한다. Android 에뮬레이터를 사용하고 있으므로, 에뮬레이터를 구동하는 호스트를 가리키는 `10.0.2.2`를 IP 주소로 설정하고 Metro 포트인 `8081`을 포트로 설정한다.

React Native 애플리케이션 화면에서 `Ctrl + M`을 입력한 뒤 아래 순서와 같이 서버의 주소를 설정한다.

1. Ctrl + M 입력
2. React Native DevMenu > Settings
3. Debugging > Debug server host & port for device
4. 10.0.2.2:8081 입력
5. 설정 저장

애플리케이션에서 서버의 주소를 설정한 다음 `npx react-native start` 명령을 통해 Metro를 실행시킨다.

~~~
# npx react-native start

                        #######
                   ################
                #########     #########
            #########             ##########
        #########        ######        #########
       ##########################################
      #####      #####################       #####
      #####          ##############          #####
      #####    ###       ######       ###    #####
      #####    #######            #######    #####
      #####    ###########    ###########    #####
      #####    ##########################    #####
      #####    ##########################    #####
      #####      ######################     ######
       ######        #############        #######
         #########        ####       #########
              #########          #########
                  ######### #########
                       #########

                    Welcome to Metro!
              Fast - Scalable - Integrated

To reload the app press "r"
To open developer menu press "d"
~~~

Metro를 실행시킨 터미널을 유지한 채 새로운 터미널에서 이전에 실패한 `npx react-native run-android` 명령을 다시 실행한다. 이전과 동일하게 `adb reverse` 오류 메시지가 출력되나 에뮬레이터에는 정상적으로 화면이 나타난다.

## References

[react-native-community/docker-android - GitHub](https://github.com/react-native-community/docker-android){:target="_blank"}

[Setting up the development environment - React Native](https://reactnative.dev/docs/environment-setup){:target="_blank"}

[Running On Device - React Native](https://reactnative.dev/docs/running-on-device)

[Networking features in Docker Desktop for Windows - Docker Documentation](https://docs.docker.com/desktop/windows/networking/){:target="_blank"}

[Set up Android Emulator networking - Android Developers](https://developer.android.com/studio/run/emulator-networking){:target="_blank"}

[why do we use 10.0.2.2 to connect to local web server instead of using computer ip address in android client - Stack Overflow](https://stackoverflow.com/questions/9808560/why-do-we-use-10-0-2-2-to-connect-to-local-web-server-instead-of-using-computer){:target="_blank"}

[Android Debug Bridge (adb) - Android Developers](https://developer.android.com/studio/command-line/adb){:target="_blank"}