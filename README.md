# Pydantic models for Nokia SR Linux

> [!IMPORTANT]
> This is an experimental project that may graduate to a proper set of Pydantic models for SR Linux in the near future.
>
> Limitations:
>
> - models are generated without if-features flag set, this means all if-features are implicitly enabled. Note, that this will render models for features that your hardware might not have.
> - if you found additional limitations, please open an issue or reach out in [Discord](https://discord.gg/tZvgjQ6PZf).

## Installation

```bash
uv add git+https://github.com/srl-labs/pydantic-srlinux
```

or with `pip`:

```bash
pip install git+https://github.com/srl-labs/pydantic-srlinux
```

## Usage

With the `pydantic-srlinux` package installed, you can get access to the generated models by importing them from the `pydantic_srlinux` module.

```python
import pydantic_srlinux.models.interfaces as srl_if

e1_1 = srl_if.InterfaceListEntry(
    name="ethernet-1/1",
    admin_state=srl_if.EnumerationEnum.enable,
    vlan_tagging=True,
    subinterface=[
        srl_if.SubinterfaceListEntry(
            index=0,
            type="bridged",
            admin_state=srl_if.EnumerationEnum.enable,
            vlan=srl_if.VlanContainer(
                encap=srl_if.EncapContainer(
                    single_tagged=srl_if.SingleTaggedContainer(
                        vlan_id=srl_if.VlanIdType(100)
                    )
                )
            ),
        )
    ],
)
```

The following top level imports are available:

| Model            | Schema URL                                                                                                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| acl              | [acl doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Facl_schema.json)                           |
| bfd              | [bfd doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Fbfd_schema.json)                           |
| interfaces       | [interfaces doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Finterfaces_schema.json)             |
| network_instance | [network_instance doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Fnetwork_instance_schema.json) |
| platform         | [platform doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Fplatform_schema.json)                 |
| qos              | [qos doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Fqos_schema.json)                           |
| routing_policy   | [routing_policy doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Frouting_policy_schema.json)     |
| system           | [system doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Fsystem_schema.json)                     |
| tunnel_interface | [tunnel_interface doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Ftunnel_interface_schema.json) |
| tunnel           | [tunnel doc](https://json-schema.app/view/%23?url=https%3A%2F%2Fraw.githubusercontent.com%2Fsrl-labs%2Fpydantic-srlinux%2Frefs%2Fheads%2Fmain%2Fschemas%2Ftunnel_schema.json)                     |

The top level imports match the top level YANG objects that you will see in the SR Linux NOS. Consult with the schema doc or IDE hints to find the structure of the objects and their hierarchy.

With an object instance, you can produce the JSON serialized representation of the object:

```python
print(
    e1_1.model_dump_json(indent=2, exclude_none=True, exclude_unset=True, by_alias=True)
)
```

And use the serialized JSON with gNMI or JSON-RPC interfaces of SR Linux. Have a look at the JSON-RPC-based example in the [example](example) directory.

## Generate YANG mapping file

Clone the [nokia/srlinux-yang-models](https://github.com/nokia/srlinux-yang-models) repo.

```bash
git clone https://github.com/nokia/srlinux-yang-models.git
```

Generate the YANG map file:

```bash
./yang_map.py --dir {path to a dir where you cloned nokia/srlinux-yang-models} --version v24.10.1
```

The script will navigate to the directory with the yang modules identified by the `--dir` argument and checkout the tag specified by the `--version` argument.

Create an environment variable `${SRL_YANG_REPO_DIR}` with the path to the cloned repo as this env var is used in the YANG map file to keep it generically applicable.

```
export SRL_YANG_REPO_DIR=~/path/to/srlinux-yang-models
```

## Generate Pydantic models (automatic)

To manually regenerate the Pydantic models for a given module, run the following command:

```bash
# generate_models.py --module <model name>
./generate_models.py --module srl_nokia-acl
```

The full list of the top level modules:

```
./generate_models.py --module srl_nokia-acl
./generate_models.py --module srl_nokia-bfd
./generate_models.py --module srl_nokia-interfaces
./generate_models.py --module srl_nokia-network-instance
./generate_models.py --module srl_nokia-platform
./generate_models.py --module srl_nokia-qos
./generate_models.py --module srl_nokia-routing-policy
./generate_models.py --module srl_nokia-system
./generate_models.py --module srl_nokia-tunnel-interfaces
./generate_models.py --module srl_nokia-tunnel
```

This will generate the `xxx.py` file in the `pydantic_srlinux/models` directory as well as create the CLI command used to generate the Pydantic models and store it in the `./temp` directory.

## Pyang and Yanglint

The yang map file contains pastable commands to generate YANG trees for all the modules. The commands are generated for `pyang` and `yanglint`. Before using these commands, make sure that the env var `${SRL_YANG_REPO_DIR}` is set to the path of the cloned YANG repo.

### Yanglint

Yanglint is easy to consume in a container flavor; paste this alias in your shell:

```
alias yanglint='docker run --rm -i -t -v ${SRL_YANG_REPO_DIR}:/${SRL_YANG_REPO_DIR} ghcr.io/hellt/yanglint:3.7.8 $@'
```

Then you will be able to paste the yanglint command from the map file and get the tree output.

By default yanglint will output warnings for all the implemented modules (these are the modules that your particular module relies on). You can suppress the warnings by adding `-Q`
