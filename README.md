# Ansible WordPress + Reverse Proxy Deployment

This repository contains an Ansible-based deployment for a WordPress web application, a reverse proxy layer, and supporting services.

## Overview

The project is organized as a multi-role Ansible repository. The main playbook is `site.yml`, which orchestrates two distinct deployments:

- `Configure WordPress Server` — sets up the WordPress instance and its dependencies.
- `Configure Reverse Proxy` — sets up a reverse proxy host and SSL support.

## Repository Layout

- `ansible.cfg` — base Ansible configuration.
- `inventory/hosts` — inventory file defining host groups.
- `group_vars/all.yml` — shared variables used across all hosts.
- `site.yml` — top-level playbook.
- `roles/` — reusable role definitions for each component.

### Role directories

- `roles/nginx/` — configures Nginx on the WordPress host.
- `roles/php/` — installs and configures PHP for WordPress.
- `roles/mariadb/` — installs and configures MariaDB/MySQL.
- `roles/wordpress/` — deploys WordPress and sets up application configuration.
- `roles/phpmyadmin/` — installs and configures phpMyAdmin for database management.
- `roles/sftp/` — configures SFTP access and the web root user.
- `roles/reverse_proxy/` — configures the proxy host.
- `roles/ssl/` — manages SSL configuration and certificates for the proxy.

Each role also contains a `tests/` subdirectory with test playbooks and inventory samples.

## Key Files

### `site.yml`

This playbook defines the two main deployment flows:

- WordPress stack: `nginx`, `php`, `mariadb`, `wordpress`, `phpmyadmin`, `sftp`.
- Reverse proxy stack: `reverse_proxy`, `ssl`.

### `ansible.cfg`

Current defaults:

- inventory is set to `inventory/hosts`
- `host_key_checking` is disabled for convenience
- default remote user is `ubuntu`

### `inventory/hosts`

Defines host groups used by the playbook.

- `wordpress` — target host(s) for the WordPress application.
- `reverse_proxy` — target host(s) for the proxy and SSL termination.

### `group_vars/all.yml`

Contains global configuration values used by multiple roles. Common variables include:

- `site_domain`
- `wordpress_url`
- `wordpress_behind_proxy`
- `wp_user`, `wp_password`, `wp_database`
- `site_user`, `site_password`
- `site_name`
- `wordpress_private_ip`
- `web_root`

> Important: Do not store real secret values in a public repository. Use Ansible Vault or environment-specific variable management for passwords and sensitive settings.

## Getting Started

1. Install Ansible on the control node.
2. Update `inventory/hosts` with your real host names or IP addresses.
3. Adjust `group_vars/all.yml` to match your environment.
4. Run the playbook:

```bash
ansible-playbook -i inventory/hosts site.yml
```

For a dry run, use:

```bash
ansible-playbook -i inventory/hosts site.yml --check
```

## Customization

Modify the following files to adapt the deployment:

- `inventory/hosts` — host assignments and group membership.
- `group_vars/all.yml` — global variables, site names, database credentials, paths, and proxy configuration.
- Role defaults and task files inside `roles/*/defaults/`, `roles/*/tasks/`, and `roles/*/templates/`.

## Role Behavior

The repository is built around roles, which encapsulate each component's configuration:

- `nginx` installs/configures the web server.
- `php` prepares the PHP runtime for WordPress.
- `mariadb` installs the database server and creates the WordPress database.
- `wordpress` deploys WordPress files and configures `wp-config.php`.
- `phpmyadmin` installs a web-based database management tool.
- `sftp` configures a site user and secure file transfer access.
- `reverse_proxy` sets up the proxy host to forward requests to the WordPress server.
- `ssl` manages TLS/SSL certificate configuration on the reverse proxy.

## Testing and Validation

Each role includes a minimal `tests/` directory. These can be used as starting points for role validation.

- `roles/<role>/tests/inventory` — example inventory for a role.
- `roles/<role>/tests/test.yml` — role-specific test playbook.

## Notes

- This repository is intended for infrastructure automation and assumes the control node can SSH to target hosts.
- Use a secure secrets management strategy for passwords, keys, and other sensitive configuration.
- Review each role before running it in production to ensure the behavior matches your requirements.

## Recommended Next Steps

- Add Ansible Vault support for secret variables.
- Review and tailor role templates and handlers.
- Validate host connectivity with `ansible-inventory --list -i inventory/hosts`.
- Use `ansible-lint` to check playbooks and roles for best practices.
