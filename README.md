﻿#Android_battery_app

 PREREQUISITES (One-time setup)
1. ✅ Install Java Development Kit (JDK)
Download: https://www.oracle.com/java/technologies/javase-jdk11-downloads.html

Install it and set JAVA_HOME:

powershell
Copy
Edit
setx JAVA_HOME "C:\Program Files\Java\jdk-11"
setx PATH "%PATH%;%JAVA_HOME%\bin"
2. ✅ Install Android SDK & Platform Tools
Install Android Studio (for SDK manager and emulator)

Download: https://developer.android.com/studio

During install, select:

Android SDK

SDK Platform Tools

Emulator

Even though you're coding in VS Code, you still need Android Studio once to install SDK tools.

3. ✅ Install VS Code Extensions
Open VS Code and install:

✅ Android i18n

✅ Kotlin Language (or Java depending on your language)

✅ Debugger for Java

✅ Gradle Language Support

🏗️ CREATE AND SETUP YOUR ANDROID PROJECT
Step 1: Create the Android project
Open Terminal in VS Code (Ctrl + `) and run:

bash
Copy
Edit
mkdir BatteryAlarmApp
cd BatteryAlarmApp
Now create the project manually OR use Android Studio once to generate it (then open in VS Code). If you're using Kotlin:

bash
Copy
Edit
# OPTION 1: Use Android Studio to create a "Basic Activity" Kotlin project
# Then open the project folder in VS Code

# OPTION 2: Or copy a sample template project structure from GitHub or ChatGPT
Step 2: Open Android project in VS Code
Use: File > Open Folder and select your Android project directory.

🔄 PROJECT FLOW (In VS Code)
✔️ 1. Setup Manifest and Permissions
Open app/src/main/AndroidManifest.xml and add:

xml
Copy
Edit
<uses-permission android:name="android.permission.REQUEST_IGNORE_BATTERY_OPTIMIZATIONS" />
<uses-permission android:name="android.permission.PACKAGE_USAGE_STATS" tools:ignore="ProtectedPermissions" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
✔️ 2. Create Battery BroadcastReceiver
In MainActivity.kt or a new BatteryReceiver.kt, add:

kotlin
Copy
Edit
class BatteryReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        val level = intent?.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) ?: -1
        if (level >= 100) {
            val ringtone = RingtoneManager.getRingtone(
                context, RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION))
            ringtone.play()
        }
    }
}
Register it in code or manifest.

✔️ 3. Create Foreground Service
In BatteryMonitorService.kt:

kotlin
Copy
Edit
class BatteryMonitorService : Service() {
    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        createNotificationChannel()
        startForeground(1, buildNotification())
        registerReceiver(BatteryReceiver(), IntentFilter(Intent.ACTION_BATTERY_CHANGED))
        return START_STICKY
    }

    private fun buildNotification(): Notification {
        return NotificationCompat.Builder(this, "battery_channel")
            .setContentTitle("Monitoring Battery")
            .setSmallIcon(R.drawable.ic_battery)
            .build()
    }

    private fun createNotificationChannel() {
        val channel = NotificationChannel("battery_channel", "Battery Service",
            NotificationManager.IMPORTANCE_LOW)
        getSystemService(NotificationManager::class.java).createNotificationChannel(channel)
    }

    override fun onBind(intent: Intent?): IBinder? = null
}
Declare it in AndroidManifest.xml.

✔️ 4. Add Ringtone Picker (Optional)
In MainActivity.kt:

kotlin
Copy
Edit
val intent = Intent(RingtoneManager.ACTION_RINGTONE_PICKER)
startActivityForResult(intent, 999)
Handle onActivityResult to get the ringtone URI.

✔️ 5. Grant Usage Access Permission
For app battery usage stats, open usage stats:

kotlin
Copy
Edit
val intent = Intent(Settings.ACTION_USAGE_ACCESS_SETTINGS)
startActivity(intent)
Then use UsageStatsManager to get data.

▶️ BUILD & RUN THE APP
Option 1: Run with VS Code Terminal
bash
Copy
Edit
# Inside your project folder
.\gradlew assembleDebug
adb install app\build\outputs\apk\debug\app-debug.apk
Option 2: Run from Android Emulator
Run Android Emulator (via Android Studio or command line)

Install via ADB: adb install path/to/app-debug.apk

📁 Folder Structure
bash
Copy
Edit
BatteryAlarmApp/
├── app/
│   └── src/
│       └── main/
│           ├── AndroidManifest.xml
│           ├── java/com/yourdomain/batteryapp/
│           │   ├── MainActivity.kt
│           │   ├── BatteryReceiver.kt
│           │   └── BatteryMonitorService.kt
│           └── res/
│               └── layout/activity_main.xml
└── build.gradle
✅ Next Steps
Would you like me to:

📦 Generate a ready-to-run template project ZIP for this app?

🎨 Create a simple UI mockup or layout XML?

🎥 Provide video tutorials or references for each step?

Let me know how deep you want to go — happy to walk you through it!
