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

**Examples:**

```sh
# Deploy a single VCL
vclireload -p /tmp/vcl/example.vcl

# Deploy all VCLs from a directory
vclireload -p /tmp/vcl -m example.vcl

# Customize the deployment
vclireload -p /tmp/vcl -m example.vcl -g example -t tag1,tag2 -d example.com,test.com
```

**See it in action:**

Given the following directory structure:
```sh
/tmp/vcl
├── main.vcl
└── utils.vcl
```

Running `vclireload -p /tmp/vcl` we get the following output:

```
Reloading root VCL Group vclireload (Main VCL: main.vcl, VCL Paths: /tmp/vcl, Tags: vclireload)
Configuration saved to: /home/alve/.vcli.yml
Login successful.
Adding File /tmp/vcl/main.vcl
Success: File main.vcl has id 77
Adding File /tmp/vcl/utils.vcl
Success: File utils.vcl has id 78
Success: Tag vclireload has id 6
Adding Deployment vclireload_dc (--tags 6 --max 128)
Success: Deployment vclireload_dc has id 30
Selecting root deployment type
Adding VCL Group vclireload (--dep 30 --vcl 77 --inc 78 --root)
Success: VCL Group vclireload has id 30
Compiling VCL Group vclireload (id: 30)
Success: VCL Group vclireload compiled
Deploying VCL Group vclireload (id: 30)
Success: VCL Group vclireload deployed
```

All files in `/tmp/vcl` (with the`.vcl` extension) are added to the Controller, and a VCL Group called `vclireload` is created and deployed. Because no domains were defined with `-d`, a root deployment is selected.

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
