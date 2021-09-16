# FuseML Release Process

## Repositories under release management 

Every repository that is part of release management features one release branch
for every official FuseML release. Currently, release branches are created from `main`
to track every minor release, e.g. `v0.1` and `v0.2`, but this may change after FuseML
exits alpha status. Tags for patch releases, e.g. `v0.2.1` are created in their respective
release branches.

The next sections document the release artifacts for every repository that is part
of release management, as well as the code changes that need to be applied to the
release branch whenever a new release is created and the release trigger.

### [https://github.com/fuseml/fuseml](https://github.com/fuseml/fuseml)

#### Release artifacts

* fuseml-installer binary

#### Release changes

* for minor and patch releases: update the fuseml-core k8s deployment manifest to use the release fuseml-core image tag instead of the `dev` tag
* for minor release only: update the default extension repository to point to the `release-*` extension branch instead of `main`

Examples:
 * https://github.com/fuseml/fuseml/pull/211
 * https://github.com/fuseml/fuseml/pull/239

#### Release triggers

* push a new tag in the release branch; the rest of the release is automated through github actions

### [https://github.com/fuseml/fuseml-core](https://github.com/fuseml/fuseml-core)

#### Release artifacts

* fuseml CLI binary
* fuseml-core binary
* fuseml-core container image

#### Release changes

Nothing needs to be done. Everything is automated through github actions and the Makefile

#### Release triggers

* push a new tag in the release branch; the rest of the release is automated through github actions

### [https://github.com/fuseml/extensions](https://github.com/fuseml/extensions)

#### Release artifacts

* extension container images
  * mlflow-builder
  * kfserving-predictor
* mlflow tracker container image
* extension installer descriptor files

#### Release changes

* for minor releases only: modify the installer extension descriptors to point to kustomize files and charts in the release branch

Example: https://github.com/fuseml/extensions/pull/28

#### Release triggers

* push a new tag in the release branch; the rest of the release is automated through github actions

### [https://github.com/fuseml/examples](https://github.com/fuseml/examples)

#### Release Artifacts

* example codesets
* example workflows
* example webapp(s) and webapp container image(s)

#### Release changes

* for minor and patch releases: update the workflow examples to point to the released container image tags.

  Examples:
  * https://github.com/fuseml/examples/pull/11
  * https://github.com/fuseml/examples/pull/12

* (optional, only if changes occur) for minor and patch releases: re-tag and update the build/release container images for webapp examples and update the k8s manifests for the webapp examples to point to the released container image tags

#### Release triggers

* none: the repository itself is part of the release

### [https://github.com/fuseml/docs](https://github.com/fuseml/docs)

#### Release artifacts

* documentation

#### Release changes

The following changes are only needed for minor releases:

* stop publishing the docs from the previous release as default/latest. Example: https://github.com/fuseml/docs/pull/23
* change the github actions to publish the docs for the new release as default/latest. Example: https://github.com/fuseml/docs/pull/24
* change the `git clone` target in the tutorial to point to the release fuseml/examples branch.
* include swagger generated files
* re-generate installer/CLI outputs to match the release

Example: https://github.com/fuseml/docs/pull/28

#### Release triggers

* push changes to the documentation release branch through regular github PR; the versioned documentation
for every release is rebuilt and published automatically on every PR merge, implemented through github actions
