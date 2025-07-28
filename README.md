# helios

[Balena's](https://www.balena.io/) on-device agent. The service is responsible for performing app updates, configuring the host system and generally monitoring and keeping the host and apps healthy.

Helios is designed to run in a diversity of environments and continue operating autonomously with little to no human input.

This project is an experimental replacement for the current [balenaSupervisor](https://github.com/balena-os/balenas-supervisor).

## Features (WIP)

- Self-configuring. When started, the service determines the capabilities of the host enviroment and available interfaces (e.g. container engine, D-Bus, OTA, etc.) and adapts its behavior to apply the remote target state within the given capabilities.
- Fault tolerant. The service can recover from failures and resume operations without human intervention.
- Self-healing. The service continuously monitors the system and perform corrective measures to ensure system health and keep the system on-target.
- Observable. The service state and its decisions can be determined from the system logs.

## Quickstart

### Assuming the identity of an existing device

If you already have a valid UUID and device API key for balenaCloud (eg. from an existing device) you can start helios as follows and it will assume the given device identity:

```
cargo run -- \
    --remote-api-endpoint https://api.balena-cloud.com \
    --remote-api-key uwFQF1eSmfPuyzUpqTVQIJdGxyeOw0Ae \
    --uuid d24c5d17f8a14cda8382c333a96018cf
```

### Registering as a new device

Alternatively, you can have helios register a new unique identity with balenaCloud. To do so, follow these steps:

- Visit balenaCloud Dashboard at https://dashboard.balena-cloud.com/ and login.

- Create a new fleet choosing "Generic AARCH64" for device type (slug: "generic-aarch64"). You may choose any device type you want, but make sure you know its slug as you'll have a hard time finding it on the Dashboard.

- Get the fleet ID from the URL in the browser's address bar.

- Go to Provisioning Keys from the left sidebar and create a new one. Make sure to copy its value when shown. If you miss it, just make a new provisioning key.

- We now have the values we need:
    - The ID of the fleet to provision the device into
    - The device type of this fleet
    - A provisioning key for this fleet

- With those, you can start Helios with:

```
cargo run -- \
    --remote-api-endpoint https://api.balena-cloud.com \
    --provisioning-fleet 2279545 \
    --provisioning-device-type generic-aarch64 \
    --provisioning-key aqP2b0SYkSqAN5rUmEHgU5aX75Cg3n0D
```

For the full list of command line arguments use `cargo run -- --help`

Helios will register with the remote and the device will appear on the Dashboard. The next time you start helios, you do not need to provide these arguments again (though it is not an error if you do; they are just ignored) and helios will still assume the correct identity and connect with remote. This is because helios stores all necessary information into its config file after successful registration. If you want Helios to forget this identity, just delete its config file whose path helios prints when it starts.


## Running on a Balena device

The service can be set-up to take over the existing balena Supervisor on a running device. The service does this by becoming a proxy betweeen the existing supervisor and the Balena API and local applications. For more information see [balena-os/balena-supervisor#2422](https://github.com/balena-os/balena-supervisor/pull/2422).
