# Plan

## Diagnosis

Issue #73 reports that `README.md` and `.env.example` disagree about which LLM API key to configure.

The documentation/configuration review showed that:

* `README.md:24` instructs users to add `OPENROUTER_API_KEY` to `.env`.
* `.env.example` lists `"mock"` and `"openai"` as `LLM_PROVIDER` options and provides `OPENAI_API_KEY`.
* `core/config.py:18-22` declares `llm_provider`, `openai_api_key`, `openrouter_api_key`, `openrouter_base_url`, and `openrouter_model`.

I then searched the repository for where these settings are read. `llm_provider` and the three `openrouter_*` settings are declared in `core/config.py:18-22`, but nothing else reads them. `ReviewGenerator` in `rag/generator/review_generator.py:25` is the only OpenRouter-capable code identified, and nothing constructs it. Therefore, `OPENROUTER_API_KEY` is not currently used by the application.

The OpenAI API key should remain in `.env.example` because the OpenAI embedding provider reads it from the environment at `ingestion/embeddings/provider.py:77`.

The underlying problem is therefore that the documentation tells users to configure an OpenRouter key that the current application does not use.

## Scope

### What I will change

* Update `README.md` so the default setup does not instruct users to configure `OPENROUTER_API_KEY`.
* Update `docs/SETUP.md` so it does not state that `OPENROUTER_API_KEY` is required for AI features.
* Make both documentation files point users toward the configuration represented by `.env.example`.

### What I will not change

* I will not add `OPENROUTER_API_KEY` to `.env.example`.
* I will not implement OpenRouter runtime support.
* I will not modify `core/config.py`.
* I will not modify the provider implementation.
* I will not modify `ReviewGenerator`.
* I will not make unrelated documentation or code changes.

## Files to Touch

The only files I will modify are:

* `README.md`
* `docs/SETUP.md`

I will inspect, but not modify:

* `.env.example`
* `core/config.py`
* `rag/generator/review_generator.py`
* `ingestion/embeddings/provider.py`

## Approach

1. Update `README.md:24` by replacing the instruction to add `OPENROUTER_API_KEY` with a note that the default mock setup requires no API key.
2. Update `docs/SETUP.md:47` with the same correction so it no longer says that `OPENROUTER_API_KEY` is required for AI features.
3. Leave `.env.example` unchanged because `OPENAI_API_KEY` is still used by the OpenAI embedding provider at `ingestion/embeddings/provider.py:77`.
4. Do not wire `LLM_PROVIDER` or the OpenRouter settings into runtime code because that would expand the issue from a documentation mismatch into a new provider implementation.
5. Review the final diff to confirm that only `README.md` and `docs/SETUP.md` were changed.

## Test Plan

After making the changes:

1. Check that the unsupported OpenRouter setup instruction is removed from the affected documentation:

```bash
grep -n OPENROUTER README.md docs/SETUP.md .env.example
```

Expected result: no `OPENROUTER` matches in `README.md` or `docs/SETUP.md`.

2. Verify that the existing OpenAI API key remains in `.env.example`:

```bash
grep -n OPENAI_API_KEY .env.example
```

Expected result: the `OPENAI_API_KEY` entry is still present.

3. Review the changed files:

```bash
git diff -- README.md docs/SETUP.md
```

Expected result: the documentation changes remove the unsupported OpenRouter setup instruction and describe the current configuration accurately.

4. Confirm that no unrelated files were changed:

```bash
git diff --stat
```

Expected result: only `README.md` and `docs/SETUP.md` are listed.

5. Re-check the relevant setup instructions in README.md and docs/SETUP.md to confirm  that they no longer tell users to configure an unsupported OpenRouter key.

## Risks

* Removing the OpenRouter instruction could be incorrect if there is undocumented runtime support elsewhere in the repository. The repository search will be reviewed before finalizing the change.
* Changing `.env.example` unnecessarily could remove the `OPENAI_API_KEY` needed by the OpenAI embedding provider. Therefore, `.env.example` will remain unchanged.
* The `ReviewGenerator` code mentions OpenRouter, so changing documentation without understanding its current usage could create another inconsistency. The repository search confirms that nothing currently constructs `ReviewGenerator`.

## Unknowns

* It is unclear why `openrouter_api_key`, `openrouter_base_url`, and `openrouter_model` exist in `core/config.py` when they are not currently read.
* `ReviewGenerator` contains OpenRouter-capable code, but its intended future use is not established by the current repository evidence.
* It is unclear whether OpenRouter support was planned for a future implementation or whether these settings are leftover configuration.
* These questions are outside the scope of this documentation fix.

## Deviations

No changes from the plan. The implementation followed the planned scope and only updated `README.md` and `docs/SETUP.md`.

