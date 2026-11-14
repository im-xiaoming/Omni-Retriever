# The old `main` (before 2026-11-14)

On 2026-11-14 `main` was replaced by the merge of `htstep_lora` and `htstep-av-extract`. The old `main`
(`435ea5e`) is kept on the branch **`main-old`**. Its 13 commits that are not in the new `main` are listed
below; their changes are **not** in the new code.

## Inference as in training (`c5f8b33`, not carried over)

The largest change. It sets the inference preprocessing to what the model saw in training:

- frames keep their aspect ratio (no centre crop), at the clip's real fps (this sets the video time steps and the
  audio chunks of `av`), held at 336² px;
- the first 8 s of audio (not the centre);
- no filler prompt on the video path, audio slots not doubled for BEATs.

Files: `data/media.py`, `inference/encode.py`, `models/loader.py` (`InferenceConfig` defaults), `cli.py`.

R@1 on 300 OmniRetriever-Bench records (gallery 300), all 12 directions averaged:

| Setting | AVG-all R@1 |
|---|---|
| previous defaults (centre crop, processor frame size, 2 fps, centre audio) | 0.607 |
| as training (crop off, 224² px, clip fps, first 8 s audio, doubled audio, video filler) | 0.623 |
| ... without doubled audio | 0.641 |
| ... with centre crop | 0.609 |
| ... with 2 fps | 0.599 |
| ... with centre audio | 0.620 |
| ... without video filler | 0.633 |
| **old `main` defaults**: as training, no doubled audio, no video filler, 336² px | **0.656** |

## Other commits

| Commit | Content |
|---|---|
| `5aa938c` | README: description of the fork (fixes, setup, API, CLI) |
| `26bc6de` | `encode_av` fix, `scripts/patch_wave_rope.py` |
| `b1ce0ad` … `5ccde6d` (7 commits, 2026-09-10/11) | processor and encode fixes (`fix`, `fix processor`) |
| `060db39`, `bb773d6`, `435ea5e` | merge commits of PRs #1-#3 (`readme` branch) |

## Getting them back

```bash
git fetch origin main-old
git show main-old:README.md                     # the old README
git diff main main-old -- src/omniretriever     # code differences
git cherry-pick c5f8b33                         # conflicts in media.py, encode.py, loader.py, cli.py: resolve by hand
```
