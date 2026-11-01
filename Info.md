**Note: Most of this file was wrote by Claude Sonnet 5.5 and I chatted about some more random stuff, so some other info stuff is here also**
I also have no coding skills, I'm just a person that founded fogcam via a video by Left Running on YouTube.

# FogCam archive (2026)

Raw, non-watermarked frames from [fogcam.org](https://fogcam.org), saved automatically by GitHub Actions. Capture started 2026-10-31, about 6 am PDT. Info last updated 2026-11-01.

## Where the frames are

All frames live under **Releases**, not in the repo files.

- One release per day, named `frames-YYYY-MM-DD`.
- One zip per clock hour inside it, named `fogcam_YYYY-MM-DD_HHh_from-HH-MM-SS.zip`.
- Inside each zip, frames sit in folders: `YYYY-MM/YYYY-MM-DD/HH/`.
- Each frame is named `fogcam_YYYY-MM-DD_HH-MM-SS.jpg`.

Example path inside a zip:

```
2026-11/2026-11-01/09/fogcam_2026-11-01_09-00-03.jpg
```

Notes:

- All names use San Francisco time (America/Los_Angeles). A day runs midnight to midnight there.
- The `from-` part of a zip name is the time of its first frame. If a run hands off mid-hour, that hour can come as two zips, and the `from-` time tells them apart.
- Zips uploaded before the folder layout was added have loose files and no folders.
- The repo's `frames/` folder, `.hashes` file and README from earlier versions are leftovers from test runs. The current version does not write to them.

## How it works

The workflow is `.github/workflows/capture.yml`.

1. A run lasts about 5 hours 40 minutes (the job limit is 355 minutes, so it stops before GitHub's 6-hour cutoff).
2. Every 15 seconds it downloads `fogcam.org/fogcam2.jpg` with a unique `?t=` number and no-cache headers.
3. It hashes each frame (SHA-256) and throws away exact duplicates.
4. New frames go into month/day/hour folders. Each hour's folder is zipped and uploaded to that day's release after the hour ends.
5. Near the end, the run starts the next run itself with `gh workflow run capture.yml`, uploads what is left, and finishes.
6. A backup schedule (`0 */6 * * *`, so 00:00, 06:00, 12:00 and 18:00 UTC) starts a run if the hand-off ever fails. Only one run is allowed at a time, so a waiting scheduled run shows as "cancelled". That is normal.

The duplicate check only remembers frames from the current run. A frame that repeats right after a hand-off can be saved twice.

## Starting and fixing it

- To start a run: Actions, then **capture**, then **Run workflow**. If you see a "disabled" banner, tap **Enable workflow** first.
- Do not use **Re-run** on an old run. It uses the old copy of the workflow file.
- Cancelling a run loses up to an hour of frames, because those are still waiting to be zipped. Cancel right after a new zip appears in Releases.
- Editing `capture.yml` while a run is going is safe. The running job keeps the old file, and the next run uses the new one.
- Settings in the file: `sleep 15` is the check interval, and the `curl` line has the URL and headers.
- The first version pushed frames into the repo and failed with "rejected (fetch first)" when the repo changed during a run. The current version never pushes to the repo, so this can't happen.

## What to expect

- **Frame size:** about 62 KB each.
- **How often FogCam updates:** 20 to 60 seconds per FogCam and fog-bank, uneven and sometimes under 10 seconds. In 2019 it was steadier, about every 21 seconds.
- **Frames per day in 2026:** fog-bank's archive page shows about 1,200 to 1,800 on full days, which is roughly 75 to 110 MB a day and 2.5 to 3.3 GB a month.
- **Filename times** are when the frame was fetched. That can be up to about 15 seconds after the time burned into the picture, so trust the clock in the image.
- **Gaps:** a hand-off leaves a gap of a minute or less. FogCam itself sometimes freezes for hours (fog-bank lists a 64-hour freeze from Sep 8 to 10).
- **Missed frames are possible.** With a 15-second check, two updates less than about 15 seconds apart can lose one.
- **GitHub limits:** a release file can be up to 2 GB, and a release can hold up to 1,000 files, so about 24 zips a day is fine. As I remember GitHub's terms, a non-stop loop like this may not be what Actions is meant for, so download the zips now and then and keep your own copy. GitHub may also disable scheduled workflows in a public repo after 60 days of no activity.

## Related

- **fog-bank.org** is a separate archive of FogCam with watermarked frames, its own catchers, and a contribute form for frames it lacks. It lists Aug 7 and 8, 2019 as nearly empty.
- **archive.org/details/fogcam19280423** is a 2019 zip (about 64 MB, roughly 900 to 1000 frames, filenames in UNIX seconds) that looks like it fills that gap. It was sent to fog-bank for review.
- _extra note:_ I tried to find days/frames that Fog-bank didn't have, and surprisingly, I founded those 900-1000 frames that wasn't in Fog-bank already. So.
- **Analemma idea:** a figure 8 needs the same time every day for a full year. At 7:30 pm PDT (02:30 UTC) the figure is thin, tilted on its side, and only the upper loop is above the horizon, roughly Mar 31 to Sep 5, so it can't show the whole 8. The 4 pm PDT figure is up all year but sits in the southwest and may be outside the camera's view. Use a fixed UTC time so daylight saving doesn't shift it.
- _extra note:_ tested this, didn't really work cuz clouds, whatever.
