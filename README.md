# 🔲 Lattice Metarepo

This is the metarepo for the Lattice service stack.

- Lattice (gateway): https://github.com/omnidotdev/lattice
- Lattice website: https://github.com/omnidotdev/lattice-site

## Prerequisites

- Install [Tilt](https://tilt.dev)

## Getting Started

`cp services.yaml.template services.yaml`, then configure the services as you see fit. To disable a service, simply comment it out. Any included services will be locally cloned. A local path override for each service can be specified using the `path` key. If no path is explicitly specified, the service will be cloned to `services/service-name` by default.

> 💡 _Note that if nested repos are cloned within this metarepo (such as with the default path of `services/service-name`) for a service and you open this metarepo in your IDE, the IDE may mark the directories as ignored due to the `.gitignore` patterns. To combat this, open the services you want to work on in their own directory (e.g. a separate unit in a VS Code workspace) rather than working with them from within this metarepo._

To get started after setting up the service configuration, run `tilt up`. The `Tiltfile` will automatically pull in resources from any nested `Tiltfile`s it discovers.

> [!WARNING]
> Services might have their own setup requirements, such as environment variable configuration. Consult the service README to make sure you have satisfied all of the initial requirements.

## Running the gateway

The published image is pulled from the Omni registry by default:

```bash
cp .env.local.template .env.local   # optional overrides
docker compose up
```

To build the gateway from source instead of pulling the image:

```bash
docker compose -f compose.yaml -f compose.dev.yaml up
```

The gateway reads a `lattice.yaml` config and a composed `supergraph.graphql` mounted into the container. See the [documentation](https://docs.omni.dev/products/lattice) for configuration, authentication, and plugin details.

## License

The code in this repository is licensed under Apache 2.0, &copy; [Omni LLC](https://omni.dev). See [LICENSE.md](LICENSE.md) for more information.
