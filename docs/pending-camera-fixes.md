# Pending Camera Kit fixes

The resolved integration combines the following independently submitted fixes against upstream base `a2a81cee8995c831aca472836e2be446994ba070`:

| PR | Source commit | Change |
| --- | --- | --- |
| [#816](https://github.com/teslamotors/react-native-camera-kit/pull/816) | `d8b1dd3307c5e4d89bff7b3647e68887fdf58683` | iOS CI toolchain and default-branch push filters |
| [#812](https://github.com/teslamotors/react-native-camera-kit/pull/812) | `d21863507221f0df9fddad9080cc02e16d09e7d9` | Android barcode coordinate mapping |
| [#813](https://github.com/teslamotors/react-native-camera-kit/pull/813) | `81914f544e2005d3b16c9c2e987c8540fc4aa3f3` | Scanner lifecycle and shared-image ownership |
| [#817](https://github.com/teslamotors/react-native-camera-kit/pull/817) | `39eee8e44bb7f326a12328457fa41edb117c6e87` | Executor recreation when the same view reattaches |

The source commit is [`7ffe27b14691536e6b96f7037033c9787cf942be`](https://github.com/zack-dev-cm/react-native-camera-kit/commit/7ffe27b14691536e6b96f7037033c9787cf942be), with tree `197520ff11fde87171639bb17c99cac06e4c9cce`. All four submitted heads are ancestors of this commit. The production fixes remain on their independent PR branches.

The geometry and scanner fixes overlap in `CKCamera.kt`, `QRCodeAnalyzer.kt`, and `android/build.gradle`. Their resolution retains the geometry snapshots and coordinate mapper while giving each analyzer its scanner lifecycle and routing image closure through the shared-consumer helper. The executor follow-up adds its three-line attachment guard and three regression tests. Gradle test dependencies occur once. The CI changes merge independently.

Review CI #816 first, then the geometry, scanner and executor changes. When incorporating the fixes separately, use the integrated versions of the overlapping files as the tested resolution. The integration contains all four fixes, so its complete diff should be reviewed before incorporating the whole branch.

In a clean checkout, inspect the exact revision without changing an existing branch:

```sh
git fetch https://github.com/zack-dev-cm/react-native-camera-kit.git integration/pending-camera-fixes
git switch --create review-pending-camera-fixes FETCH_HEAD
git rev-parse HEAD
git rev-parse 'HEAD^{tree}'
git diff --check a2a81cee8995c831aca472836e2be446994ba070 HEAD
git diff --stat a2a81cee8995c831aca472836e2be446994ba070 HEAD
git diff a2a81cee8995c831aca472836e2be446994ba070 HEAD -- android/src/main/java android/build.gradle .github/workflows
```

The [public validation workflow](../.github/workflows/validate-pending-fixes.yml) checks out that pinned source and tree rather than the validation branch's tip. It installs an npm archive into the example, verifies the installed native sources, runs TypeScript and ESLint, then runs all 41 Robolectric tests and builds the installed Debug AAR with `newArchEnabled=false` and `true`. It requires zero failures, errors or skips, including the executor reattachment suite, and publishes a per-suite summary, JUnit reports, build logs and AARs.

[Run 36891628860](https://github.com/zack-dev-cm/react-native-camera-kit/actions/runs/36891628860) passes both architecture jobs at that exact source revision: 41 tests per job with zero failures, errors or skips, TypeScript and ESLint, installed native-source comparisons and both Debug AAR builds. The job summaries list each test suite; the two downloadable artifacts contain the summaries, JUnit reports, build logs and AARs.

The validation covers Android unit regressions and installed-library builds. It does not run physical-camera tests, new iOS runtime tests, or the upstream example workflow. Existing independent PR descriptions retain their earlier platform evidence. Upstream Build and Linter runs still need maintainer approval.
