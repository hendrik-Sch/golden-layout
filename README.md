# Golden Layout

[![NPM version](https://img.shields.io/npm/v/golden-layout)](https://www.npmjs.com/package/golden-layout) [![License](https://img.shields.io/github/license/golden-layout/golden-layout)](https://img.shields.io/github/license/golden-layout/golden-layout)

<!-- [![NPM version](https://badge.fury.io/js/golden-layout.svg)](http://badge.fury.io/js/golden-layout) [![Build Status](https://travis-ci.org/deepstreamIO/golden-layout.svg?branch=master)](https://travis-ci.org/deepstreamIO/golden-layout) -->

<!-- ![Screenshot](https://cloud.githubusercontent.com/assets/512416/4584449/e6c154a0-4ffa-11e4-81a8-a7e5f8689dc5.PNG) -->

## Version 2

Version 2 of Golden Layout is now available in the main repository and will be published to NPM shortly.

Moving to version 2 is a large change to Golden Layout.  These changes can be summarised as:

1. The code has been ported to TypeScript
1. The primary focus of maintenance will be on reliability.

### Version 2 Status

Version 2.0 is now ready for production. However before migrating from Version it is **important** to review the following:

#### Dropped Features

As part of the port, the code base was significantly refactored.  A number of features have been dropped from the version 1.0 as their implementation was not robust enough to meet the reliability requirements of version 2.  The dropped features are:

* **React Support** - The [FlexLayout](https://github.com/caplin/FlexLayout) library has been designed for React components.  We recommend developers using React to use this library instead of Golden Layout.
* **Nested Stacks** - While it was possible to create layouts with Nested Stacks in version 1, the implementation was incomplete.  Due to the large amount of work that would have been necessary to fix the implementation, it was decided instead to drop this feature. Version 2 explicitly does not allow nested stacks.
* [**Internal and Public API**](#public-and-internal-apis) - All classes, interfaces, functions and properties are marked as either `internal` or `public`. Only `public` APIs are generally available to applications.
* **Legacy Browsers** - The library will now only target modern browsers (see package.json for browserlist configuration)
* **Native JavaScript modules only** - No support is available for CommonJS or other JavaScript module formats.
* **No JQuery** - JQuery is no longer used in Golden Layout (many would consider this as an added feature)

#### Features implemented but not ready for production

Some features have been ported to TypeScript but are not yet ready for production.  These features are:

* **Popouts** - The coding for this has been completed however testing still needs to be carried out.
* **Touch Support** - Improvements are required in accessing browser Touch/Drag APIs.  Also some conceptual aspects of the implementation need to be improved.  These will be carried out in a future release.
* **Some API functions** - While most API functions have been ported, not all have been tested.  The APIs used in the Test Application (both demo app and Angular example) have been tested and are ready for production.  Other API functions should work but please take this warning into account.

### Migration

The Version 1 to Version 2 migration guide is available [here](#migration).

## Features

* ~~Full touch support~~
* ~~Native popup windows~~
* Completely themeable
* Comprehensive API
* Powerful persistence
* Works in modern browsers (Firefox, Chrome)
* Reponsive design

## Installation / Usage

### Library
Golden Layout is shipped via NPM. Use the following commands to install it into an application package:\
`npm i golden-layout`

### Source
The source can be installed by cloning the repository at:\
`https://github.com/golden-layout/golden-layout`

To build the distribution locally, open a shell at the golden-layout directory/folder and run the following commands:
1. `npm install` or `npm ci` (recommended) to install required dependencies
1. `npm run build` to generate the distribution (`dist` subfolder). This script will:
    * delete the existing `lib` and `dist` folders
    * compile the TypeScript code
    * generate the rolled up TypeScript definition files (`index.d.ts` and `golden-layout-untrimmed.d.ts`)
    * generate source map
    * copy the style files to the `dist` folder

Note that the`lib` subfolder only holds the TypeScript declaration files generated by the compiler. Generally this subfolder can be ignored. It is used during the build process to generate the rolled up TypeScript definition files.

### Build and run demo/test app

After installing the source and building the distribution, you can build and start the `demo` app to view the library in action. Use the following commands:
* `npm run apitest:build` to just build it
* `npm run apitest:serve` to both build and start the development server.\
You can then view it in your browser using the following link:\
http://localhost:3000/

### Debugging Golden Layout library

The `demo` can be used to debug the Golden Layout library.  Its `webpack` configuration will import the Golden Layout library source map, allowing debuggers to step through the library source code and place break points.

If you wish to test the library with other applications, you can link to the Golden Layout repository without having to install it into the application from NPM.  This is done with the `npm link` command.  Use the following steps:
1. Make sure that the `golden-layout` package is **not** included as a dependency in the application's package
1. Run the `npm link` from a shell in the golden-layout source repository top level folder.
1. Run `npm link golden-layout` from a shell in your application's top level folder.

Your application will then use the distribution in the Golden Layout repository `dist` subfolder. If you wish to make changes to the Golden Layout library, you will need to run the `build:api` to regenerate the `dist` folder.

### Angular Example App
A sample Angular application using Golden Layout is available.  The source can be installed by cloning the repository:\
`https://github.com/golden-layout/golden-layout-ng-app`

After installing the source, the app can be built and started with the standard build and start scripts.

## Migration
Version 2 has been re-written in TypeScript. A general code cleanup has been carried out as part of this re-write.  

Also, some changes have been made to the GoldenLayout API.  Where possible, backwards compatibility has been retained,however functions and properties kept for backwards compatibility have been marked as deprecated. It is strongly recommend applications be migrated to the new API.

The API changes include 2 new events to support creation of components: `getComponentEvent` and `releaseComponentEvent`. With these events, it is far easier to integrate GoldenLayout into frameworks.  This example application demonstrates how to integrate GoldenLayout into Angular:
  [https://github.com/golden-layout/golden-layout-ng-app](https://github.com/golden-layout/golden-layout-ng-app)

### Config
Configs are now strongly typed. In addition, GoldenLayout now has "Configs" and "Resolved Configs"
1. Configs\
Application developers will mainly work with "Configs".  A "Config" supports optional properties.  If a property is not specified, a default will be used.  In addition, "Config" also will handle backwards compatibility.  It will migrate deprecated properties to their new values.\
Config parameters in GoldenLayout API methods will be of type "Config".  The one exception is `LayoutConfig.saveLayout()` which returns a "Resolved Config".
1. Resolved Configs\
Golden-Layout internally uses "Resolved Config"s. Whenever an API function is passed a "Config", GoldenLayout will resolve it to its corresponding "Resolved Config".  This resolving process will set default values where an optional value has not been specified. It will also handle backwards compatibility.  This allows the GoldenLayout library to always work with fully configured Configs.

For persistence of configs, always save the "Resolved Config" returned by `LayoutManager.saveLayout()`. When reloading a saved Layout, first convert the saved "Resolved Config" to a "Config" by calling `LayoutConfig.fromResolved()`.

Both "Resolved Config" and "Config" have 2 types of interface hierarchies:
1. `ItemConfig`\
This specifies the config for a content item.
1. `LayoutConfig` (previously the `Config` interface)\
This specifies the config for a layout.

The (optional) `ItemConfig.id` property now has type `string` (instead of its previous `string | string[]` type).  For backwards compatibility, when `ItemConfig.id` is resolved, it will still accept an `id` with of type string array. This will allow handling of legacy saved configs in which `id` contains an array of strings (including possibly the legacy maximise indicator). When such an `id` is resolved, the array is first checked for the legacy maximise indicator and then the first element becomes the `id` string value.  The remaining elements are discarded.

The `ComponentItemConfig.componentName` property has now been replaced by property `ComponentItemConfig.componentType`.  `componentType` is of type `JsonValue`.  While a component type can now be specified by values that can be serialised by JSON, `componentType` must be of type `string` if it is registered with one of the following functions:
1. `LayoutManager.registerComponent()` (deprecated)
1. `LayoutManager.registerComponentConstructor()`
1. `LayoutManager.registerComponentFactoryFunction()`

A `LayoutConfig` has a `root` property which specifies the ItemConfig of root content item of the layout.  `root` is not optional and must always be specified.

The `LayoutConfig` `selectionEnabled` property has been removed. Clicking of Stack Headers can now be handled with the new `stackHeaderClick` event (which is always enabled).

`ResolvedLayoutConfig` now has functions to minify and unminify configurations:
1. `minifyConfig()` Replaces `LayoutManager.minifyConfig()`
1. `unminifyConfig()` Replaces `LayoutManager.unminifyConfig()`

For examples of how to create LayoutConfigs, please refer to the demo program in the repository.

Many of the Config properties have been deprecated as they overlapped or were moved to more appropriate locations. Please refer to the `config.ts` source file for more information about these deprecations.

### GoldenLayout class

GoldenLayout is now a distinct class which is a descendant of the LayoutManager class.  Your application should always create an instance of this class.

The GoldenLayout constructor takes one optional parameter: the HTML element which contains the GoldenLayout instance.  If this is not specified, GoldenLayout will be placed under `body`.

Note that the initial Layout is no longer specified in this constructor. Instead it is loaded with `LayoutManage.loadLayout()` (see below).

### LayoutManager changes
1. Do not construct an instance of LayoutManager.  Construct an instance of GoldenLayout (see above).
1. `registerComponentConstructor()` (new function)\
Same as previous `registerComponent()` however only used when registering a component constructor.
1. `registerComponentFactoryFunction` (new function)\
Same as previous `registerComponent()` however only used when registering a call back function (closure) for creating components.
1. Do not use `registerComponent()`. Use the new `registerComponentConstructor()` or `registerComponentFactoryFunction()` instead.
1. `getComponentEvent` (new event)\
Generate a component needed by GoldenLayout. The parameters specify its container and `ItemConfig`. Use this event instead of `registerComponentConstructor()` or `registerComponentFactoryFunction` if you want to control the disposal of the component.
1. `releaseComponentEvent` (new event)\
Use in conjunction with `getComponentEvent` to release/dispose any component created for GoldenLayout
1. Do not call `init()`. Call `LayoutManager.loadLayout()` instead.
1. `loadLayout()` (new function)\
Will load the new layout specified in its `LayoutConfig` parameter.  This can also be subsequently called whenever the GoldenLayout layout is to be replaced.
1. `saveLayout()` (new function)\
Saves the current layout as a `LayoutConfig`. Replaces the existing `toConfig()` function.
1. Do not uses `minifyConfig()` of `unminifyConfig()` functions.  Use the respective functions in `ResolvedLayoutConfig`.
1. Do not call `toConfig()`. Call `LayoutManager.saveLayout()` instead.
1. `setSize()` (new function)\
Sets the size of the GoldenLayout instance in pixels. Replaces the existing `updateSize()` function.
1. Do not use `updateSize()`.  Use the new `LayoutManager.setSize()` instead.
1. `rootItem` (new property)
Specifies the root content item of the layout (not the Ground content item).
1. Do not use `root`. This has been replaced with the internal property `groundItem`. You probably want to use the new `rootItem` instead.
1. `setFocusedComponentItem()` will either focus the specified component item or, (if item is undefined), remove existing focus from a component item. `focus` or `blur` events will be emitted as appropriate unless the `suppressEvent` parameter is set to true.
1. `focusComponent()` an alternative to `setFocusedComponentItem()` which only focuses the specified component.
1. `clearComponentFocus()` which removes any existing component item focus.

### Content Items
1. `AbstractContentItem` has been renamed to `ContentItem`
1. `config` property has been removed. Use the toConfig() method instead (as recommended in the original GoldenLayout documentation).
1. Some of the previous `config` properties such as `id` and `type` are now available as properties of `ContentItem` or its descendants (where appropriate).
1. `id` now has type `string`.  (It used to be `string | string[]`.)
1. `ItemContainer` has been renamed to `ComponentContainer`
1. `Component` has been renamed to `ComponentItem`.  "Component" now refers to the external component hosted inside GoldenLayout
1. `Root` has been renamed to `GroundItem` and has been marked as internal only. Applications should never access GroundItem.  Note that the layout's root ContentItem is GroundItem's only child.  You can access this root ContentItem with `LayoutManager.rootItem`.
1. `Stack.getActiveContentItem()` and `Stack.setActiveContentItem()` have been renamed to respective `Stpack.getActiveComponentItem()` and `Stack.setActiveComponentItem()`
1. `ContentItem.select()` and `ContentItem.deselect()` have been removed.  Use the new `ComponentItem.focus()` and `ComponentItem.blur()` instead.
1. `ComponentItem.focus()` (new function) will focus the specified ComponentItem. It will also remove focus from another component item which previously had focus.  Only one component item can have focus at any time. If layout focus has changed, a `focus` event will be emitted (unless suppressEvent parameter is set to true).
1. `ComponentItem.blur()` (new function) will remove focus from the specified ComponentItem. After this is called, no component item in the layout will have focus.  If the component lost focus, a `blur` event will be emitted (unless suppressEvent parameter is set to true).

### ComponentContainer
1. `element` (new property - replaces `getElement()`)\
Returns HTMLElement which hosts component
1. Do not use `getElement()`. Use the new `element` property instead
1. `initialState` (new getter)\
Gets the componentState of the `ComponentItemConfig` used to create the contained component.
1. `stateRequestEvent` (new event)\
If set, `stateRequestEvent` is fired whenever GoldenLayout wants the latest state for a component. Calling `LayoutManager.saveLayout()` will cause this event to be fired (if it is defined).  If it is not defined, then the initial state in the ItemConfig or the latest state set in `setState()` will be saved.
1. `beforeComponentRelease` (new EventEmitter event)\
`beforeComponentRelease` is emitted on the container before a component is released.  Components can use this event to dispose of resources.
1. Do not use `getState()` unless you are using the deprecated `setState()`. Use `ComponentContainer.initialState` getter if you have migrated to the new `ComponentContainer.stateRequestEvent`.
1. `setState()` has been marked as deprecated. If possible, use the new `stateRequestEvent` event instead.
1. `replaceComponent()` allows you to replace a component in a container without otherwise affecting the layout.

### Header and Tab
Several properties and functions have been renamed in `header.ts` and `tab.ts`. Please search for "@deprecated" in these files for these changes.

### Events
1. All DOM events are now propagated so that they can be handled by parents or globally.
1. preventDefault() is only called by MouseMove listener used in DragListener. All other event listeners are added with passive: true.
1. Bubbling Events are now emitted with the parameter EventEmitter.BubblingEvent (or descendant)
1. New EventEmitter events:
    * beforeComponentRelease
    * stackHeaderClick - Bubbling event. Fired when stack header is clicked - but not tab.
    * stackHeaderTouchStart - Bubbling event. Fired when stack header is touched - but not tab.
    * focus - Bubbling event. Fired when a component gets focus.
    * blur - Bubbling event. Fired when a component loses focus.

### Other
1. `undefined` is used instead of `null` for new properties, events etc.  Some internals have also been switched to use `undefined` instead of `null`. Existing properties using `null` mostly have been left as is however it is possible that some of these internal changes have affected external properties/events/methods.

### Deprecations
For most changes, the existing functions and properties have been left in place but marked as deprecated.  It is strongly recommended that applications be reworked not to use these deprecations. Bugs associated with deprecations will be given low priority (or not fixed at all).  Also, deprecated aliases, methods and properties may be removed in future releases.

### Public and Internal APIs
All API elements (classes, interfaces, functions etc) have been labelled as either `public` or `internal`.  Applications should only use `public` API elements.  Internal API elements are subject to change and no consideration will be given to backwards compatibility when these are changed.

The library distribution includes 2 TypeScript declaration (typing) files:
1. `index.d.ts` which contains only public API elements.  Applications should use this declaration file to access the library.
1. `golden-layout-untrimmed.d.ts` which contains all (public and internal) API elements.  Use this declaration file if you wish to access any API element in the library however please take the above warning into account.

Note that the allocation of API elements to either public or internal has not been finalised.  However any element used in either the demo application or the example Angular application will remain labelled as public.
