# A guidelines for juju reactive framework and operator

# Agenda

- Juju dispatch

- Architectures

- What is hook?

- Layers vs Libraries

## History

https://juju.is/docs/sdk/history#heading---the-reactive-framework


## Hook, hook-tool, event?


[Hook, Hook-tool, Event?](./hook-hooktool-event.md)

## Event Handling

[Event Handling](./event-handling.md)


## The same parts

- Life-cycle
- charmcraft
- python-libjuju
    - Call juju API with python
- charm-helpers
    - Mostly local's job
    - Communicate with juju API with hook tool


## Set Status

### Reactive set_status


```python
from charmhelpers.core import hookenv

@when('files.render')
def deploy_files():
    hookenv.status_set('active', 'ready')
```

### Operator set_status

```python
from ops.model import ActiveStatus

class UbuntuCharm(CharmBase):

    ...

    def _set_version(self, _):
        self.unit.status = ActiveStatus()
```

> Remember no matter use charmhelpers's `hookenv` or ops's `self.unit.status`. Both will call hook-tool `status-set` with subprocess in the backend.

## Layers vs Libraries

### Layers

> https://ubuntu.com/blog/charming-2-0-now-with-100-more-awesome
> https://charmsreactive.readthedocs.io/en/latest/layer-basic.html

Charms can be seen as a build-time artifact on the workstation machine. As a developer of many charms, its surfaced that when charming in a particular domain, say PHP website creation, I constantly need to setup apache (or nginx) and the php5-fpm daemon. Tuning these can be application specific, but normally I just set them up, and start modeling the application deployment. But I cannot do anything until this plumbing is complete.

Charm Layers solve for the complexity of abstracting away these common tasks into their own modules, which can then be called, included, and overridden in complimentary layers.

For example:

https://github.com/juju-solutions/layer-basic/blob/master/hooks/hook.template

This is the hook file we seen before in the `/var/lib/juju/agents/{unit-name}/charm/hooks`

https://github.com/stub42/layer-apt

> See https://charmsreactive.readthedocs.io/en/latest/structure.html?highlight=layer#charm-layer for more details


### Libraries

- A way to easily share and reuse logic, this is particularly important given the two-sided nature of relations.
- Charm libraries are directly integrated with Charmhub, increasing the discoverability of relevant libraries (including their documentation) when exploring available charms.
- Create inside charm project.

```sh
charmcraft create-lib demo
```

> We will show the details later in integration part

## Build relation/integration

TODO

- Connect to operator's relation from reactive

## Testing

TODO

### Unit test

-  testing harness

### integration test

- https://github.com/charmed-kubernetes/pytest-operator
- zaza

## Pebble
