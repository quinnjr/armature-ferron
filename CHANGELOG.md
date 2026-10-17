# Changelog — `armature-ferron`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

### Removed

- **Breaking:** The `native-tls` feature is removed (it pulled in OpenSSL; Armature is rustls-only). Use `rustls`.

### Security

- KDL configuration injection: every value written into the generated Ferron config is now escaped as a KDL string (`"` and `\` escaped, control characters rejected). Backend URLs (default backend, load balancer, location `proxy`, route `backend`, registry instances) must be `http`/`https` with a host and are emitted in normalized `Url` form. Location/route paths and `add_prefix` must start with `/` and contain only printable ASCII without whitespace, quotes, backslashes, or `$`; header names must be RFC 9110 tokens; domains are restricted to hostname characters.

### Added

- `Location::validate` and `ProxyRoute::validate`.

### Changed

- **Breaking:** `FerronConfig::validate`/`to_kdl`, `Backend::validate`, and `ServiceRegistry::register`/`register_instance` now reject configurations they previously accepted (non-http(s) backend schemes, control characters, malformed paths/header names/domains). Migration: use `http://`/`https://` backends and plain absolute paths.

## [0.2.3] - 2026-09-15

### Changed

- Dependencies bumped to their latest releases: `tokio` 1.52 → 1.53, `uuid` 1.23 → 1.26.

## [0.2.2] - 2026-08-04

### Fixed

- Requirements on sibling armature crates name a minor instead of `0`. Under
  Cargo's 0.x rules `version = "0"` matches any release ever made, and edition
  2024 selects the MSRV-aware resolver, so a consumer declaring an older
  `rust-version` was handed the oldest version satisfying it — resolving
  `armature-core = "0"` on Rust 1.89 produced `armature-core 0.2.3` while an
  explicit `armature-core = "0.8"` elsewhere in the same graph pulled 0.8.2.
  Two copies of core, and a build failing on symbols the older one lacks. Each
  0.x minor in this family is a breaking change, so the requirement now names
  one. No API change.
