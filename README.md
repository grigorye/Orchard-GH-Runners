# Orchard GH runners

An attempt to employ [Orchard](https://github.com/cirruslabs/orchard)-controlled macOS VMs instead of default GH runners.

## Usage

Assuming  two repos, `gh-user-1/repo-1` and `gh-user-2/repo-2`, and a personal access token `pat`:

### Launching

Create and launch VM with the runners (should become available ~5 min later on each GH repo):

```shell
$ env RUNNER_CFG_PAT=pat spin-up-orchard-runner-vm gh-user-1/repo-1 gh-user-2/repo-2
```

## Deregistering

Remove runners for the above VM (e.g. before deleting the VM):

```shell
$ env RUNNER_CFG_PAT=pat spin-down-orchard-runners-in-vm gh-runner-ventura-xcode-14.3-20230603-223532
```

## Why

-   Save (personal) money by running self-hosted runners (on one or multiple machines), while still benefiting from isolation and reproducibility of runners.
-   Keep the original workflows backward compatible with GH/require zero changes to employ the runners.

## How

Run single orchard-controlled VM per host (ok, 2 VM max, see below), setting up user per runner, therefore share single VM for multiple runners.

Sharing VM instance is not ideal in terms of reproducibility/clean builds/mimicking GH runner:

-   It's not "clean run" as (global) side effects persist/VM is not started clean
-   Runner home may differ
-   Side effects from things like Homebrew are visible/require locking
-   Overall, caution is necessary to avoid effect on other runners, running in parallel (hardcoded absolute paths and etc.)

Unfortunately, while theoretically I could run single VM per runner, it's not practical, as number of running VMs is limited (by Apple) to 2. Spawning VMs on demand (ephemeral runners) could be an option, that would solve most of the above problems, but it would require more complex setup, that I did not try to accomplish yet. I intend to employ this very setup for multiple jobs running in parallel on a single Mac (ok, two Macs maximum).

### Customizable options

All options should be set through environment variables below.

| Variable              | Meaning                                                      | Default            |
| --------------------- | ------------------------------------------------------------ | ------------------ |
| RUNNERS_PER_REPO      | Number of runners spawn per repo                             | 4                  |
| ORCHARD_MACOS_VARIANT | The variant of VM image (xxx in macos-xxx image name). See the list of the images [here](https://github.com/cirruslabs/macos-image-templates). | ventura-xcode:14.3 |
| RUNNER_CFG_PAT        | Personal access token (required)                             |                    |
| RUNNER_LABELS         | Labels associated with the runner (should match the ones used in GH workflows) |                    |
| RUNNER_HOSTNAME       | Hostname visible reflected in the runner settings on GitHub (for the host identification purposes). | $(hostname)        |
|                       |                                                              |                    |

### Known to work

-   [Orchard](https://github.com/cirruslabs/orchard): version 0.7.0-60e564d
-   https://tart.run: 1.5.0
-   (Host) macOS: 13.4
-   Xcode: 14.3

### Thanks

-   Cirrus CI folks for releasing [Orchard](https://github.com/cirruslabs/orchard) and [Tart](https://tart.run) for free/open-sourcing both products.

