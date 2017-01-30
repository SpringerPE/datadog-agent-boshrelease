# Datadog Agent release for BOSH

* For all stemcells (Python source implementation)
* Automatically defines tags based on deployments, names and jobs
* Process, network, ntp and disk integrations by default
* Monit processes are added automatically to process integration
* You can define additional integrations


# Configuration

Upload the release to Bosh director

Create a `runtime-config.yaml` file:
```
releases:
- name: datadog-agent
  version: 1

addons:
- name: dd-agent
  jobs:
  - name: dd-agent
    release: datadog-agent
  properties:
    dd:
      use_dogstatsd: yes
      api_key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
      tags: ["pe", "bosh", "bosh:bosh-exp"]

tags:
  owner: pe
  email: platform-engineering@springernature.com
```

Upload runtime-config to Bosh Director: `bosh update runtime-config  runtime-config.yaml`

Re-deploy the deployments to automatically add the agent.

You can also add the release to the manifest and deploy it


# Development

After cloning this repository, run:

```
git submodule init
git submodule update
```
to update the gohai submodule. Gohai is a binary (golang) program used by Datadog Agent to collect
basic information from the server. There is a script `bosh_prepare` which also runs the git
submouldes commands and downloads all sources to the `blobs` directory:
```
./bosh_prepare
```

It will get all sources specified in the packages/*/spec files (commented out) for
local development, then you can create the release (final) with:
```
./bosh_final_release
```
which will upload the tarball to github and make the new blobs public available in the S3 bucket


### Note

`bosh_prepare` ensures that `gohai` git submodule is populated in `src/gohai`. In order
to avoid including a git package -only needed because of `go get` command to download
the dependencies- the script runs `go get` locally to get all dependencies, so it is
assuming you have `go` installed. After those dependencies were downloaded, the
binary package can be compiled, but a new folder `src/gohai/src` appears, because of
that bosh will complain about untracked git changes, but there are two workarounds:

* use `bosh create release --force`
* add to the `.gitmodules` file a config parameter for ignoring dirty state of the submodule: `ignore = dirty`

Here, the second option is used.


# Author

Springer Nature Platform Engineering, Jose Riguera Lopez (jose.riguera@springer.com)

Copyright 2017 Springer Nature



# License

Apache 2.0 License

