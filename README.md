# vclireload

It's `varnishreload`, but for the Varnish Controller.

This script aims to simplify automated VCL deployments through the Varnish Controller API. It currently only supports root deployments, but will eventually support shared deployments as well.

## Usage

```
vclireload -g <VCL Group> -t <tags> -f <Main VCL> -p <VCL Path>
```

For the moment, the script expects the appropriate environment variables to be set for `vcli login` to succeed. This means the current environment variables should be set:
```
VARNISH_CONTROLLER_CLI_USERNAME=<username>
VARNISH_CONTROLLER_CLI_PASSWORD=<password>
VARNISH_CONTROLLER_CLI_ENDPOINT=<endpoint>
```
