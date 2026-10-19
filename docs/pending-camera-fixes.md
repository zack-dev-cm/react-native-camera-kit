# Pending Camera Kit fixes

The current resolved integration combines these contributions against upstream base `a2a81cee8995c831aca472836e2be446994ba070`:

| PR | Source commit | Change |
| --- | --- | --- |
| [#816](https://github.com/teslamotors/react-native-camera-kit/pull/816) | `173643f47bf6ad8eb872d13fc46ce7c74f186d25` | Linux installation, iOS toolchain, branch triggers and Android regression CI |
| [#812](https://github.com/teslamotors/react-native-camera-kit/pull/812) | `d21863507221f0df9fddad9080cc02e16d09e7d9` | Barcode coordinate mapping |
| [#813](https://github.com/teslamotors/react-native-camera-kit/pull/813) | `81914f544e2005d3b16c9c2e987c8540fc4aa3f3` | Scanner lifecycle and shared-image ownership |
| [#817](https://github.com/teslamotors/react-native-camera-kit/pull/817) | `39eee8e44bb7f326a12328457fa41edb117c6e87` | Executor recreation on reattachment |

The integration is [`90f906b345e46d2bd50839b1f41701cbee065441`](https://github.com/zack-dev-cm/react-native-camera-kit/commit/90f906b345e46d2bd50839b1f41701cbee065441), tree `79b8474874b27383a28cf0fe3c7abcb5984500bf`, on `integration/tesla-fixes-20261019`. All four heads above are ancestors. Its native files are identical to the earlier `7ffe27b` integration; the new diff changes only Build and Linter workflows.

The native fixes remain independently submitted. Geometry and scanner ownership overlap in `CKCamera.kt`, `QRCodeAnalyzer.kt` and Gradle configuration. The resolved files retain geometry snapshots, scanner ownership and shared-image closure, then add the executor guard. Review CI #816 first and use this complete integration as the tested resolution of those overlaps. Inspect its full diff before incorporating it.

```sh
git fetch https://github.com/zack-dev-cm/react-native-camera-kit.git integration/tesla-fixes-20261019
git switch --create review-camera-fixes FETCH_HEAD
git rev-parse HEAD
git rev-parse 'HEAD^{tree}'
git diff --check a2a81cee8995c831aca472836e2be446994ba070 HEAD
git diff a2a81cee8995c831aca472836e2be446994ba070 HEAD -- android/src/main/java android/build.gradle .github/workflows
```

[Run 39201665460](https://github.com/zack-dev-cm/react-native-camera-kit/actions/runs/39201665460) passed on October 19, 2026. Its Android jobs pin this integration, build and install an npm archive, verify installed native files, run TypeScript/ESLint, and pass all 41 Robolectric cases per architecture with zero failures, errors or skips. Both installed Debug AARs and Android example APKs build. The five suites cover coordinate mapping (9), selection (8), scanner lifecycle (14), shared-image ownership (7) and executor reattachment (3).

The separate iOS job pins the isolated #816 source `173643f` and passes the exact Debug `iphoneos` example build with Xcode 26.2. The original Linux bootstrap at `d8b1dd3` reaches the CocoaPods hook and fails with exit 127 (`bundle: not found`). Its control job expects that failure and preserves its actual output; a successful verifier job does not make the original bootstrap pass.

The run publishes JUnit, per-suite summaries, build logs and AARs. This is unit/build validation, with simulated geometry and mocked inference in the unit cases. New physical-camera checks, ML Kit instrumentation, simulator navigation and performance measurements were not run. Earlier independent platform evidence remains in the original PR descriptions. Upstream workflow execution and maintainer acceptance are separate requirements.
