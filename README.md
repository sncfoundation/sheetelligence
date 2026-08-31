<p align="center">
  <img src="https://sncfoundation.github.io/logos/sheetelligence.svg" width="96" alt="Sheetelligence logo">
</p>

<h1 align="center">Sheetelligence</h1>

<p align="center"><b>Prompt-native, LLM-driven orchestration</b><br>
A <a href="https://sncfoundation.github.io">Sheet-Native Computing Foundation</a> project &#183; analog of <b>an AI copilot</b></p>

---

**Status:** 📝 Unsaved Draft &#183; this project is a proposal. Design notes and contributions are welcome.

## About

Prompt-native, LLM-driven orchestration Part of the spreadsheet-native stack — the Sheet stays the source of truth, and it reconciles.

## Usage

`sheetelligence` turns a plain-English intent into a [Sheeternetes](https://github.com/tym83/sheeternetes)
deployment manifest (JSON) and prints it. Pipe it into `skctl apply`, or pass `--apply` to POST it
straight to the Apps Script apiserver.

```bash
# Print a manifest (rule-based engine — no API key needed)
./sheetelligence "run 3 replicas of nginx called web"
```

```json
{
  "deployments": [
    {
      "name": "web",
      "image": "nginx",
      "replicas": 3,
      "cpu": 100,
      "memory": 64,
      "cpu_req": 100,
      "mem_req": 64,
      "command": ""
    }
  ]
}
```

The manifest carries both the documented resource keys (`cpu`, `memory`) and the apiserver's own
field names (`cpu_req`, `mem_req`), so it reads per the Sheeternetes shape **and** applies cleanly.

### Two engines

- **LLM** — if `ANTHROPIC_API_KEY` is set, it asks a small Claude model
  (`claude-haiku-4-5` by default) for the manifest. Override with `ANTHROPIC_MODEL`.
- **Rule-based fallback** — always available, no network. Parses an image
  (`nginx`, `redis`, `postgres`, `traefik/whoami`, `myreg.io/team/api:v2`, …), a replica
  count (`3 replicas`, `scale to 5`, `x4`), and a name (`called web`, `named backend`)
  straight out of the intent. Always emits valid JSON. If the LLM call fails, it falls back here.

### Applying

```bash
# POST it to the cluster
./sheetelligence --apply "scale redis cache to 5"

# ...or pipe into skctl
./sheetelligence "run 2 replicas of traefik/whoami named hello" | skctl apply /dev/stdin
```

### Configuration

Reads a `.skctl.env` next to the script (or in the current directory), or plain environment variables:

```bash
WEBAPP_URL=https://script.google.com/macros/s/XXXX/exec
TOKEN=CHANGE_ME_super_secret
ANTHROPIC_API_KEY=sk-ant-...      # optional, enables the LLM engine
ANTHROPIC_MODEL=claude-haiku-4-5  # optional, override the model
```

Requires `bash` and `jq`; `curl` is needed only for `--apply` and the LLM engine.

## Get involved

- 📋 Tracking issue &amp; design: [sncfoundation/sheeternetes#22](https://github.com/sncfoundation/sheeternetes/issues/22)
- 🗺️ [SNCF Landscape](https://sncfoundation.github.io/landscape.html)
- 🧩 [All projects](https://sncfoundation.github.io/projects.html)
- ⚖️ [Governance &amp; how to contribute](https://github.com/sncfoundation/governance)
- 🎓 [Get certified (CSFE)](https://sncfoundation.github.io/certification.html)

## Status legend

Everything starts as an **Unsaved Draft**. It reconciles up the tiers from there — see the
[maturity model](https://sncfoundation.github.io/foundation.html#maturity).

---

<sub>Licensed under Apache-2.0. The SNCF does not recommend running production on a spreadsheet. If you do, please film it.</sub>
