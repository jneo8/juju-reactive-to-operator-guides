# A guidelines for juju reactive framework and operator

# Agenda

- Juju dispatch

- Architectures

- History

- What is hook and hook-tool?

- Layers vs Libraries

- Integration/Relation

## History

https://juju.is/docs/sdk/history#heading---the-reactive-framework

## Architectures

- [Operator Architecture](./operator-architecture.md)


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

[layers vs Libraries](./layer-library.md)


## Build relation/integration

[Integration-Relation](./integration-relation.md)

## Testing

TODO

### Unit test

-  testing harness

### integration test

- https://github.com/charmed-kubernetes/pytest-operator
- zaza

## Pebble
