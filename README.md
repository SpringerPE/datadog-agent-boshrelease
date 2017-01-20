# Datadog Agent release for BOSH

* For all stemcells (Python source implementation)
* Automatically defines tags based on deployments, names and jobs
* Automatically adds the monit processes to processes metrics
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
to update the gohai submodule. Gohai is a binary (golang) used by Datadog Agent to collect
basic information from the server. There is a script `bosh_prepare` which also does that
and downloads all sources to the `blobs` directory:
```
./bosh_prepare
```

It will get all sources specified in the packages/*/spec files (commented out), then you
can create the release with:
```
bosh create release --force --name datadog-agent-boshrelease
```

and upload to BOSH director:

```
bosh upload release
```

To create a final release:
```
bosh create release --force --with-tarball --final --name datadog-agent
git tag <version>
git push origin --tags
```
and then upload the tarball to github


# Author

Jose Riguera Lopez (jose.riguera@springer.com)

Springer Nature Platform Engineering


# License

Apache 2.0 License

