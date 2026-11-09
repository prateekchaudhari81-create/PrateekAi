name: Build Debug APK

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Unpack project.zip (if present)
        run: |
          if [ -f project.zip ]; then
            unzip -o project.zip
          else
            echo "project.zip not found; using files already in the repo"
          fi

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Set up Gradle 8.7
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: 8.7

      - name: Build debug APK
        run: gradle assembleDebug --stacktrace

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: PrateekAI-debug-apk
          path: app/build/outputs/apk/debug/*.apk

      - name: Run unit tests
        continue-on-error: true
        run: gradle testDebugUnitTest --stacktrace
