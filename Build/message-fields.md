# Defining your `message` with `fields`

In your `.proto` file you define all of the data your distributed system will be sending from client `Stub`s and returning from `Server`s. Message `fields` help you define the strict typing for all of those data.

```protobuf
syntax proto3

package messageFields

service HeavyLogic {
  rpc FireHose ( ManyFields ) returns ( ManyFields ) {}
}

message ManyFields {
  double enormousNum = 1;
  float decimalNum = 2;
  int32 num = 3;
  uint32 varNum = 4;
  sint32 negNum = 5;
  fixed32 xlargeNum = 6;
  sfixed32 largeNum = 7;
  int64 long = 8;
  uint64 varLong = 9;
  sint64 negLong = 10;
  fixed64 xlargeLong = 11;
  sfixed64 largeLong = 12;
  bool isTrue = 13;
  string comment = 14;
  bytes chunk = 15;
}
```
> Note: every instance of this message whether `Unary` or a `stream` will now need the 15 properties as they are written here inside an object.

### example 
```javascript
const exampleManyFieldsMessage = {
  enormousNum: 100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000,
  decimalNum: 9.999999999999997,
  num: 32,
  varNum: 32,
  negNum: -32,
  xlargeNum: 2147483646,
  largeNum: 268435456,
  long: 3000111222,
  varLong: 3000111222,
  negLong: -3000111222,
  xlargeLong: 9223372036854775806,
  largeLong: 72057594000000000,
  bool: true,
  string: 'hello world',
  bytes: 'AA 73 A8 72',
}
```