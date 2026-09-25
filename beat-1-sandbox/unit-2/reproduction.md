# Unit 2 — Claim and Reproduce

## Your identity upstream

**GitHub username**

Sangeetha229

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5821512740

I’d like to work on #73. I’m going to check the README’s `OPENROUTER_API_KEY` instructions against `.env.example` and the OpenRouter configuration fields in `core/config.py`. I’ll post what those three files actually say and document the mismatch, or report what I find if I can’t reproduce the issue.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5824016366

## Reproduction Report — Issue #73

## Issue

**Repository:** `codepath/pathreview-ai301-fa26-s3`

**Issue:** #73 — README and `.env.example` disagree about which LLM API key to set

## Environment

This is a documentation/configuration consistency issue, so no application runtime environment was required.

**Repository state tested:** `codepath/pathreview-ai301-fa26-s3` main @ `2f4e82f` (2026-09-24)

## Steps to Reproduce

1. Open `README.md`.
2. Search for `OPENROUTER_API_KEY`.
3. Open `.env.example`.
4. Check the documented `LLM_PROVIDER` options and API key variables.
5. Open `core/config.py`.
6. Search for the LLM provider and API key configuration fields.

## Observed Behavior

The files contain inconsistent configuration instructions.

In `README.md`, the environment setup says:

```text
# Configure environment (add your OPENROUTER_API_KEY to .env)
cp .env.example .env
```

However, `.env.example` says:

```text
# LLM provider
# Options: "mock" (default, no API key needed), "openai"
LLM_PROVIDER=mock
OPENAI_API_KEY=sk-your-key-here
```

There is no `OPENROUTER_API_KEY` entry in `.env.example`, and OpenRouter is not listed in its documented `LLM_PROVIDER` options.

At the same time, `core/config.py` contains OpenRouter configuration fields:

```text
# LLM Configuration
llm_provider: str = Field(default="mock")
openai_api_key: str = Field(default="")
openrouter_api_key: str = Field(default="")
openrouter_base_url: str = Field(default="https://openrouter.ai/api/v1")
openrouter_model: str = Field(default="google/gemma-3-27b-it:free")
```

This shows that the configuration code defines OpenRouter-related settings while the example environment file does not document them.

## Expected Behavior

`README.md`, `.env.example`, and `core/config.py` should provide consistent instructions for configuring supported LLM providers.

If OpenRouter is supported, `.env.example` should include the appropriate OpenRouter configuration, including `OPENROUTER_API_KEY` and the corresponding `LLM_PROVIDER` option.

## What I Did Not Test

I did not run the application with `LLM_PROVIDER` set to OpenRouter, so this reproduction does not establish whether OpenRouter works at runtime. It only verifies the inconsistency between `README.md`, `.env.example`, and `core/config.py`.

## Conclusion

The reported documentation/configuration mismatch is reproducible by comparing `README.md`, `.env.example`, and `core/config.py`.

The README directs users toward an OpenRouter API key, while `.env.example` does not provide corresponding OpenRouter configuration guidance, even though `core/config.py` contains OpenRouter configuration fields.

---

## Eval iterations

### Run history

| Run                | Packages                                         | Agreement |
| ------------------ | ------------------------------------------------ | --------: |
| 1                  | `pkg-01`                                         |   **1/1** |
| 2                  | `pkg-02`, `pkg-03`, `pkg-04`, `pkg-05`           |   **4/4** |
| 3                  | `pkg-06`, `pkg-07`, `pkg-08`, `pkg-09`, `pkg-10` |   **3/5** |
| 4                  | `pkg-11`, `pkg-12`, `pkg-13`, `pkg-14`, `pkg-15` |   **5/5** |
| 5                  | `pkg-16`, `pkg-17`, `pkg-18`, `pkg-19`, `pkg-20` |   **4/5** |
| 6                  | `pkg-09`, `pkg-10`, `pkg-16`                     |   **2/3** |
| 7                  | `pkg-17`, `pkg-18`, `pkg-19`, `pkg-20`           |   **4/4** |
| 8                  | `pkg-11`, `pkg-12`, `pkg-13`, `pkg-14`, `pkg-15` |   **5/5** |
| 9                  | `pkg-06`, `pkg-07`, `pkg-08`                     |   **3/3** |
| 10                 | `pkg-02`, `pkg-03`, `pkg-04`, `pkg-05`, `pkg-01` |   **5/5** |
| **Final full run** | **`pkg-01` through `pkg-20`**                    | **20/20** |

### Final Evaluation

The final `eval-run.txt` reports:

> **agreement: 20/20 scored items (bar: 18/20: PASS)**

### Package analysis

**Package:** `pkg-16`

**Rubric result:** `reject`
**Gold label:** `accept`

The rubric decided `reject` because the package reproduced the reported behavior using an older pandas version rather than establishing that the current issue was reproduced under the relevant conditions. The package used pandas 1.5.3, Python 3.10.12, and Ubuntu 22.04, while the issue context concerned pandas 2.3.3/current main, Python 3.14.6, and macOS arm64.

The reproduction showed the reported `ValueError`, but the rubric was designed to distinguish an old-version reproduction from evidence that the current issue was reproduced under the relevant environment.

### Check rationale

**Quoted check from `rubric.md`:**

| `environment`    | The **repro report's Environment record**, read against the **issue context's stated version, OS, installation method, and other relevant target conditions**          | Pass if the environment information is sufficient to determine whether the reported issue was tested under relevant conditions; differences from the issue's environment must be identified when they materially affect reproduction   | required  |

I revised this check so that the environment is treated as required rather than preferred. The revision requires enough environment information to determine whether the issue was tested under relevant conditions and requires material differences to be identified. This prevents a reproduction on a materially different or unsupported version from automatically being treated as evidence that the current issue was reproduced.

### Trade-offs

The revised environment requirement can reject a reproduction that demonstrates the same error if it was performed under materially different conditions. I accepted that trade-off because the evaluation should distinguish reproducing an old-version behavior from establishing that the reported issue is reproduced under the relevant current conditions.

For a documentation mismatch such as issue #73, this also means recording the repository revision is important because the contents of the files can change over time.

---

