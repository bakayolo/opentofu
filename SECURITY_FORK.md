# OpenTofu 1.12 security maintenance package

This personal fork preserves upstream v1.12.11 at commit
`7f94b5f33baeb0e255fd22f0785f905fac9644b8`. It changes the committed Go
dependency graph and build/test workflow, plus the telemetry schema import that
must match the upgraded SDK. Planning and apply implementation are unchanged.

The official ARM64 package has eleven fixable HIGH findings under Trivy 0.70.0:
go-jose v4.1.4, klauspost/compress v1.18.5, OpenTelemetry SDK v1.42.0,
x/mod v0.38.0, x/net v0.57.0, gRPC v1.79.3, and oras-go/v2 v2.6.1.
The maintained Alpine package is older, and official v1.13.2 also retains
vulnerable dependencies. The image's vulnerability gate must not be bypassed.

The package is `1.12.11-shuffle.2`; the CLI reports upstream `1.12.11` to
preserve native plan/version compatibility. Its fork source commit and archive
checksum are pinned in the Atlantis Dockerfile. Go 1.27.3, `-mod=readonly`,
module verification, native ARM64 tests for provider installation/cache,
plugin RPC, encryption and tracing, and executable-aware vulnerability scanning are
required before publishing the package.

Build from the exact `v1.12.11-shuffle.2` source tag with Git history available,
and assert the Go build-info module version as well as the CLI version. A shallow
checkout gives Go a `v0.0.0` pseudo-version, which incorrectly matches advisories
already fixed in upstream v1.12.11. Keep accurate version metadata; do not add a
scanner exception.

OpenTelemetry SDK v1.44.0 uses schema v1.41.0. The upstream traceattrs wrapper
explicitly requires its semconv import to match that SDK schema. Updating this
single import preserves telemetry startup; its existing `TestNewResource` guards
the compatibility requirement. Package shuffle.1 omitted that import update and
must not be used with tracing enabled.

Build artifacts include the binary, upstream MPL-2.0 license, archive checksum,
and scanner report. Publish the tested artifact as release
`v1.12.11-shuffle.2`, targeting the exact successful workflow commit. Do not
rebuild or change dependencies during image assembly.

Return to an official compatible OpenTofu release when its actual executable
passes the same vulnerability scan and the Atlantis concurrent-provider and
saved-plan tests. Never switch the IaC engine as part of an image hardening update.
