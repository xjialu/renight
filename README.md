# RENight 2026: show backdrop

- `RENight-2026.html`: the show. Open it in Chrome (double-click works). Use this one on the night; the mic visualiser works here.
- `programme.html`: the same code without the page wrapper. This is the artifact source; edit this, then rewrap it to update the show file.
- `rsigma.svg`: the R-Sigma logo used top right.

Everything (engine, scenes, styles, logo) lives in one file. Fonts load from Google Fonts, so open the show once while online so Chrome keeps them.

Keys: → / click next · ← back · Space timer or song · F fullscreen · B blank or visualiser · V mic · M sound · ] / [ running late ±5 min · G go to scene · S setup and pre-show check · D fonts and tuning · H help · 0 0 restart · 9 9 end.

Microphone: the show asks for the mic only when you press V, or click Test microphone in Setup (S). It never asks on open. Double-clicking opens the file in your default browser, which may not be Chrome. To be sure, right-click the file and choose Open With, then Google Chrome. If the mic is refused, the pre-show check says why.

Prize photos: all 10 prizes have a built-in photo, matched by prize number. A photo added in Setup replaces it, and Use built-in goes back. If you reorder the prize list, re-add photos to match.

Settings, winners and prize photos are saved in the browser, so rehearse and run the show on the same laptop and browser.

## Family Feud — Game Two

Game Two now contains its opening timer followed by **10 question slides**, each with **three answers**. The ten questions and their ranked answers are filled in; points are blank. The original fonts, colours, dither engine, split-flap tiles, logo and scene transitions are reused.

- Press **S** to edit questions, the top three answers and optional points under **Family Feud**, then **Save**. Editing is only visible in the operator panel.
- Press **1 / 2 / 3**, or click an answer row, to reveal or hide that answer independently.
- Press **X** for an incorrect answer: a large red cross appears with the Family Feud buzzer, without changing revealed answers or advancing the question. The cross lasts for the clip. **Esc** dismisses it and stops the sound early. **M** mutes/unmutes sound.
- **→ / Space / click the backdrop** reveals the next covered answer, then moves to the next question. **←** hides an answer before stepping back.
- **Shift + → / ←** skips directly between questions. **G** jumps to any question or reveal step. **F** enters fullscreen.
- After question 10, the full show continues to the **Montage**. Game One is unchanged.

For a local preview, run `python3 -m http.server 8765 --bind 127.0.0.1` in this folder:

- Full programme, starting at Game Two: <http://127.0.0.1:8765/RENight-2026.html?game=2>
- Full programme, starting at question 1: <http://127.0.0.1:8765/RENight-2026.html?game=2&question=1>
- Only the 10 question slides: <http://127.0.0.1:8765/RENight-2026.html?deck=family-feud&question=1>

The two views share question edits in the same browser and origin, while remembering their positions separately. The [ClassPoint PDF](https://cdn.classpoint.io/wp-content/uploads/Family-Feud-Questions-and-Answers-PDF-by-ClassPoint.pdf) was the original question-format reference; the current questions and answers were supplied by the organiser.

The [Family Feud incorrect buzzer clip from Quick Sounds](https://quicksounds.com/sound/24477/family-feud-incorrect-buzzer) is embedded as MP3 data in the HTML, so it does not need an internet connection. [Source download](https://quicksounds.com/uploads/tracks/1215347886_1411154259_501325313.mp3); source lists the [Quick Sounds Standard License](https://quicksounds.com/page/license-agreement).
