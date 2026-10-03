# The Cost of Multitasking: AI-enabled monolith

A presenter-led Streamlit app about task switching and protected focus. The
presentation, content, data, graphics, navigation, authentication, and AI adapters
are deliberately contained in one `multitaskingdownsides.py`.

> **Evidence status:** Citations and chart values are intentionally marked as
> placeholders. Replace and verify them before presenting any value as a research
> finding.

## AI features

The password-protected **Presenter AI** workspace provides:

- Speaker-note generation for any presentation section
- Draft answers to audience questions
- Section-revision drafts
- Provider selection using only providers configured in Streamlit secrets

AI drafts never modify the live presentation. Prompts and section text are sent to
the selected external provider, so do not enter confidential workplace information.

## Files

```text
multitaskingdownsides.py                         # Complete application
requirements.txt              # Pinned Python packages
.streamlit/config.toml         # Visual theme and server settings
.streamlit/secrets.toml.example
.gitignore                     # Excludes local secrets
tests/test_integrity.py        # Static safety and structure checks
README.md
```

## Configure secrets

Do not commit `.streamlit/secrets.toml` to GitHub. For local use, create that file
from `.streamlit/secrets.toml.example`. For Community Cloud, paste the same TOML into
**App settings > Secrets**.

At minimum, configure the password and one provider:

```toml
APP_PASSWORD = "replace-with-a-strong-unique-password"

DEEPSEEK_API_KEY = "replace-with-your-api-key"
DEEPSEEK_MODEL = "replace-with-your-model-name"
```

Supported secret-name pairs:

| Provider | API key | Model |
|---|---|---|
| DeepSeek | `DEEPSEEK_API_KEY` | `DEEPSEEK_MODEL` |
| Kimi | `KIMI_API_KEY` | `KIMI_MODEL` |
| Qwen | `QWEN_API_KEY` | `QWEN_MODEL` |
| GLM | `GLM_API_KEY` | `GLM_MODEL` |
| MiniMax | `MINIMAX_API_KEY` | `MINIMAX_MODEL` |
| Mistral | `MISTRAL_API_KEY` | `MISTRAL_MODEL` |
| Cohere | `COHERE_API_KEY` | `COHERE_MODEL` |
| SEA-LION | `SEALION_API_KEY` | `SEALION_MODEL` |

Only fully configured providers appear in the app. Each default endpoint can be
overridden with a matching secret such as `GLM_ENDPOINT` or `MISTRAL_ENDPOINT`.

Qwen API keys are region-bound. The built-in default is the legacy international
endpoint, which Alibaba currently says remains functional. For a workspace-specific
deployment, add the full regional chat-completions endpoint:

```toml
QWEN_ENDPOINT = "https://YOUR_WORKSPACE_ID.ap-southeast-1.maas.aliyuncs.com/compatible-mode/v1/chat/completions"
```

## Security boundaries

- API keys are read only from `st.secrets` and sent server-to-server.
- Key values are never displayed, logged, cached, or included in error messages.
- Password comparison uses `hmac.compare_digest`.
- Authentication lasts for the current Streamlit session and includes a logout.
- Provider calls require HTTPS and use connection and read timeouts.
- The password gate is suitable for a private presenter utility, but it is not a
  substitute for organization-level identity, audit logs, or role-based access.
- The public presentation does not require a password. Only Presenter AI is gated.

If any unredacted key has ever been pasted into a public place, revoke and replace it
before deployment.

## Run locally

Use Python 3.12 or newer:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
streamlit run multitaskingdownsides.py
```

On Windows PowerShell, activate with `.venv\Scripts\Activate.ps1`.

## Deploy to Streamlit Community Cloud

1. Upload the contents of this folder to the root of a GitHub repository.
2. Confirm `.streamlit/secrets.toml` is not in the repository.
3. In Streamlit Community Cloud, create a new app from that repository.
4. Choose `multitaskingdownsides.py` as the entrypoint and Python 3.12 in Advanced settings.
5. Paste your real secret values into **App settings > Secrets**.
6. Deploy and test one provider at a time.

## Live verification checklist

1. Confirm all six presentation sections and Next/Previous navigation work.
2. Unlock Presenter AI with the configured password, then log out and confirm it locks.
3. Generate a short speaker-note draft with each configured provider.
4. Confirm an invalid model name produces a friendly error without showing a key.
5. Confirm the four presentation graphics render and the GIF loops.
6. Keep all placeholder warnings visible until citations and data are finalized.

## Local integrity checks

```bash
python -m compileall multitaskingdownsides.py tests
python -m unittest discover -s tests -v
```

The app intentionally has no automatic provider fallback. Selecting the provider
explicitly prevents an unexpected prompt from being sent to a different company.
