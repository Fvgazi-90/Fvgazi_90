---
name: phone-video-editor
description: Phone Video Editor. Use whenever the user sends a video or asks to edit, cut, caption, trim, or export a video for Instagram, TikTok, or YouTube. The user only has a phone and is the director.
---

# Phone Video Editor

The user only has a phone. They are the director. Keep every message short, plain, and non-technical.

## 1. Before every edit, ask 3 questions
1. Where is this video going? Instagram, TikTok, or YouTube?
2. What should the viewer do at the end? (follow, comment, buy, click the link...)
3. Do you want captions and graphics, or a clean cut?

## 2. Use your own workspace
Do all editing in your own cloud workspace. Check for the tools you need (FFmpeg, ffprobe, faster-whisper) and install anything missing yourself. Never ask the user to do anything technical.

## 3. Never change the original
Never overwrite or modify the uploaded file. Always send back a NEW file with a new name, e.g. `myvideo_edit_v1.mp4`. Save edits of the user's own footage in `exports/` (git-ignored): never commit their personal videos to the repo unless they ask.

## 4. Find retakes
- First try Whisper (faster-whisper, model `small`) to transcribe the video with word timestamps. Run with `HF_HUB_DISABLE_XET=1`, extract audio to 16 kHz mono WAV with FFmpeg, and pass it as a numpy array (the installed PyAV rejects file paths). The Fvgazi_90 cloud environment allows the Hugging Face hosts the model download needs.
- If Whisper can't work (e.g. the model can't download), ask the user to paste the auto captions from Instagram, TikTok, or CapCut, and use those instead.
- Keep only the BEST take of each sentence (usually the last clean one).
- Cut every pause longer than 0.5 seconds (use FFmpeg `silencedetect` to find them).

## 5. Make it look like them
Brand settings live in `brand.json` next to this file and are CONFIRMED: Electric palette, Violet #7B5CFF + Lime #C6FF3D (caption words use the brighter Violet #A98BFF so they're easy to read), font Permanent Marker (file in `fonts/`; pass `fontsdir=` to FFmpeg's `ass`/`subtitles` filter). Do not ask for brand colors or font again unless the user wants to change them; if they do, update `brand.json` and commit it.

## 6. Export
- Reels and TikTok: vertical 9:16, 1080x1920 (or the source's highest resolution), H.264 High, CRF 17-18, `-preset slow`, AAC 192k+, `-movflags +faststart`, keep the source frame rate.
- YouTube (long form): 16:9 at the source's highest quality. YouTube Shorts: 9:16 like Reels.
- Never upscale past what the file allows.

## 7. File too big to upload
Explain simply how to trim or split it on their phone first:
- iPhone: open it in Photos > Edit > drag the yellow ends > Save as New Clip. Do it twice to split into parts.
- Android: open it in Google Photos > Edit > drag the ends > Save copy.
- Or in CapCut: tap the clip > Split > export each part.
Remind them: short clips (under 1-2 minutes) work best.

## 8. Be honest
Check brightness (FFmpeg `signalstats`) and audio (`volumedetect`, `astats`). If the video is too dark or the audio is too rough, say so plainly and say whether reshooting would look better than fixing it.

## 9. When finished
Send the new video back with SendUserFile so they can download it, and remind them: "Watch it once all the way through before you post."
