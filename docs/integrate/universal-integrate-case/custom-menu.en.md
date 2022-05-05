---
id: custom-menu
title: Custom Menu
slug: custom-menu
order: 2
---

## Register Custom Menus

There are two common modes for registering custom menus

- Register a new menu item
- Registers submenus with existing menu items

OpenSumi provides the ability to customize menus based on OpenSumi's [Contribution](../../develop/basic-design/contribution-point)mechanism, to implement `MenuContribution` and call the methods provided by `menuRegistry`.

```typescript
interface MenuContribution {
  registerMenus?(menus: IMenuRegistry): void;
}

interface IMenuRegistry {
  // Register a new menu item
  registerMenubarItem(
    menuId: string,
    item: PartialBy<IMenubarItem, 'id'>
  ): IDisposable;
  // Register submenus with existing menu items
  registerMenuItem(
    menuId: MenuId | string,
    item: IMenuItem | ISubmenuItem | IInternalComponentMenuItem
  ): IDisposable;
}
```

## Register a new menu item

For example, if we want to register a new `terminal` menu item and want it to be displayed in the first item, we call `registry.registerMenuBarItem` and pass `order: 0` to indicate that it is positioned in the first item.

```typescript
import {
  MenuContribution,
  IMenuRegistry,
  MenuId
} from '@opensumi/ide-core-browser/lib/menu/next';

const terminalMenuBarId = 'menubar/terminal';

@Domain(MenuContribution)
class MyMenusContribution implements MenuContribution {
  registerMenus(registry: IMenuRegistry) {
    registry.registerMenubarItem(terminalMenuBarId, {
      label: 'terminal',
      order: 0
    });
  }
}
```

![Menu](https://img.alicdn.com/imgextra/i4/O1CN01AMrUFm1E5RVxMZ417_!!6000000000300-2-tps-3808-2414.png)

## Register submenus with existing menu items

We have registered the `terminal` menu item as the first item in the menubar, but it doesn't have submenu yet, and will not respond when clicked. We need to register another set of submenu for it. Call `registerMenuItem` of `registry`'to register a single menu item, or you can use the `registerMenuItems` method to register multiple submenu items in bulk.
The menu needs to perform some action after click. In this case we want to split the terminal after click, we need to bind a `Command` for it. `Command` can also be [customized](./custom-command)by implementing `CommandContribution`, where we use the built-in `terminal.split` command.

> Note that the label of a registered menubar item does not take effect by default when the bound Command is also registered with `label` property specified at the time of registration

```typescript
registerMenus(registry: IMenuRegistry) {
  registry.registerMenubarItem(terminalMenuBarId, { label: '终端', order: 0 });

  registry.registerMenuItem(terminalMenuBarId, {
    command: 'terminal.split',
    group: '1_split',
  });
}
```

![Split Terminal](https://img.alicdn.com/imgextra/i1/O1CN018sreiD26Jd2EQc1RI_!!6000000007641-2-tps-2409-1510.png)

### Submenu grouping

When there are more menus registered, we may want to space out some submenus with similar actions from other menus, and can use the `group` property to group the submenus. Specifically, you can use the same `group` value for these `similar actions` menus. Here we use `registry.registerMenuItems` to register more submenus.

```typescript
registerMenus(registry: IMenuRegistry) {
  registry.registerMenubarItem(terminalMenuBarId, { label: '终端', order: 0 });

  registry.registerMenuItems(terminalMenuBarId, [
    {
      command: 'terminal.split',
      group: '1_split',
    },
    {
      command: 'terminal.remove',
      group: '2_clear',
    },
    {
      command: 'terminal.clear',
      group: '2_clear',
    },
    {
      command: 'terminal.search',
      group: '3_search',
    },
    {
      command: 'terminal.search.next',
      group: '3_search',
    },
  ]);
}
```

![More MenuItems](https://img.alicdn.com/imgextra/i1/O1CN0142Ey531JAY0aEEurA_!!6000000000988-2-tps-2409-1510.png)

### Register the secondary submenu

For the same type of menu, besides using `group` to group them, you can also register them as `secondary submenu`. When there are more submenus, using secondary menu can effectively improve the user experience. For example, we want to register both `search` and `search next match` as a secondary menu of `search`.

```typescript
const searchSubMenuId = 'terminal/search';

registerMenus(registry: IMenuRegistry) {
  registry.registerMenubarItem(terminalMenuBarId, { label: '终端', order: 0 });

  registry.registerMenuItems(terminalMenuBarId, [
    {
      command: 'terminal.split',
      group: '1_split',
    },
    {
      command: 'terminal.remove',
      group: '2_clear',
    },
    {
      command: 'terminal.clear',
      group: '2_clear',
    },
    {
      label: 'search',
      group: '3_search',
      submenu: searchSubMenuId,
    },
  ]);

  registry.registerMenuItems(searchSubMenuId, [
    {
      command: 'terminal.search',
    },
    {
      command: 'terminal.search.next',
    },
  ]);

}
```

![submenu](https://img.alicdn.com/imgextra/i3/O1CN01uVgEDb1CnICqwllsD_!!6000000000125-2-tps-2208-1527.png)