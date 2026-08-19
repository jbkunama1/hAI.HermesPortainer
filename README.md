# hAI.HermesPortainer

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Docker Build](https://github.com/jbkunama1/hAI.HermesPortainer/actions/workflows/docker-build.yml/badge.svg)](https://github.com/jbkunama1/hAI.HermesPortainer/actions/workflows/docker-build.yml)
[![Secret Scan](https://github.com/jbkunama1/hAI.HermesPortainer/actions/workflows/trufflehog.yml/badge.svg)](https://github.com/jbkunama1/hAI.HermesPortainer/actions/workflows/trufflehog.yml)

This repository contains a Docker-optimized setup for Hermes WebUI and Hermes Agent on a DietPi/Debian server.

## Features

- **Hermes Agent & WebUI**: Stable two-container Docker stack.
- **Shared Storage**: Unified `HERMES_HOME` via shared volumes for configuration, provider keys, and sessions.
- **Gateway Authentication**: Secure internal communication between WebUI and Agent.
- **Flexible LLM Routing**: Compatible with OpenAI-compatible endpoints (configured for 9router).
- **External Network**: Integrated with `highfishNetwork` for seamless container communication and Cloudflare tunnel integration.

## Architecture

```text
Browser
  │
  ▼
https://hermes.arbeitermili.eu
  │
  ▼
Cloudflare Tunnel
  │
  ▼
hermes-webui:8787
  │
  ├── hermes-agent:8642
  ├── gemeinsames hermes-home-Volume
  ├── gemeinsames hermes-agent-src-Volume
  ├── externer OpenAI-kompatibler LLM-Router
  └── externe MCP-Server
```

## Quick Start

1. Ensure the `highfishNetwork` exists: `docker network create highfishNetwork`.
2. Create your `.env` from the template and fill in secrets:
   - `OPENAI_API_KEY`: your LLM provider key (9router). The stack also exports it as `CUSTOM_9ROUTER_ARBEITERMILI_EU_API_KEY` for Hermes `custom_providers` config.
   - `API_SERVER_KEY`: a secure random hex string for the internal gateway auth (shared by agent and webui). Generate one with `openssl rand -hex 32`.
   - `HERMES_WEBUI_PASSWORD`: Your desired login password.
3. Deploy the stack via Portainer or `docker-compose up -d`.

## LLM Provider: 9router

Both containers share the `hermes-home` volume, so provider config lives in
`<hermes-home>/.hermes/<profile>/config.yaml` (agent and webui read the same file).
`OPENAI_API_KEY` + `OPENAI_BASE_URL` register 9router automatically under the
`openai-api` provider; the model dropdown shows what 9router's `/v1/models`
actually returns for your key (often just `all_coding`).

To pin or extend the model list, declare 9router as a custom provider with a
`models:` allowlist:

```yaml
custom_providers:
  - name: 9router
    base_url: https://9router.arbeitermili.eu/v1
    api_key: "${OPENAI_API_KEY}"
    models:
      - all_coding
      - <weitere model ids von 9router>
```

After editing, run `docker exec -it hermes-agent hermes doctor --fix` and check
what 9router exposes: `curl https://9router.arbeitermili.eu/v1/models -H "Authorization: Bearer $OPENAI_API_KEY"`.

## License

MIT