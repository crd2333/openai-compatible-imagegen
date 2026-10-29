# Setup and Network Configuration

## Running Commands

Requires Python 3.12+. Use `python3` on macOS/Linux or `python` on Windows. Commands can run from any working directory when the script path is absolute.

Set `SkillDir` to the installed skill directory:

```bash
SkillDir="/absolute/path/to/openai-compatible-imagegen"
python3 "$SkillDir/scripts/imagegen.py" info
```

Windows PowerShell:

```powershell
$SkillDir = "C:/path/to/openai-compatible-imagegen"
python "$SkillDir/scripts/imagegen.py" info
```

## Authentication

Initialize configuration interactively with `scripts/quick-init.py`. To configure an environment-variable API key non-interactively:

```bash
python3 "$SkillDir/scripts/quick-init.py" --non-interactive --base-url "https://example.com/v1" --model "gpt-image-2" --auth-method env --api-key-env "OPENAI_API_KEY" --no-postprocess
```

Use `--postprocess` instead when local transparency processing is wanted. For other providers and model profiles, see [models](models.md). `imagegen.py init` copies the example configuration template.

Authentication fields in `auth.json`:

- `api_key`: a local API key.
- `api_key_env`: the name of an environment variable containing the key.

A non-placeholder `api_key` takes precedence over `api_key_env`. Keep credentials out of command arguments and version control; use `info` for a redacted configuration summary.

## Network Errors

`proxy.url` selects an HTTP proxy for API requests and returned-image downloads. It accepts a complete `http://` or `https://` URL without credentials, paths, queries, or fragments; SOCKS is unsupported. Do not expose the proxy URL.

If the configured proxy fails, stop and report the error. Do not retry through another proxy or a direct connection.

If a returned-image download fails with a TLS EOF while direct downloading is disabled, explain the two authorization choices:

- `--allow-direct-url-download`: direct image downloads for one command.
- `auth.json` field `url_download.proxy_mode="direct"`: persistent direct image downloads.

Enable either only with user approval. These controls affect returned-image downloads, not API requests. Download requests never receive API credentials.
