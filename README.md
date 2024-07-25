# vclireload

It's `varnishreload`, but for the Varnish Controller.

This script aims to simplify automated VCL deployments through the Varnish Controller API. Create dedicated or shared deployments and keep them updated by running the script from your machine, a GitHub Action, or any other deployment pipeline.

## Usage

```
vclireload [options]
```

**Options:**

- `-g`: VCL Group name (Default: `vclireload`)
- `-m`: Main VCL name (Default: `main.vcl`)
- `-t`: Tag(s) (Default: `vclireload`)
- `-d`: Domain(s) (Default: `none`)
- `-p`: VCL directory/file path(s) (Default: `cwd`)

**Example Usage:**

```sh
# Deploy VCL from CWD with default values
vclireload

# Use a different name for the VCL Group
vclireload -g example

# Use a different name for the Main VCL
vclireload -g example -m example.vcl

# Define custom tags for the deployment
vclireload -g example -m example.vcl -t tag1,tag2

# Define a shared deployment for example.com and test.com
vclireload -g example -m example.vcl -t tag1,tag2 -d example.com,test.com

# Use a different VCL directory than CWD
vclireload -g example -m example.vcl -t tag1,tag2 -d example.com -p /tmp/vcl

# Deploy a single VCL file
vclireload -g example -m example.vcl -p ./example.vcl
```

**See it in action:**

Given the following directory structure:
```sh
/tmp/vcl
├── main.vcl
└── utils.vcl
```

Running the script for the first time, we get the following output:
```
alve@hyperion:~$ vclireload -p /tmp/main.vcl
Reloading root VCL Group vclireload (Main VCL: main.vcl, VCL Paths: /tmp/main.vcl, Tags: vclireload)
Configuration saved to: /home/alve/.vcli.yml
Login successful.
Adding File /tmp/main.vcl
Success: File main.vcl has id 74
Success: Tag vclireload has id 6
Adding Deployment vclireload_dc (--tags 6 --max 128)
Success: Deployment vclireload_dc has id 29
Selecting root deployment type
Adding VCL Group vclireload (--dep 29 --vcl 74  --root)
Success: VCL Group vclireload has id 29
Compiling VCL Group vclireload (id: 29)
Success: VCL Group vclireload compiled
Deploying VCL Group vclireload (id: 29)
Success: VCL Group vclireload deployed
```

All files in `/tmp/vcl` (with the`.vcl` extension) are added to the Controller, and a VCL Group called `vclireload` is created and deployed. Since no domains have been defined, we end up with a root deployment.

When this script is run for the first time, you will be prompted for an API-GW endpoint, a username and a password. To avoid this prompt, you can instead set the following environment variables:
```
VARNISH_CONTROLLER_CLI_USERNAME=<username>
VARNISH_CONTROLLER_CLI_PASSWORD=<password>
VARNISH_CONTROLLER_CLI_ENDPOINT=<endpoint>
```

## Dependencies

This tool needs `varnish-controller-cli` and `jq` to be installed locally.

## Compatibility

Tested to work with version 6.3.2 of the Varnish Controller.
