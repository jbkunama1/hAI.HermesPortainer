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
2. Replace placeholders in `docker-compose.yml`:
   - `OPENAI_API_KEY`: Your LLM provider key.
   - `HERMES_WEBUI_PASSWORD`: Your desired login password.
   - `API_SERVER_KEY`: A secure random hex string for internal gateway auth.
3. Deploy the stack via Portainer or `docker-compose up -d`.

## License

MIT