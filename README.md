# taqeem-abuamr
تطبيق تعليمي عن أساسيات الكهرباء
name: Build APK

on:
  push:
    branches:
      - main
      - feature/professional-ui
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"
          cache: gradle

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Install Android SDK packages
        shell: bash
        run: |
          yes | sdkmanager --install "platform-tools" "platforms;android-34" "build-tools;34.0.0"
          yes | sdkmanager --licenses >/dev/null

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v3

      - name: Grant execute permission for gradlew
        run: chmod +x ./gradlew

      - name: Build debug APK
        run: ./gradlew assembleDebug --no-daemon

      - name: Upload APK artifact
        uses: actions/upload-artifact@v4
        with:
          name: MotorControlApp-debug-apk
          path: app/build/outputs/apk/debug/*.apk
          if-no-files-found: error