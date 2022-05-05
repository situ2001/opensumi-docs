---
id: decoration
title: Decoration Module
slug: decoration
---

The FileDecoration module is used to register/manage/distribute filene-related 'Decoration' services. 

## Basic Concepts

Decoration —— a means to to "decorate" the file tree style, common scenarios including Git project special decoration
Git projects are decorated with decorations. Git projects are decorated with decorations.  

![git-sample](https://img.alicdn.com/imgextra/i4/O1CN0102WFi9267ik1JKMeC_!!6000000007615-2-tps-1038-824.png)

## User Guide

### Register Decorations

#### `registerDecorationsProvider`

```ts
  registerDecorationsProvider(provider: IDecorationsProvider): IDisposable;
```

The underlying data structure for registering a DecorationsProvider is as follows.

```ts
interface IDecorationData {
  /**
   * 权重
   */
  readonly weight?: number;
  /**
   * Decoration 颜色
   */
  readonly color?: ColorIdentifier;
  /**
   * Decoration 字符
   */
  readonly letter?: string;
  /**
   * Decoration tooltip
   */
  readonly tooltip?: string;
  /**
   * Decoration 是否冒泡，类似文件的 Decoration 是否传给文件夹
   */
  readonly bubble?: boolean;
}
```

##### Use Cases

```ts
class SampleDecorationsProvider implements IDecorationsProvider {
  readonly label = 'sample';

  readonly onDidChangeEmitter: Emitter<Uri[]> = new Emitter();

  get onDidChange() {
    return this.onDidChangeEmitter.event;
  }

  provideDecorations(resource: Uri): IDecorationData | undefined {
    if (file.scheme !== 'file') {
      return undefined;
    }

    return {
      letter: '😸',
      color: 'cat.smileForeground',
      tooltip: localize('cat.smile'),
      weight: -1,
      bubble: false
    } as IDecorationData;
  }
}
```

### Minotor Decorations Changes

#### `onDidChangeDecorations`

```ts
  readonly onDidChangeDecorations: Event<IResourceDecorationChangeEvent>;
```

Event distribution is performed for the change event of file name Decoration

##### Use Cases

```ts
this.decorationsService.onDidChangeDecorations(() => {
  // some listener
});
```

### Get the File Decorator

#### `getDecoration`

```ts
getDecoration(uri: Uri, includeChildren: boolean, overwrite?: IDecorationData): IDecoration | undefined;
```

Get the result of the current file's Decoration by means of getting the uri, or return undefined if it doesn't get it.

##### Use Cases

```ts
this.decorationsService.getDecoration(uri, true);
```