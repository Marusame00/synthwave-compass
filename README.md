# ⬡ Synthwave Compass ⬡

A retro 80s-inspired 3D compass web app that uses your phone's motion sensors to find magnetic north. Built with pure HTML, CSS, and JavaScript—no frameworks, no dependencies.

Built by [Marusame00](https://github.com/Marusame00) with AI assistance from [Abacus AI Agent](https://abacus.ai) — the app concept, the original HTML/CSS/JS, and the Android packaging were a collaboration between the developer and the agent stack.

![Synthwave Compass](https://img.shields.io/badge/style-synthwave-ff00ff?style=for-the-badge) ![Mobile Optimized](https://img.shields.io/badge/mobile-optimized-00ffff?style=for-the-badge) ![No Dependencies](https://img.shields.io/badge/dependencies-none-success?style=for-the-badge)

## ✨ Features

- 🧭 **Real-time compass** using device orientation sensors
- 🎨 **Synthwave aesthetic** with neon pink/cyan color scheme
- 📱 **Mobile-optimized** for vertical phone displays
- 🔮 **3D effects** with pulsing rings and glowing elements
- 📊 **Debug readout** showing raw sensor data (alpha/beta/gamma)
- 🎯 **Cardinal directions** (N/E/S/W) with color-coded labels
- ⚡ **Zero dependencies** - just one HTML file

## 🚀 Live Demo

Visit the live app: 
```text
https://marusame00.github.io/synthwave-compass/
```


## 📱 Usage

1. Open the app on your phone (works best on mobile devices with motion sensors)
2. Tap **ACTIVATE** to enable motion sensors
3. Grant permission if prompted (iOS only)
4. Move/rotate your device to see the compass needle track magnetic north
5. Check the bottom-left debug panel to see raw sensor values

### Browser Compatibility

**Recommended browsers:**
- ✅ Chrome (Android/iOS)
- ✅ Safari (iOS)
- ✅ Brave (with motion sensors enabled)
- ✅ Firefox (Android/iOS)

**Requirements:**
- Must be served over **HTTPS** (or localhost for development)
- Device must have magnetometer/orientation sensors
- Motion sensor permissions must be granted

## 🛠️ Development

### Local Testing

To test locally with sensor support:

**Option 1: GitHub Pages (recommended)**
- Fork this repo
- Enable GitHub Pages in Settings
- Access via the HTTPS URL

**Option 2: Local HTTPS server**
```bash
# Using Python 3
python -m http.server 8080

# Then access via http://localhost:8080
```

**Note:** Modern browsers require HTTPS (or localhost) to access motion sensors. Using a LAN IP like `http://192.168.x.x` will cause sensors to be blocked.

### File Structure

```
synthwave-compass/
├── index.html          # Complete app (HTML + CSS + JS)
└── README.md          # This file
```

That's it! The entire app is self-contained in one HTML file.

## 🎨 Customization

### Change Colors

Edit the CSS variables in the `<style>` section:

```css
/* Primary neon colors */
#ff00ff  /* Neon pink/magenta */
#00ffff  /* Neon cyan */
#ffff00  /* Neon yellow */
#ff8800  /* Neon orange */
```

### Adjust Compass Size

Find `.compass-wrap` in the CSS:

```css
.compass-wrap {
  width: min(75vw, 300px);   /* Change 300px to desired max size */
  height: min(75vw, 300px);
}
```

### Modify Needle Sensitivity

In the JavaScript, adjust the transition speed:

```css
.needle {
  transition: transform 0.15s ease-out;  /* Change 0.15s for faster/slower */
}
```

## 🐛 Troubleshooting

### "NO SENSOR DATA" message

**Possible causes:**
1. **Not using HTTPS** - Sensors require secure context
   - ✅ Solution: Use GitHub Pages or localhost
2. **Browser blocking sensors** - Check browser settings
   - ✅ Brave: Enable "Motion sensors" in Site Settings
   - ✅ Chrome: Check site permissions (lock icon in address bar)
3. **Device has no sensors** - Some tablets/laptops lack magnetometers
   - ✅ Solution: Test on a phone with compass capability

### Compass not pointing north accurately

- **Calibrate your device's compass:**
  - Android: Move phone in a figure-8 pattern
  - iOS: Settings → Privacy → Location Services → System Services → Compass Calibration
- **Magnetic interference:** Move away from metal objects, electronics, or magnets

### Sensors work on demo sites but not this app

- Ensure you're accessing via **HTTPS** (not `http://` or `file://`)
- Check that motion sensors are allowed for your specific domain
- Try clearing browser cache and reloading

## 📋 Technical Details

### Sensor Data

The app uses the **DeviceOrientationEvent** API:

- **Alpha (α):** Rotation around Z-axis (0-360°) - used for compass heading
- **Beta (β):** Rotation around X-axis (-180 to 180°) - tilt forward/back
- **Gamma (γ):** Rotation around Y-axis (-90 to 90°) - tilt left/right

**Heading calculation:**
```javascript
heading = (360 - alpha) % 360
```

This inverts the alpha value so 0° = North and increases clockwise (standard compass convention).

### Browser Support Details

| Feature | Chrome | Safari | Firefox | Brave |
|---------|--------|--------|---------|-------|
| DeviceOrientation | ✅ | ✅ | ✅ | ✅* |
| HTTPS Required | ✅ | ✅ | ✅ | ✅ |
| Permission Prompt | Android: Auto<br>iOS: Manual | Manual | Auto | Manual* |

*Brave requires manual permission via site settings (no JS API to request)

## 🎯 Use Cases

- 🏕️ **Outdoor navigation** - Quick compass when hiking/camping
- 🧭 **Orientation reference** - Check which way you're facing
- 📱 **Sensor testing** - Verify device motion sensors work
- 🎨 **Design inspiration** - Synthwave/retro UI reference
- 🎓 **Learning tool** - Study DeviceOrientation API usage

## 🔒 Privacy

- **No data collection** - Everything runs locally in your browser
- **No analytics** - No tracking scripts or third-party services
- **No network requests** - Works completely offline after initial load
- **Sensor data stays local** - Never transmitted anywhere

## 📱 Android App (APK)

The same compass is packaged as a native Android app — a WebView loading the page straight from the APK assets.

### 📂 Project layout

| Path | What it is |
| --- | --- |
| `index.html` | Original browser version (fully self-contained, zero CDNs) |
| `www/` | Offline bundle consumed by the Android app (`index.html` + a small sensor-bridge shim) |
| `android/` | Native Android project that packages `www/` into an installable APK |

### ⚠️ WebView sensor bridge (important for editing)

The Android WebView **does not fire the `deviceorientation` event**, so the compass cannot rely on the browser API inside the app. `MainActivity` reads Android's fused **rotation vector** sensor (gyroscope + accelerometer + magnetometer via `SensorManager`) and pushes the azimuth into the page via `window.androidBridgeAzimuth(alpha)` (defined in `www/index.html`), which re-dispatches it as a synthetic `deviceorientation` event. The rest of the page logic is untouched.

**If you edit the sensor code, keep the contract intact:**

- `window.androidBridgeAzimuth(deg)` must exist and dispatch a `deviceorientation` event with `alpha` set
- The page adds `+180°` to `alpha` (`rawHeading = (e.alpha + 180) % 360`) to correct for devices reporting north as south — the native side feeds `alpha = heading + 180` so the displayed heading reads true. Do not "fix" one side without the other.

GPS works via `navigator.geolocation`; location permission is requested on launch, and the WebView grants the JS geolocation prompt automatically.

### 📲 Install on an Android phone

1. On your phone, open: **github.com/Marusame00/synthwave-compass → Releases → latest**
2. Tap the downloaded `SynthwaveCompass-X.Y.apk`
3. If prompted, allow installing from your browser ("Allow from this source")
4. Tap **Install anyway** when Android warns about an unknown source — it is a debug-signed build, which is expected
5. Open **Synthwave Compass** from your app drawer, tap **ACTIVATE**, and grant location when asked

> Prefer ADB? Enable **Settings → Developer options → USB debugging / Wireless debugging**, then from a PC with the Android platform-tools:
>
> ```powershell
> adb install -r SynthwaveCompass-1.0.apk
> ```

### 🛠 Build the APK yourself

Requires **Git, Android Studio (which includes the Android SDK), and a JDK** (Android Studio bundles JBR).

```powershell
git clone https://github.com/Marusame00/synthwave-compass.git
cd synthwave-compass\android
.\gradlew.bat :app:assembleDebug
```

The APK is named after the version (e.g. `SynthwaveCompass-1.0.apk`) and lands in `app\dist\` (AGP's stock `app-debug.apk` stays in `app\build\outputs\apk\debug\`). Or just open the `android` folder in **Android Studio** and press **Run**.

> **Packaging pitfall:** don't rename the APK by moving `app-debug.apk` in a `doLast` hook on `packageDebug` (as older templates did) — AGP's incremental packager tracks that file, and when it goes missing, the next *asset-only* incremental build produces a corrupt APK containing just the changed file. The `renameDebugApk` task here works by **copying** the stock output into `app\dist\`, which keeps AGP's tracked output intact and incremental builds healthy.

New versions: bump `versionCode` and `versionName` in `android\app\build.gradle`, rebuild, and publish a tagged GitHub Release with the new APK attached. Signing for Play Store distribution needs a release keystore (Build → Generate Signed App Bundle / APK).

## 📄 License

MIT License - feel free to use, modify, and distribute.

## 🙏 Credits

Built as a pure HTML "vibe coding" experiment. Inspired by:
- 80s synthwave aesthetic
- Retro sci-fi UI design
- The need for a simple, beautiful compass app

## 🤝 Contributing

Found a bug or want to add a feature?

1. Fork the repo
2. Make your changes to `index.html`
3. Test on mobile devices
4. Submit a pull request

**Ideas for contributions:**
- [ ] Add compass rose tick marks
- [ ] Implement geolocation for true north vs magnetic north
- [ ] Add sound effects (synthwave beeps!)
- [ ] Create alternate color themes
- [ ] Add "lock heading" feature
- [ ] Implement landscape mode layout

## 📞 Support

Having issues? Check the **Troubleshooting** section above or open an issue on GitHub.
Vibe Coded with abacus.ai
---

**Made with 💜 and neon dreams**

*Best viewed on mobile devices with motion sensors enabled*
