name: "ShabihSaz - Remote Browser on GitHub"

on:
  workflow_dispatch:  # با یک کلیک اجرا می‌شود
    inputs:
      url:
        description: 'آدرس سایت مورد نظر (مثلا https://youtube.com)'
        required: true
        type: string
        default: 'https://youtube.com'

jobs:
  browse-website:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Java (برای شبیه‌ساز)
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'
      
      - name: Setup Android Emulator
        uses: reactivecircus/android-emulator-runner@v2
        with:
          api-level: 30
          target: google_apis
          arch: x86_64
          profile: Nexus 6
          emulator-options: -no-window -no-audio -no-boot-anim
          script: |
            # منتظر بوت کامل شبیه‌ساز
            adb wait-for-device
            adb shell svc wifi enable
            
            # باز کردن مرورگر در آدرس مورد نظر
            URL="${{ github.event.inputs.url }}"
            adb shell am start -a android.intent.action.VIEW -d "$URL"
            
            # صبر برای لود کامل صفحه (۱۰ ثانیه)
            sleep 10
            
            # گرفتن اسکرین‌شات
            adb exec-out screencap -p > /tmp/screenshot.png
            
            # گرفتن اطلاعات صفحه با JavaScript (از طریق Remote Debugging)
            adb forward tcp:9222 tcp:9222
            
            # ذخیره نتیجه در Artifact
            mkdir -p results
            cp /tmp/screenshot.png results/
            echo "URL: $URL" > results/info.txt
            date >> results/info.txt
      
      - name: Upload Results
        uses: actions/upload-artifact@v4
        with:
          name: browser-output
          path: results/
          retention-days: 7
