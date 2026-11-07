name: APK bauen

on:
  workflow_dispatch:
  push:
    branches: [main, master]

permissions:
  contents: write

jobs:
  build:
    name: Debug-APK bauen
    runs-on: ubuntu-latest
    steps:
      - name: Quellcode holen
        uses: actions/checkout@v4
      - name: Projektordner bestimmen
        run: |
          if [ -f settings.gradle ]; then
            echo "PROJ=." >> "$GITHUB_ENV"
          else
            unzip -q -o ./*.zip
            DIR=$(dirname "$(find . -name settings.gradle -not -path './.git/*' | head -n 1)")
            echo "PROJ=$DIR" >> "$GITHUB_ENV"
          fi
      - name: JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'
      - name: Android-SDK
        uses: android-actions/setup-android@v3
      - name: Android-Plattform installieren
        run: sdkmanager 'platforms;android-35' 'build-tools;34.0.0'
      - name: Gradle 8.9
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'
      - name: APK bauen
        working-directory: ${{ env.PROJ }}
        run: gradle --no-daemon --stacktrace assembleDebug
      - name: APK als Artefakt speichern
        uses: actions/upload-artifact@v4
        with:
          name: ClickBotVision-debug-apk
          path: ${{ env.PROJ }}/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
          retention-days: 14
      - name: APK als Release bereitstellen
        uses: softprops/action-gh-release@v2
        with:
          tag_name: build-${{ github.run_number }}
          name: ClickBot Vision Build ${{ github.run_number }}
          files: ${{ env.PROJ }}/app/build/outputs/apk/debug/app-debug.apk
