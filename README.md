# gk2019-cordova

## Setup:
1. add `google-services.json`
1. run
	>`cordova prepare`
1. Apply fixes (see [FIXES.md](https://github.com/suryavip/gk2019-cordova/blob/master/FIXES.md))

## Before build:
1. update app version on `config.xml`
1. get fresh copy of `gk2019` folder into `www` folder
1. remove unnecessary and git related files from `www/gk2019`
1. cleanup `platforms/android/app/build/outputs/apk` `debug` and `release` folder

## Build release steps:
1. build
	> `cordova build --release android`
1. align
	> `& "$Env:ANDROID_SDK_ROOT\build-tools\29.0.2\zipalign" -v 4 app-release-unsigned.apk app-release-unsigned-aligned.apk`
1. create keystore if needed, or supply it
	> `keytool -genkey -v -keystore keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias suryavip`
1. sign and verify
	> `& "$Env:ANDROID_SDK_ROOT\build-tools\29.0.2\apksigner" sign --ks keystore.jks --out app-release.apk app-release-unsigned-aligned.apk`
	
	> `& "$Env:ANDROID_SDK_ROOT\build-tools\29.0.2\apksigner" verify app-release.apk`

## After build checklist:
1. apk version
1. web appVersion
1. push notification
1. `mailto:` intent
1. take a photo using camera
1. file picker
	1. single image
	1. multiple image
	1. multiple file
1. sharing link
1. dynamic and deep link