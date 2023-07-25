# vclireload

It's `varnishreload`, but for the Varnish Controller.

This script aims to simplify automated VCL deployments through the Varnish Controller API. It currently only supports root deployments, but will eventually support shared deployments as well.

## Usage

```
vclireload [options]
```

**Options:**

- `-g`: Name of the VCL Group (Default: `vclireload`)
- `-t`: Comma separated list of tags (Default: `vclireload`)
- `-m`: Name of the Main VCL (Default: `main.vcl`)
- `-p`: Path to VCL directory (Default: `$cwd`)

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

# Use a different VCL directory than CWD
vclireload -g example -m example.vcl -t tag1,tag2 -p /tmp/vcl
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
Reloading VCL Group vclireload (Main VCL: main.vcl, VCL path: /tmp/vcl, tags: vclireload)
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

The result is that all files in `/tmp/vcl` that end in `.vcl` are added to the Controller, and a VCL Group called `vclireload` is created and deployed. Running it again, we can see that the relevant resources are updated and redeployed:

```
alve@alvepad:~$ cd /tmp/vcl && vclireload
Reloading VCL Group vclireload (Main VCL: main.vcl, VCL path: /tmp/vcl, tags: vclireload)
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

When run for the first time, the script will prompt you to provide an API-GW endpoint, a username and a password. To avoid this prompt, you can set the following environment variables:
```
VARNISH_CONTROLLER_CLI_USERNAME=<username>
VARNISH_CONTROLLER_CLI_PASSWORD=<password>
VARNISH_CONTROLLER_CLI_ENDPOINT=<endpoint>
```
## Dependencies

This tool only needs `varnish-controller-cli` installed locally to work. No other dependencies.
