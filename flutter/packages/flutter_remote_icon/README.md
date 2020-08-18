# flutter-remote-icon [![](https://img.shields.io/badge/pub-latest-brightgreen)](https://pub.dev/packages/flutter_remote_icon) [![](https://img.shields.io/badge/generator-latest-brightgreen)](https://pub.dev/packages/flutter_remote_icon_generator)

> What if you decided to load icon from server? But want to use local's font icons?
>
> "general remote icon loader for flutter." (this is part of bridged's [remote-ui](https://github.com/softmarshmallow/remote-ui) project)

![](./doc/remote-icon-demo.gif)



> Flutter remote icon enables you to load material icons (`Icons.~`) or your custom icon (`CustomIcons.~`) or remotely fetched content (svg) to your icon widget via `uri`, wich can be dynamically configured, remotely loaded.

## Installation

```yaml
dependencies:
  flutter_remote_icon: latest

dev_dependencies:
  flutter_remote_icon_generator: latest
  build_runner: latest
```
> for more information about  [flutter_remote_icon_generator](../flutter_remote_icon_generator), please refer [here](../flutter_remote_icon_generator)



## Usage

**supported usage**
* loading native icons (IconData)
* loading remote icon (svg, png) into `Icon()`
* loading packaged (local) asset as Icon
* loading packaged (local) font as Icon

**example**
```dart
Widget buildRemoteIcon(){
  // var remoteIconData = new RemoteIconData(Icons.add); // -> flutter native material icons
  // var remoteIconData = new RemoteIconData("material://Icons.add"); // -> native material icons remotely (dynamically)  
  // var remoteIconData = new RemoteIconData("https://example.com/svg.svg");  // -> loading remote svg
  // var remoteIconData = new RemoteIconData("assets/icons/add.png"); // -> loading local assets 
  // var remoteIconData = new RemoteIconData("custom-namespace://CustomIcons.icon_name"); // -> (requires pre-usage definition)
  var remoteIconData = new RemoteIconData();
  return RemoteIcon(icon: remoteIconData, color: Colors.black);
}
```

**supported icons**

0.  `Icons.add` (non dynamic usage)
1.  "local://assets/image.png"
2.  "http://example.com/image.png"
3.  "https://example.com/image.png"
4.  "material://icons.name"
5.  "custom-namespace://CustomIcons.icon_name"



## register custom font icon schema
```dart
void main() {
  IconProvider.register("namespace", {
    "namespace://CustomIcons.icon_name": CustomIcons.icon_name
  });
  runApp(ExampleApp());
}
```

## generate icons mapping for your own custom font IconData

in your `custom_icons.dart`
```dart
// your font based IconData class
part 'custom_icons.g.dart';

@IconMapper("namespace")
class CustomIcons{
  // ...
  static const IconData add_awesome; // ~
  static const IconData person_awesome; // ~
  // ...

  Map<String, IconData> get mapping{
    return _CustomIconsMapping;
  }

  static IconData fromUri(String uri) => _$CustomIconsFromUri(uri);
}
```

and run `flutter pub build_runner build`

will generate `custom_icons.g.dart`
```dart
part of 'custom_icons.dart';

const _CustomIconsMapping = {
  "namespace://CustomIcons.add_awesome": CustomIcons.add_awesome,
  "namespace://CustomIcons.person_awesome": CustomIcons.person_awesome,
};

IconData _$CustomIconsFromUri(String uri){
  return _CustomIconsMapping;[uri];
}
```

next, you can register the namespace and mappings easily
```dart
void main() {
  // register mapping
  IconProvider.register("namespace", CustomIcons.mapping);
  runApp(ExampleApp());
}

// and use it!
Widget buildDynamicIcon(){
  return RemoteIcon(icon: RemoteIconData("namespace://CustomIcons.person_awesome"));
}
```

> for using font as a icon please read [this blog](https://medium.com/flutterpub/how-to-use-custom-icons-in-flutter-834a079d977)



## What problem does it solve?

- https://github.com/flutter/flutter/issues/16189