# Hook, Hook-tool, Event?

## Basic concept

- An event is a data structure that encapsulates part of the execution context of a charm.
    - https://juju.is/docs/sdk/events
    > The events have not changed across the various frameworks. Only the way of implementing the hooks handling them has changed.
- A hook is an event handler.
    - https://juju.is/docs/sdk/hook
    > - In the hooks-based framework, they were executable files placed in the /hooks folder of a charm.
    > - In the reactive-framework, they were functions in a single Python file.
    > - In the current, Ops (ops) framework they are code blocks, typically methods of a class that inherits from CharmBase and represents the charm object.
- Hook tools are Bash scripts that come with your juju installation that charm authors can use to talk to the juju API.


## Example in ubuntu(Operator)

```sh
$ juju deploy ubuntu
$ juju ssh ubuntu/0

# List hook tools
$ ls /var/lib/juju/tools/unit-ubuntu-0

action-fail              close-port            juju-log     k8s-spec-get  opened-ports        relation-get   state-get    storage-list
action-get               config-get            juju-reboot  k8s-spec-set  payload-register    relation-ids   state-set    unit-get
action-log               credential-get        jujuc        leader-get    payload-status-set  relation-list  status-get
action-set               downloaded-tools.txt  jujud        leader-set    payload-unregister  relation-set   status-set
add-metric               goal-state            k8s-raw-get  network-get   pod-spec-get        resource-get   storage-add
application-version-set  is-leader             k8s-raw-set  open-port     pod-spec-set        state-delete   storage-get


# List hooks
$ ls /var/lib/juju/agents/unit-ubuntu-0/charm/hooks

install  start  upgrade-charm

$ cat /var/lib/juju/agents/unit-ubuntu-0/charm/hooks/install
$ cat /var/lib/juju/agents/unit-ubuntu-0/charm/hooks/start

```

`/var/lib/juju/agents/unit-ubuntu-0/charm/hooks/install`

```python
#!/bin/sh

JUJU_DISPATCH_PATH="${JUJU_DISPATCH_PATH:-$0}" PYTHONPATH=lib:venv \
  exec ./src/charm.py
```

`/var/lib/juju/agents/unit-ubuntu-0/charm/hooks/start`

```python
#!/bin/sh

JUJU_DISPATCH_PATH="${JUJU_DISPATCH_PATH:-$0}" PYTHONPATH=lib:venv \
  exec ./src/charm.py
```

## Example in infra-node(reactive)

```sh
$ juju deploy infra-node
$ juju ssh infra-node/0


# Hook-tools is basicly the same as operator
$ ls /var/lib/juju/tools/unit-infra-node-0

action-fail  application-version-set  goal-state   jujud         leader-get    payload-register    relation-get   state-delete  storage-add
action-get   close-port               is-leader    k8s-raw-get   leader-set    payload-status-set  relation-ids   state-get     storage-get
action-log   config-get               juju-log     k8s-raw-set   network-get   payload-unregister  relation-list  state-set     storage-list
action-set   credential-get           juju-reboot  k8s-spec-get  open-port     pod-spec-get        relation-set   status-get    unit-get
add-metric   downloaded-tools.txt     jujuc        k8s-spec-set  opened-ports  pod-spec-set        resource-get   status-set


# Hooks
$ ls /var/lib/juju/agents/unit-infra-node-0/charm/hooks/

config-changed  leader-elected                        nrpe-external-master-relation-changed   post-series-upgrade  start          upgrade-charm
hook.template   leader-settings-changed               nrpe-external-master-relation-departed  pre-series-upgrade   stop
install         nrpe-external-master-relation-broken  nrpe-external-master-relation-joined    relations            update-status

$ cat /var/lib/juju/agents/unit-infra-node-0/charm/hooks/start
```

`/var/lib/juju/agents/unit-infra-node-0/charm/hooks/start`

```python
#!/usr/bin/env python3

# Load modules from $JUJU_CHARM_DIR/lib
import sys
sys.path.append('lib')

from charms.layer import basic  # noqa
basic.bootstrap_charm_deps()

from charmhelpers.core import hookenv  # noqa
hookenv.atstart(basic.init_config_states)
hookenv.atexit(basic.clear_config_states)


# This will load and run the appropriate @hook and other decorated
# handlers from $JUJU_CHARM_DIR/reactive, $JUJU_CHARM_DIR/hooks/reactive,
# and $JUJU_CHARM_DIR/hooks/relations.
#
# See https://jujucharms.com/docs/stable/authors-charm-building
# for more information on this pattern.
from charms.reactive import main  # noqa
main()
```

## Lifecycle

![](https://discourse.charmhub.io/uploads/default/optimized/2X/e/e301ec466d08857474df3124bc58b934adacc91e_2_690x248.png)

> https://juju.is/docs/sdk/ops



# References

- https://discourse.charmhub.io/t/the-hook-environment-hook-tools-and-how-hooks-are-run/1047
