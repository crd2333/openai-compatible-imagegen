---
name: openai-compatible-imagegen
description: Generate, edit, and batch-process images with the bundled Python CLI and configured API providers. Use when the user requests this local OpenAI-compatible image workflow; respect an explicitly selected alternative tool or backend.
---

# OpenAI-Compatible Images

Use Python 3.12+ to run `scripts/imagegen.py`. The script reads `auth.json` from the skill directory. Command examples below use paths relative to that directory.

## Workflow

1. Run `info` before an API request to inspect configuration without exposing credentials. If setup is missing, read [setup](references/setup.md).
2. For a v2 configuration, run `list-models` and select a profile with `--profile`. Resolve user choices by exact profile ID, unique alias, or unique model ID; clarify ambiguous matches. Keep one-call choices in command flags rather than changing the configured default.
3. Choose `generate`, `edit`, or `batch`. Build the prompt from the user's subject, composition, style, text, and constraints. Preserve explicit choices; leave flags unset when configuration defaults should apply.
4. Run the command in the foreground and wait for completion. If interrupted or timed out, stop the launched process before continuing.
5. Report output paths, any batch manifest, and factual warnings or failures. Preserve valid API originals even when requested dimensions, count, format, or local processing requirements are unmet.

Use the bundled client rather than writing API calls inline. Let script validation and API responses determine request support; do not preemptively reject requests or substitute models, sizes, quality, or protocols. Apart from the configured native-transparency retry, another image request requires `transparency.llm_assisted.enabled` and `allow_api_retry`.

Never expose credentials or commit `auth.json`. Do not change proxy settings or enable direct downloads without user approval; read [setup](references/setup.md#network-errors) when a network error needs investigation.

## Common Commands

Inspect configuration:

```bash
python3 scripts/imagegen.py info
```

Generate an image:

```bash
python3 scripts/imagegen.py generate -p "Editorial still life of a ceramic tea set, soft window light, space for a headline on the left, no text" -f "outputs/tea-set.png"
```

Edit using a reference image (`-i` is repeatable):

```bash
python3 scripts/imagegen.py edit -p "Keep the subject and camera angle; replace the background with a neutral studio wall" -i "input.png" -f "outputs/studio-edit.png"
```

Run a JSONL batch:

```bash
python3 scripts/imagegen.py batch --input "prompts.jsonl" --out "outputs/imagegen" --concurrency 3
```

Use `--size` for exact API pixels or `--aspect` with `--resolution` for shape and resolution. Use `--delivery-size` for a separate local resize. An explicit output extension must match the resolved format.

For transparent output, add `--transparent` and read the transparency references below. Keep the base prompt semantic; the runtime adds route-specific background and alpha instructions. Local transforms and QA are opt-in unless required by transparent delivery.

## Read as Needed

| Task | Reference |
| --- | --- |
| Initial setup, authentication, platform commands, proxy or download errors | [Setup](references/setup.md) |
| Provider/model profiles and native JSON parameters | [Models](references/models.md) |
| Prompt construction or controlled batch variation | [Prompting](references/prompting.md) |
| Flags, defaults, size presets, JSONL fields and batch paths | [Parameters](references/parameters.md) |
| Transparency policy and route selection | [Transparency parameters](references/parameters.md#visual-deliverables-and-transparency) |
| Local transparency processing, resizing, grid splitting, previews or adjustment | [Post-processing](references/postprocess.md) |
| Technical inspection and delivery checks | [QA](references/qa.md) |

## Results

`ok=true` means at least one API original was published. `delivery_ready` separately reports whether requested transparency, transforms, and QA passed. Return available originals and successful derivatives even when delivery is incomplete; do not report technical QA as proof of semantic or aesthetic correctness. Batch results include `manifest.json`; report success/failure counts and short failure summaries.
