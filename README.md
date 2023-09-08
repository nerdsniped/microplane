# Microplane

The control plane for localhost

## Synopsis

Microplane is the control plane for localhost.

What does that mean? I'm glad you asked!
Microplane allows automatically starting and stopping services on your
development machine, allowing you to access them via a single configurable TLD,
and automatically handles routing requests so all of your services can be accessed
on ports 80 and 443.

Additionally, microplane sets up standard observability and process management stacks,
so your local development environment can be as productive as production as possible.

Microplane is brought to you by Nerdsniped LCC, a small software consultancy based in Oakland, CA. [Subscribe to microplane](https://microplane.dev/pricing) to support [@segiddins](https://github.com/segiddins) and unlock access to upcoming pro features in microplane.

## Installation

### Homebrew

```sh
brew install nerdsniped/microplane/microplane
microplane install
```

and profit!

## Example

This is an example microplane file that has several services.
Place it in `~/.microplane.yaml` and run `microplane install` to get started.

```yaml
# yaml-language-server: $schema=https://raw.githubusercontent.com/nerdsniped/microplane/main/dot-microplane.schema.json
# vim: set ts=2 sw=2 tw=0 fo=cnqoj
tlds:
  microplane: {}
  test: {}

applications:
  "junit-io":
    enabled: true
    name: junit.io
    processes:
      web:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - |
            set -eo pipefail
            source .pumaenv
            set -u
            RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET
        working_directory: "~/Development/github.com/segiddins/junit-io"
        network_mode: socket
        tailscale:
          machine_name: junit-io
          allowed_tailnets: [folk-dinosaur.ts.net, shark-crocodile.ts.net]
  "rubygems-org":
    enabled: true
    name: rubygems.org
    processes:
      web:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - |
            set -eo pipefail
            source .pumaenv
            set -u
            RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET
        working_directory: "~/Development/github.com/rubygems/rubygems.org"
        network_mode: socket
    timeouts:
      idle: PT60M
  "microplane-dev":
    enabled: true
    name: microplane.dev
    processes:
      web:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - |
            set -euxo pipefail
            RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET
        working_directory: "~/Development/github.com/segiddins/microplane-dev"
        network_mode: socket
        tailscale:
          enabled: true
          machine_name: microplane-dev
          allowed_tailnets: [folk-dinosaur.ts.net, shark-crocodile.ts.net]
  "testserver":
    name: testserver
    processes:
      server:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - "exec ./testserver --port $PORT"
        working_directory: "~/Development/github.com/segiddins/microplane/testserver"
        network_mode: port
        tailscale:
          machine_name: microplane-test
          allowed_tailnets: mine
  "microplane-frontend":
    name: microplane-frontend
    processes:
      web:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - |
            set -euxo pipefail
            yarn dev --strictPort --port ${PORT} --host $MICROPLANE_APP_NAME.microplane
        working_directory: "~/Development/github.com/segiddins/microplane/frontend"
        network_mode: port
        tailscale:
          machine_name: microplane-frontend
          allowed_tailnets: mine
  "wedding":
    processes:
      next-dev:
        launch_command:
          - env
          - -S
          - ${SHELL}
          - -lic
          - |
            set -euxo pipefail
            yarn dev --port ${PORT}
        working_directory: "~/Development/github.com/segiddins/hannah-and-sam.wedding"
        network_mode: port
        tailscale:
          machine_name: wedding
          allowed_tailnets: all
```
