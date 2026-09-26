# Prayer alert sounds

The app reads `sounds.json` and offers every entry in its sound picker. A sound
is downloaded to the phone the first time someone picks it. `adhan_1_short` is
bundled in the app and is not listed here.

## Adding a sound

1. Put the Android file here, e.g. `adhan_3.ogg`.
2. Make the iOS file. iOS cannot play `.ogg` in notifications and plays the
   default tone instead of any sound longer than 30 seconds, so trim it to 29s:

   ```
   ffmpeg -i adhan_3.ogg -t 29 -ac 1 -c:a adpcm_ima_qt ios/adhan_3.caf
   ```

3. Get the lengths in seconds, rounded up (the app uses the Android length to
   know when the adhan has finished before it silences the phone):

   ```
   ffmpeg -i adhan_3.ogg      # Duration: 00:04:33.03  -> 274
   ffmpeg -i ios/adhan_3.caf  # Duration: 00:00:29.00  -> 29
   ```

4. Add an entry to `sounds.json`:

   ```json
   {
     "id": "adhan_3",
     "version": 1,
     "type": "adhan",
     "names": { "en": "...", "ta": "...", "ar": "...", "si": "..." },
     "android": "adhan_3.ogg",
     "androidDurationSeconds": 274,
     "ios": "ios/adhan_3.caf",
     "iosDurationSeconds": 29
   }
   ```

   - `id`: letters, digits and `_` only. Never reuse or rename an id; people's
     saved choice points at it.
   - `type`: `adhan` or `tone`. If the file is not on the phone yet, an adhan
     falls back to the bundled short adhan and a tone to the default sound.
   - `names`: `en` is required; missing languages fall back to it.

5. Commit and push. The app picks it up the next time the picker opens.

## Replacing a sound

Upload the new file under the same name and increase `version`. Phones that
already downloaded it fetch the new file the next time the app starts.
