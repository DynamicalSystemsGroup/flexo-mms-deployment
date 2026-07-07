# Flexo MMS Deployment

This repo contains sample files for Flexo MMS deployment using docker compose or kubernetes.

Please see [here](https://flexo-mms-deployment-guide.readthedocs.io/en/latest/index.html) for Flexo MMS documentation.

## Developer entry roadmap

This repository can become the first stop for new Flexo contributors: a place where they can start the service set, load a small model, exercise the APIs, and understand how model lifecycle workflows fit together before working on the individual services.

The proposed onboarding path is:

1. Start Flexo locally with one command.
2. Verify service health with one command.
3. Load a small, easy-to-understand SysML v2 model.
4. Create branches that represent competing engineering changes.
5. Inspect API responses, graph diffs, and review/merge scenarios.
6. Follow links into the service repositories for implementation work.

### Proposed quickstart experience

The target experience should be simple enough for a first-time contributor:

```sh
git clone https://github.com/Open-MBEE/flexo-mms-deployment.git
cd flexo-mms-deployment
./demo up
./demo seed thermostat
./demo check
```

The expected output should tell contributors what is running and what to try next:

```text
Flexo MMS Layer 1: OK
Flexo MMS SysML v2 API: OK
Thermostat lifecycle model: loaded
Branches: base, comfort-change, energy-saving, safety-change
Try: http://localhost:8083/projects
```

### Proposed first model: smart thermostat

A smart thermostat is intentionally simple. The system itself is easy to understand, so contributors can focus on Flexo's model-management concepts:

- projects, branches, commits, and API calls
- SysML v2 model loading
- model diff and review workflows
- conflict-resolution examples
- policy-based lifecycle management

The example should include:

- `base` model with a thermostat, temperature sensor, controller, heater, cooling unit, room, and display
- `comfort-change` branch that narrows the comfort band
- `energy-saving` branch that widens the comfort band and delays actuation
- `safety-change` branch that strengthens sensor fault behavior
- a walkthrough that explains which changes can auto-merge and which require review

### Proposed repository additions

The following additions would make this repository friendlier for new contributors:

- `examples/thermostat-lifecycle/README.md` for the end-to-end scenario
- SysML v2 seed files for the thermostat model and branch variants
- Bruno or Postman collections for common API calls
- `demo` helper script for `up`, `down`, `seed`, `check`, and `logs`
- health checks for Layer 1, SysML v2, and the backing graph store
- `.devcontainer/` configuration for a one-click GitHub Codespaces setup
- README badges for build status, docs, container images, and community links
- issue labels such as `good first issue`, `developer-experience`, `example`, and `needs-repro`

### One-click options to evaluate

Chatwoot-style deploy buttons are useful because they reduce the time between discovery and a working system. For Flexo MMS, the most appropriate one-click path is likely a developer environment rather than a public production deployment:

- **GitHub Codespaces / Dev Containers:** best first target for contributor onboarding.
- **Gitpod:** useful alternative if maintainers prefer a browser IDE path.
- **DigitalOcean Kubernetes one-click:** possible later for deployment demos, but higher maintenance because Flexo is a multi-service stack.
- **Local Docker Compose:** should remain the canonical path for repeatable development.

### Maintainer questions

Before implementing the full onboarding kit, contributors should confirm:

- Which branch should receive developer-experience work?
- Should the thermostat lifecycle example live in this repository or in a separate examples repository?
- Should the API examples use Bruno, Postman, curl scripts, or more than one?
- Which services must be included in the minimum useful local stack?
- Which ports and service names should be treated as stable for examples?
