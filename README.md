<h2 align="center">Self Hosted Expo Updates Server</h2>

# Intro
Self Hosted Expo Update Server is ... a ready to use, battery included, Expo-compatible server to manage updates that you can host yourself in the cloud and have full control and visibility on the update cycle, including rollbacks!

I love the ability to push over-the-air updates with expo, it is a fantastic feature, but with great power comes great responsibility.
The console-only interface can be tricky, the risk of making mistakes is high (especially on ejected app with incompatible binaries), if you want to roll back you really need to know what you are doing. And a single mistake can have potentially devastating impact.

I have already made a simple helper library that I use in my expo projects to simplify the update setup on the mobile side, check out [expo-custom-updater](https://github.com/umbertoghio/expo-custom-updater)

This is my attempt to simplify my own life when dealing with updates on the server side, and hopefully it can be useful to you too!

Features:
* Manage multiple Expo Apps
* Manage multiple Versions and Release Channels
* Send expo updates securely to the server and decide later when / how to release to users
* Roll back to a previous update
* Get insight on how many client app downloaded the update, see your changes being released in realtime
* Get a ton of info on the update, including git branch, commit, package.json and app.json information
* Assisted app configuration with self-signed certificate generator.
* All from a simple Web interface

Monitor client updates in realtime

![ezgif com-gif-maker (1)](https://user-images.githubusercontent.com/25666241/188273081-d55da67d-0906-4348-bc0c-714286e8e812.gif)

A lot of useful information on every update
![image](https://user-images.githubusercontent.com/25666241/187002164-56841c80-27f1-4055-9fa2-f1efd6fe3cf7.png)

Details on dependencies to avoid incompatible updates
![image](https://user-images.githubusercontent.com/25666241/187002193-ee179043-545e-4c71-ba3d-762447688c27.png)

Roll Back to a previous update
![image](https://user-images.githubusercontent.com/25666241/187002214-eaaf68bf-9d17-44b8-afc9-dd27a0f861e0.png)

# Install / Setup

## Play around in Dev
If you have Docker installed you clone this project you and play around by running `yarn` in the root folder and then running `yarn dev:run` to start the Docker/development docker compose. Web credentials are admin/devserver (admin password is set in the docker-compose file). 

## Deploy on your server
If you use Docker you can find a production-ready docker-compose files under the Docker folder, just copy Docker/production on your server, set your secrets / credentials and you are ready to go. The two docker images are public and ready to go.
Explanation for the Environment settings is in the docker-compose file

Otherwise you can build from code, there's a NodeJS API server under the API folder and a React / Vite web project under the Web foder.

## Add your app
Use the web interface to add your application, just enter the expo slug name
![image](https://user-images.githubusercontent.com/25666241/187029334-a1748a96-97e1-4efc-af70-631cea61a152.png)


## Setup a script to publish updates
You can download the ready to use publish script or create your own, the script logic is simple: 
- use `expo publis --experimental-bundle` to generate an update
- Add app.json and package.json to the build folder
- Zip the build folder
- use curl to push the zip file to the server

![image](https://user-images.githubusercontent.com/25666241/187029353-9fb6dfe9-913d-4537-900f-673cf7d8e886.png)

## Generate Self Signed certificate and private key
In order to validate the update expo needs a certificate.pem inside your app and a private key on the update server.
Use the SERVER CONFIGURATION section to generate a new self-signed key, make sure to SAVE, then downlaod both key and back them up!
![image](https://user-images.githubusercontent.com/25666241/187003070-c348189d-b159-4cfd-9f03-3801ea7e9b40.png)

## Configure your app
It is necessary to configure your app.json with the provided keys from the APP CONFIGURATION section.
Make sure to have a runtime version specified.

If you are on an Ejected project you can run `expo prebuild` to autogenerate the code in android-manifest.xml and Expo.plist, otherwise use the provided settings in the APP CONFIGURATION with the autogenerated code.

## Build a new app
Updates don't work in dev / expo app, you need to build a new app to test the system. You can use the provided examples (Managed and Ejected) in the relative folders, in order to build it is necessary to do a regular `expo publish` first in order to generate the proper configuration on the Expo servers. The provided example app have a button to request and download an update if present, and another button to reload the app.
It is expected that an app will automatically do this operation on start, check out [expo-custom-updater](https://github.com/umbertoghio/expo-custom-updater)
Once the app pings the update server you will see an update appearing in the homepage, and after an update / download / reload cycle the updated app will reflect in the dashboar AFTER it asks for an update again.

![image](https://user-images.githubusercontent.com/25666241/187808093-743afc83-77c1-4ed0-afc8-bd1d9ed0691e.png)


## Publish and release an update
This part should be the simplest: call the provided script with appropiate parameters and see your update pop in the web UI.
Release, rollback or delete the updates and see the clients updating in the homepage.

## Monitor the update being downloaded by your client in realtime
Configure the throttle value in docker-compose Environment in order to slow down updates (in case you have thousands of users) to avoid overloads.
Default throttle is no more than one dashboard update every 5 seconds.

![image](https://user-images.githubusercontent.com/25666241/187808147-1c6fac7c-cc95-4fcf-a736-f059b00f83ef.png)


# Contribute
Feel free to clone, costomize and send back PRs!

Have fun!
