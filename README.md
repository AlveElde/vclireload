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
- `-t`: Tags (Default: `vclireload`)
- `-d`: Domains (Default: `none`)
- `-p`: VCL directory path (Default: `cwd`)

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
alve@alvepad:~$ cd /tmp/vcl && vclireload
Reloading root VCL Group vclireload (Main VCL: main.vcl, VCL path: /tmp/vcl, tags: vclireload)
Configuration saved to: /home/alve/.vcli.yml
Login successful.
Adding File /tmp/vcl/main.vcl
Success: File main.vcl has id 6
Adding File /tmp/vcl/utils.vcl
Success: File utils.vcl has id 7
Success: Tag vclireload has id 6
Adding Deployment vclireload_dc (--tags 6)
Success: Deployment vclireload_dc has id 7
Adding VCL Group vclireload (--dep 7 --vcl 6 --inc 7 --root)
Success: VCL Group vclireload has id 6
Compiling VCL Group vclireload (id: 6)
Success: VCL Group vclireload compiled
Deploying VCL Group vclireload (id: 6)
Success: VCL Group vclireload deployed
```

All files in `/tmp/vcl` (with the`.vcl` extension) are added to the Controller, and a VCL Group called `vclireload` is created and deployed. Since no domains have been defined, we end up with a root deployment.

Running it again, we can see that the relevant resources are updated and redeployed:

```
alve@alvepad:~$ cd /tmp/vcl && vclireload
Reloading root VCL Group vclireload (Main VCL: main.vcl, VCL path: /tmp/vcl, tags: vclireload)
Configuration saved to: /home/alve/.vcli.yml
Login successful.
Updating File /tmp/vcl/main.vcl
Success: File main.vcl has id 6
Updating File /tmp/vcl/utils.vcl
Success: File utils.vcl has id 7
Success: Tag vclireload has id 6
Updating Deployment vclireload_dc (id: 7 --tags 6)
Success: Deployment vclireload_dc has id 7
Updating VCL Group vclireload (id: 6 --dep 7 --vcl 6 --inc 7 --root)
Success: VCL Group vclireload has id 6
Compiling VCL Group vclireload (id: 6)
Success: VCL Group vclireload compiled
Deploying VCL Group vclireload (id: 6)
Success: VCL Group vclireload deployed
```

When this script is run for the first time, you will be prompted for an API-GW endpoint, a username and a password. To avoid this prompt, you can instead set the following environment variables:
```
VARNISH_CONTROLLER_CLI_USERNAME=<username>
VARNISH_CONTROLLER_CLI_PASSWORD=<password>
VARNISH_CONTROLLER_CLI_ENDPOINT=<endpoint>
```
## Dependencies

This tool only needs `varnish-controller-cli` installed locally to work. No other dependencies.
