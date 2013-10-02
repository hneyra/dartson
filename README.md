# dartson

[![Build Status](https://drone.io/github.com/eredo/dartson/status.png)](https://drone.io/github.com/eredo/dartson/latest)

Dartson is a Dart library that can be used to convert Dart objects into a JSON string. It uses dart:mirrors reflection to rebuild the schema. It also works in dart2js when using the dartson builder.

 > NOTICE: dart2js support is currently in development. It will be done soon.

## Serializing objects in dart

```dart
library example;

import 'package:dartson/dartson.dart';

// If the builder should recognize this class add the following annotation
@DartsonEntity()
class EntityClass {
  String name;
  
  @DartsonProperty(name:"renamed")
  bool otherName;
  
  @DartsonProperty(ignore:true)
  String notVisible;
  
  // private members are never serialized
  String _private = "name";
  
  String get doGetter => _private;
}

void main() {
  EntityClass object = new EntityClass();
  object.name = "test";
  object.otherName = "blub";
  object.notVisible = "hallo";
  
  String jsonString = serialize(object);
  print(jsonString);
  // will return: '{"name":"test","renamed":"blub","doGetter":"name"}'
}
```

## Parsing json to dart object

```dart
library example;

import 'package:dartson/dartson.dart';

// If the builder should recognize this class add the following annotation
@DartsonEntity()
class EntityClass {
  String name;
  String _setted;
  
  @DartsonProperty(name:"renamed")
  bool otherName;
  
  @DartsonProperty(ignore:true)
  String notVisible;
  
  List<EntityClass> children;
  
  set setted(String s) => _setted = s;
  String get setted => _setted;
}

void main() {
  EntityClass object = parse('{"name":"test","renamed":"blub","notVisible":"it is", "setted": "awesome"}', EntityClass);
  
  print(object.name); // > test
  print(object.otherName); // > blub
  print(object.notVisible); // > it is
  print(object.setted); // > awesome
  
  // to parse a list of items use [parseList]
  List<EntityClass> list = parseList('[{"name":"test", "children": [{"name":"child1"},{"name":"child2"}]},{"name":"test2"}]', EntityClass);
  print(list.length); // > 2
  print(list[0].name); // > test
  print(list[0].children[0].name); // > child1
}
```

## TODO

- Better dart2js solution
- Handle recrusive errors
