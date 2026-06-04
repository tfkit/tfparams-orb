<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://tfkit.github.io/tfparams/logo-horizontal-dark.svg">
    <img src="https://tfkit.github.io/tfparams/logo-horizontal.svg" alt="tfparams" width="320">
  </picture>
</div>

# tfparams-orb

A [CircleCI Orb](https://circleci.com/developer/orbs) for [**tfparams**](https://github.com/tfkit/tfparams) —
generate a Terraform parameter sheet from a plan JSON and terraform-docs metadata.

The orb downloads the published `tfparams` release binary (Linux/macOS executors)
and runs it. The CLI version is configurable via the `version` parameter.

## Usage

```yaml
version: 2.1

orbs:
  tfparams: tfkit/tfparams@1.0.0

jobs:
  parameter-sheet:
    docker:
      - image: cimg/base:current
    steps:
      - checkout
      - run:
          name: Produce plan JSON and terraform-docs output
          command: |
            terraform plan -out=tfplan
            terraform show -json tfplan > plan.json
            terraform-docs json . > docs.json
      - tfparams/generate:
          plan-json: plan.json
          docs-json: docs.json
          out: PARAMETERS.md
          env: production
          store-artifact: true

workflows:
  main:
    jobs:
      - parameter-sheet
```

## `generate` command parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `plan-json` | — (required) | Path to the plan JSON (`terraform show -json <planfile>`) |
| `docs-json` | — (required) | Path to the terraform-docs JSON output |
| `version` | `latest` | tfparams release to use (e.g. `v0.1.1`) or `latest` |
| `out` | `PARAMETERS.md` | Output file path. Use `-` for stdout |
| `format` | `markdown` | `markdown` / `csv` / `json` |
| `scope` | `root` | `root` (root variables) / `module` (module-passed values) |
| `module` | — | Module call name when `scope=module` |
| `env` | — | Environment name shown in the header |
| `extra-args` | — | Additional raw tfparams arguments (appended verbatim) |
| `working-directory` | `.` | Directory to run tfparams in |
| `store-artifact` | `false` | Upload the generated sheet as a CircleCI build artifact |

The orb also ships a `generate` **job** (checkout + generate + store-artifact) and a
default executor (`cimg/base:current`).

> **Executors:** Linux and macOS are supported (the orb downloads a `tar.gz`
> release archive).

## Development & publishing

The orb source lives in `src/`; `.circleci/config.yml` packs and validates it on
every push and publishes on `vX.Y.Z` tags. See the comments in that file for the
one-time CircleCI setup (namespace, orb registration, and the `orb-publishing`
context holding `CIRCLE_TOKEN`).

```bash
# Validate locally
circleci orb pack src > orb.yml
circleci orb validate orb.yml
```

## License

MIT — see [LICENSE](./LICENSE).
