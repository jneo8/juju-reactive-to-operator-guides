# Layers vs Libraries

The concept of layer/library is about sharing code. But they have different ways to create a new one and include in another charm.

## Layers

> https://ubuntu.com/blog/charming-2-0-now-with-100-more-awesome
> https://charmsreactive.readthedocs.io/en/latest/layer-basic.html

Charms can be seen as a build-time artifact on the workstation machine. As a developer of many charms, its surfaced that when charming in a particular domain, say PHP website creation, I constantly need to setup apache (or nginx) and the php5-fpm daemon. Tuning these can be application specific, but normally I just set them up, and start modeling the application deployment. But I cannot do anything until this plumbing is complete.

Charm Layers solve for the complexity of abstracting away these common tasks into their own modules, which can then be called, included, and overridden in complimentary layers.

For example:

https://github.com/juju-solutions/layer-basic/blob/master/hooks/hook.template

This is the hook file we seen before in the `/var/lib/juju/agents/{unit-name}/charm/hooks`. The reactive charm use this layer to register basic lifecycle hooks.

Another example is apt layer that can be a helper to handle the package install and events before/after apt install package: https://github.com/stub42/layer-apt

> See https://charmsreactive.readthedocs.io/en/latest/structure.html?highlight=layer#charm-layer for more details

For overview of available layers, please take a look at: https://github.com/juju/layer-index


## Libraries

- A way to easily share and reuse logic, this is particularly important given the two-sided nature of relations.
- Charm libraries are directly integrated with Charmhub, increasing the discoverability of relevant libraries (including their documentation) when exploring available charms.
- Create inside charm project with charmcraft:
    ```sh
    charmcraft create-lib demo
    ```

### Questions

- Can I use `layer` in operator?

    - No, the way how layer trigger events is handle by the *flag* which is not exists in operator.

- *If we need to use `apt layer` to install package for machine charm in reactive. How can we install package in operator?*

    - https://charmhub.io/operator-libs-linux/libraries/apt

- *How about snap?*

    - https://charmhub.io/operator-libs-linux/libraries/snap

- *Is there any similiar package like reactive layer-index*?

    - https://juju.is/docs/sdk/library-index


> We will show the details later in integration part
