# React Plus Native App

This repository contains two applications:

- A Next.js web app in the repository root.
- A React Native app in `mobile/` for iOS and Android.

## Requirements

- Node.js 22.11 or newer
- npm
- macOS for iOS development
- Xcode and an iOS Simulator for iOS development
- Android Studio, Android SDK, and an Android Emulator for Android development

## Install dependencies

Install the web app dependencies from the repository root:

```sh
npm install
```

Install the React Native dependencies:

```sh
cd mobile
npm install
```

## Run the web app

From the repository root:

```sh
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in a browser. Edit files in `app/` to update the web app.

Other web commands:

```sh
npm run lint
npm run build
npm run start
```

`npm run start` serves the production build, so run `npm run build` first.

## Run the iOS app

Install the iOS native dependencies once:

```sh
cd mobile
bundle install
bundle exec pod install --project-directory=ios
```

Make sure an iOS Simulator is installed and available in Xcode, then launch the app:

```sh
cd mobile
npm run ios
```

To select a specific simulator:

```sh
npm run ios -- --simulator="iPhone 18 Pro"
```

To run Metro separately with a clean cache:

```sh
cd mobile
npm start -- --reset-cache
```

Then run `npm run ios -- --no-packager` in another terminal.

## Run the Android app

Start an Android Emulator from Android Studio, or start an available AVD from the terminal. For this project, the configured AVD is `Pixel_10_Pro`:

```sh
export ANDROID_HOME="$HOME/Library/Android/sdk"
export ANDROID_SDK_ROOT="$ANDROID_HOME"
export PATH="$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$PATH"

emulator -avd Pixel_10_Pro
```

Wait for the emulator to finish booting, then run the app from another terminal:

```sh
cd mobile
npm run android
```

If the Android build reports `No space left on device`, free disk space on the Mac and retry. The first Android build can take several minutes.

## Test and lint the mobile app

From `mobile/`:

```sh
npm run lint
npm test
```

## Project structure

```text
app/                 Next.js web app routes and components
public/              Web app static assets
mobile/              React Native app
mobile/ios/          Xcode iOS project
mobile/android/      Gradle Android project
```

The web app and mobile app currently have separate dependency installations and build pipelines. Shared code can be added later in a workspace package if needed.
