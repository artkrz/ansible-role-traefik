# Changelog

All notable changes to this Ansible role will be documented in this file.

## [3.0.0] - 2025-11-03

### Changed
- **BREAKING**: Upgraded Traefik from v2.2 to v3.5.4
- Updated minimum Ansible version from 2.7 to 2.9
- Updated role description to reflect Traefik v3 support
- Fixed README example configuration:
  - Changed `entrypoints` to `entryPoints` (consistent casing)
  - Changed string values to lists for `entryPoints` configuration
  - Changed `certresolver` to `certResolver` (camelCase)
  - Added required `storage` and `httpChallenge` to ACME configuration

### Added
- MIGRATION_V2_TO_V3.md - Comprehensive migration guide
- CHANGELOG.md - This file
- Additional galaxy tags: traefik3, reverse-proxy

### Migration Notes
- This is a major version upgrade. Please review [MIGRATION_V2_TO_V3.md](MIGRATION_V2_TO_V3.md) before upgrading
- The role maintains backward compatibility with v2 syntax for dynamic configuration
- Test in a non-production environment first
- For v2 compatibility, you can set `core.defaultRuleSyntax: v2` in your configuration

### Resources
- [Traefik v3 Documentation](https://doc.traefik.io/traefik/)
- [Official v2 to v3 Migration Guide](https://doc.traefik.io/traefik/migrate/v2-to-v3/)

## [2.0.0] - Previous versions
- Traefik v2.2 support
