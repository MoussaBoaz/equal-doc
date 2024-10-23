# Managing Complex Cases - Patterns and Solutions



## Case : Handling Data Consistency in Complex Entity Logic

### Presentation
For scenarios involving complex entities logic, ensuring data consistency can be challenging and involve non-trivial logic. 

Such a situation occurs when updating certain fields requires additional processing to ensure coherence, whether the updates involve not only computed fields but also direct fields that must be assigned according to specific logic, or when child objects need to be automatically regenerated, or finally, when changes to child objects trigger modifications to parent objects, and vice versa.

Additionally, maintaining coherence may sometimes require a specific sequence of updates, as the order in which these updates are applied is crucial to ensuring consistency and avoiding data conflicts

In such scenarios, relying solely on `onupdate` callbacks can be insufficient or even inappropriate.

### Strategy

The solution involves a strategy combining the use of classes and controllers.

Within the targeted classes, `refresh` methods are created to group the logic that ensures data consistency. These methods can be called on demand, based on the following principles:

* The method names `refresh` are arbitrary but follow a coherent logic.
* `refresh` methods are designed to update only one object at a time.
* These methods act solely on a single object level, without affecting higher or lower levels.
* `refresh` methods are not triggered automatically. They are invoked only when a valid version of the data is required, typically at the end of the update cycle, after all modifications have been applied.

In parallel, controllers are defined to handle the actions executed via the front-end application. It is important to restrict available actions on the front-end to only authorized operations, excluding direct calls to functions like `model_update` or `model_delete`.

These controllers contain business logic and group the appropriate actions. Based on the fields modified, they trigger the necessary refreshes on the affected objects via the `refresh` methods. These refreshes are limited to a single object level or, in some cases, two levels—especially when child objects directly depend on the configuration of their parent (e.g., assignments, consumption, etc.).

When necessary, these controllers use the `ObjectManager::disableEvents()` method to avoid any conflicts with other events defined on the classes or fields involved.
