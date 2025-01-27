# beet-bolt-plugins
A collection of beet/bolt plugins written by me

## bolt-item
This is a plugin providing an abstraction for custom items using decorators and python classes.

```py
from namespace:bolt-item import item

@item
class MyAwesomeItem:
    item_model = "diamond"
    item_name = "Awesome Item"
```

### Basics
Every class decorated by the `@item` decorator is considered a bolt-item item.

Any class attribute name that matches a component gets considered one and copied to the resulting item.

### Optaining the Item
You can create a loot table or a crafting recipe for the item like this:
```py
MyAwesomeItem.loot_table()
MyAwesomeItem.recipe([
    ["diamond", "diamond",    "diamond"],
    ["diamond", "gold_ingot", "diamond"],
    ["diamond", "iron_ingot", "diamond"],
])
```

The `.loot_table` method can also take a path as an argument to overwrite the default path.

The `.recipe` method takes either a shaped recipe like shown above, a list of ingredients (`["iron_ingot","#logs"]`) for a shapeless recipe or a dict with `input` and `material` (`{"input":"netherite_sword","material":"#cat_food"}`) for a crafting transmute recipe.
The `.recipe` method can also take a path as an argument to overwrite the default path.

### Component Transformers
```py
def prefix_item_name(cls, v):
    return f'{cls.custom_prefix} {v}'

@item
class PrefixedNameItem:
    item_name = "Regular Name"
    custom_prefix = "[SPECIAL]"
    item_name_transformer = prefix_item_name
```

A transformer is a function that takes the class and the original value as parameters and just outputs what that component should be instead. This can be used to translate text, manage textures or what ever you can think of.

### Method Decorators
```py
from namespace:bolt-item import item, on_consume

@item
class ScreamingCookie:
    consumable = {}

    @on_consume()
    def scream():
        say AAAAAAAAAAAAAAAAAAA!
```
Method decorators are placed infront of methods/functions that should run when a specific event happens.
By default `@on_consume`, `@on_equip`, `@on_unequip`, `@on_attack` and `@on_attacked` are included, where [tungsten](https://github.com/ps-dps/mc-Tungsten) and [argon](https://github.com/PuckiSilver/mc-argon) are required to be able to use all of them.

`@on_consume` can also take a `return_item` boolean to return the item after it's been consumed. This can be used to have right click abilities with cooldown.

`@on_equip`, `@on_unequip`, `@on_attack` and `@on_attacked` each can take a `slot` string argument to determine where the item should be kept in order for the method/function to be triggered.

### Inheritance
```py
class MyPackItem:
    lore = [{"text":"My Pack","color":"blue","italic":false}]

@item
class FakeDiamond(MyPackItem):
    item_model = "diamond"
    item_name = "Ultra Very Real Diamond, Trust"
```
Components, Transformers and decorated methods/functions are inherrited from parent classes, even if they don't include the `@item` decorator.
