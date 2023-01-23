# Operator architecture

```mermaid
flowchart TD

Object -.-> ObjectEvents -.-> FrameworkEvents
ObjectEvents -.-> CharmEvents --"self.install"--> EventSoruce("EventSource(InstallEvent)") --> InstallEvent

EventBase -.-> HookEvent
HookEvent -.-> InstallEvent
HookEvent -.-> StartEvent

Object -.-> Framework --self.on--> FrameworkEvents
Object -.-> CharmBase --self.on--> CharmEvents
CharmBase --self.framework--> Framework
```

*How event handler handling the events?*

Every Object belongs to exactly one framework. Every object has a parent, which might be a framework.
Operator track a *path to object", which is the path to the parent, plus the object's unique identifier.
Event handlers use this identity to track the destination of their events, and the Framework used this id to track persisted state between event executions.
