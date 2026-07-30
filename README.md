# setup-soft-roce-action

[![Test](https://github.com/SF-Zhou/setup-soft-roce-action/actions/workflows/main.yml/badge.svg)](https://github.com/SF-Zhou/setup-soft-roce-action/actions/workflows/main.yml)

Set up [Soft-RoCE](https://github.com/SoftRoCE/rxe-dev/wiki/rxe-dev:-Home) on Ubuntu GitHub Actions runners.

## Usage

Add the action to your workflow:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: SF-Zhou/setup-soft-roce-action@v1
      - run: rdma link
```

The action automatically selects the interface used by the default route. You can override the interface and device name:

```yaml
- id: soft-roce
  uses: SF-Zhou/setup-soft-roce-action@v1
  with:
    interface: eth0
    device-name: rxe_0
- run: echo "${{ steps.soft-roce.outputs.interface }}"
```

## Inputs

| Name          | Required | Default | Description                           |
| ------------- | -------- | ------- | ------------------------------------- |
| `interface`   | No       | Auto    | Ethernet interface used by Soft-RoCE  |
| `device-name` | No       | `rxe_0` | Name assigned to the Soft-RoCE device |

## Outputs

| Name          | Description                          |
| ------------- | ------------------------------------ |
| `interface`   | Ethernet interface used by Soft-RoCE |
| `device-name` | Name of the Soft-RoCE device         |

## Compatibility

The action supports GitHub-hosted Ubuntu x64 and ARM64 runners. It first loads the runner's `rdma_rxe` module and falls back to building the module from matching Linux kernel sources.
