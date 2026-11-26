
name: Build ShahinDiagPlus APK

on:
  workflow_dispatch:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Extract source
        run: |
          mkdir source
          unzip -o *.zip -d source
          PROJECT_DIR=$(dirname "$(find source -name settings.gradle.kts -print -quit)")
          echo "PROJECT_DIR=$PROJECT_DIR" >> $GITHUB_ENV

      - name: Build APK
        run: |
          cd "$PROJECT_DIR"
          gradle assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: ShahinDiagPlus-APK
          path: ${{ env.PROJECT_DIR }}/app/build/outputs/apk/debug/*.apk
