# Lattice Development Orchestration
#
# Usage:
#   tilt up    - Start all services
#   tilt down  - Stop all services

load("ext://git_resource", "git_checkout")
load("ext://dotenv", "dotenv")
load("ext://color", "color")

# Load environment
if os.path.exists(".env.local"):
    dotenv(fn=".env.local")

# Read service configuration
config_path = "services.yaml"
if not os.path.exists(config_path):
    fail("Missing services.yaml - copy from services.yaml.template")

services = read_yaml(config_path).get("services", [])

# ------------------------------------
# Bootstrap services
# ------------------------------------
for service in services:
    name = service.keys()[0]
    values = service.values()[0]
    repo = values.get("repo", "")
    path = values.get("path", "services/%s" % name)

    if repo and not os.path.exists(path):
        print(color.yellow("%s does not exist, cloning..." % path))
        git_checkout(repo, path)
    elif os.path.exists(path):
        print(color.green("%s already exists" % path))

# ------------------------------------
# Lattice server
# ------------------------------------
server_path = "services/lattice"

if os.path.exists(server_path):
    local_resource(
        "build-lattice",
        cmd="cargo build --release -p lattice-cli",
        dir=server_path,
        deps=[
            "%s/core" % server_path,
            "%s/cli" % server_path,
            "%s/Cargo.toml" % server_path,
        ],
        labels=["lattice"],
    )

    local_resource(
        "dev-lattice",
        serve_cmd="cargo run -p lattice-cli -- serve --config lattice.yaml",
        serve_dir=server_path,
        resource_deps=["build-lattice"],
        readiness_probe=probe(
            http_get=http_get_action(
                path="/health",
                port=4000,
            ),
            initial_delay_secs=5,
            period_secs=5,
        ),
        labels=["lattice"],
    )
else:
    print(color.yellow("lattice not found - run services bootstrap first"))

# ------------------------------------
# Lattice site
# ------------------------------------
site_path = "services/lattice-site"

if os.path.exists(site_path):
    include("%s/Tiltfile" % site_path)
else:
    print(color.yellow("lattice-site not found - run services bootstrap first"))
