# Microplane

Microplane is a control plane for localhost.

What does that mean? I'm glad you asked!
Microplane allows automatically starting and stopping services on your
development machine, allowing you to access them via a single configurable TLD,
and automatically handles routing requests so all of your services can be accessed
on ports 80 and 443.

Additionally, microplane sets up standard observability and process management stacks,
so your local development environment can be as productive as production as possible.

## Installation

Download microplane, run `microplane install`, and profit!

## Example

This is an example microplane file that has several services.
Place it in `~/.microplane.yaml` and run `microplane install` to get started.

```yaml
# ~/.microplane.yaml

# yaml-language-server: $schema=./.microplane.schema.json
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
          - bash
          - -eu
          - -c
          - "RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET"
        working_directory: "~/Development/github.com/segiddins/junit-io"
        network_mode: socket
  "rubygems-org":
    enabled: true
    name: rubygems.org
    processes:
      web:
        launch_command:
          - bash
          - -eu
          - -c
          - "RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET"
        working_directory: "~/Development/github.com/rubygems/rubygems.org"
        network_mode: socket
  "microplane-dev":
    enabled: true
    name: microplane.dev
    processes:
      web:
        launch_command:
          - bash
          - -eu
          - -c
          - "RAILS_LOG_TO_STDOUT=true exec bundle exec puma -C $CONFIG --tag microplane:$MICROPLANE_APP_NAME -w $WORKERS -t 0:$THREADS -b unix:$MICROPLANE_SOCKET"
        working_directory: "~/Development/github.com/segiddins/microplane-dev"
        network_mode: socket
        tailscale:
          machine_name: microplane-dev
          allowed_tailnets: mine
```
