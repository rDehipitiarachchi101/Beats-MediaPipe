# Lean Tracker (MediaPipe Pose)

A single web page (`index.html`) that runs MediaPipe Pose Landmarker in the browser
and reports trunk lean angles. It runs entirely on-device (no server-side processing),
in Chrome on a Mac now and in Chrome on the Pixel later.

## What it measures

| Reading | How it's computed | Best camera placement |
|---|---|---|
| **Lateral lean** | Angle of hip-midpoint → shoulder-midpoint vs. image vertical (2D) | Camera facing the subject (front/back view) |
| **Fwd/back lean (3D est.)** | Same vector in MediaPipe's 3D world landmarks, sagittal plane | Rough only: depth comes from one camera |
| **Shoulder tilt** | Angle of the left–right shoulder line | Front view |

For accurate forward/back lean, film the subject **from the side**. The *lateral* reading
then becomes the forward/back angle, because it is measured in the image plane.

**Zero (calibrate)** sets the current posture to 0°. **Record** logs every frame, and
**Download CSV** saves it. You can also **Open video…** to analyze a recorded clip.

## Run it on the Mac (now)

```bash
cd ~/Documents/AnimalLab/MediaPipe && python3 -m http.server 8000
```

Open http://localhost:8000 in Chrome and click **Start camera**.

## Run it on the Pixel (later)

The browser only allows camera access on `https://` or `localhost`. Choose one option:

**Option A: USB + adb (simplest, private)**
1. On the Pixel: Settings → About phone → tap *Build number* 7× → Developer options → enable **USB debugging**.
2. On the Mac: `brew install android-platform-tools`
3. Plug in the phone, accept the prompt, then run:
   ```bash
   adb reverse tcp:8000 tcp:8000
   ```
4. With the Mac server running, open `http://localhost:8000` in Chrome **on the Pixel**.

**Option B: Host it on HTTPS** (GitHub Pages, Vercel, etc.). Then open the URL on the phone.
This also works with no cable.

**Option C: Video files.** Record on the Pixel's normal camera app, then use *Open video…*.
This gives you the best resolution and frame rate, and you can re-analyze clips later.

## Tuning

- In `index.html`, change `pose_landmarker_lite` to `pose_landmarker_full` or `_heavy` for better accuracy at lower FPS.
- `ALPHA` sets the smoothing (1 = raw, smaller = smoother).
- Ignore frames where visibility < ~0.5. They are flagged orange.

## Going native later

If you need higher FPS, background recording or sensor fusion (e.g. the Pixel's IMU),
the same model runs in an Android app through the MediaPipe Tasks `PoseLandmarker` API
(`com.google.mediapipe:tasks-vision`). The lean math in `computeAngles()` carries over directly.
