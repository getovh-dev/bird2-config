# BIRD Ansible Deployment

Automated deployment and management of BIRD Internet Routing Daemon (BIRD) using Ansible, with support for BGP session management, IRR-based filtering, and IPv6 GRE tunnel generation.

## Features

- **Automated BIRD Configuration** — Deploy BIRD configuration across multiple routers with Jinja2 templating
- **BGP Session Management** — Support for transit, peer, IXP, downstream, and BYOIP sessions
- **IRR Filtering** — Automatic generation of prefix filters from Internet Routing Registry using `bgpq4`
- **RPKI Validation** — Built-in RPKI checks for route validation
- **GRE Tunnels** — Generate IPv6 GRE tunnel meshes for iBGP connectivity
- **CI/CD Integration** — Automated IRR updates and configuration validation via scheduled pipelines

## Quick Start

### Prerequisites

- Ansible >= 2.9
- Python 3.8+
- SSH access to target routers
- `bgpq4` (for IRR filtering)

### Installation

```bash
# Clone the repository
git clone <repo-url> bird-deployment
cd bird-deployment

# Create Python virtual environment (optional)
python -m venv .venv
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# or simply: pip install ansible
```

### Basic Usage

1. **Edit inventory** — Update `routers/hosts.yml` with your routers
2. **Configure routers** — Edit router-specific vars (e.g., `routers/amsterdam.yml`)
3. **Run playbook**:
   ```bash
   ansible-playbook -i routers/hosts.yml play.yml --tags bird
   ```

For detailed examples, see [Usage Guide](docs/usage.md).

## Documentation

- [Getting Started](docs/getting-started.md) — Initial setup and requirements
- [Usage Guide](docs/usage.md) — Common workflows and patterns
- [Architecture](docs/architecture.md) — Repository layout and component overview
- [Templates](docs/templates.md) — Template variables, macros, and customization
- [Examples](docs/examples.md) — Practical configuration examples
- [iBGP Tunnels](docs/generate_ibgp_tunnels.md) — IPv6 GRE tunnel generation
- [CI/CD](docs/ci.md) — Scheduled IRR updates and pipeline setup
- [Tasks](docs/tasks.md) — Ansible task reference
- [FAQ](docs/faq.md) — Common questions and answers
- [Contributing](docs/contributing.md) — Contribution guidelines

## Key Components

### Roles

- **`roles/bird/`** — Main BIRD installation and configuration role
  - `tasks/` — Installation and config deployment steps
  - `templates/` — `bird.conf.j2` and helper scripts
  - `files/` — Filter definitions and static configs

- **`roles/generate_ibgp_tunnels/`** — iBGP tunnel mesh generation

### Inventory

- **`routers/hosts.yml`** — Host group definitions and SSH settings
- **`routers/*.yml`** — Per-router variables (ASN, BGP peers, addressing)

### Automation

- **`play.yml`** — Main playbook applying roles to routers
- **`generate_ibgp_tunnels.yml`** — Helper playbook for tunnel generation
- `.github/workflows/` — CI/CD workflows for automated updates

## Common Tasks

### Deploy BIRD to a specific router

```bash
ansible-playbook -i routers/hosts.yml play.yml --limit amsterdam
```

### Test configuration without applying changes

```bash
ansible-playbook -i routers/hosts.yml play.yml --check
```

### Generate iBGP tunnel configuration

```bash
ansible-playbook -i inventory/hosts generate_ibgp_tunnels.yml
```

### Update IRR filters locally

```bash
./scripts/ci_update_irr.sh
```

### Validate repository

```bash
./scripts/check_repo.sh
```

## Session Types

The templates support multiple BGP session types:

- **Transit** — Transit provider sessions
- **Peer** — Peer exchange or bilateral peering
- **IXP** — Internet exchange point participants
- **Downstream** — Customer/downstream sessions with prefix filtering
- **BYOIP** — Bring-Your-Own-IP sessions with custom prefix validation
- **iBGP** — Internal BGP for multi-router setups

## IRR and RPKI

The system uses:
- **IRR filters** — Generated per-downstream AS-SET or ASN for prefix validation
- **RPKI** — BGP origin validation to detect hijacked prefixes
- **Bogon checks** — Filtering of reserved and private IP ranges

See [Templates](docs/templates.md) for detailed IRR and RPKI configuration.

## Support

For questions:
1. Check [FAQ](docs/faq.md) for common issues
2. Review [Examples](docs/examples.md) for configuration patterns
3. See [Architecture](docs/architecture.md) for component overview
4. Open an issue or consult [Contributing](docs/contributing.md) guidelines

## License

[Add your license information here]

## Project Status

This project is actively maintained. Contributions are welcome — see [Contributing](docs/contributing.md).
