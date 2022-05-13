---
title: "構造化束縛に対応した自作クラスをつくる"
emoji: "🪢"
type: "tech"
topics: [cpp]
published: true
---

構造化束縛(structured bindings)は配列やタプルの要素を分解して取り出すことができる機能です。

```cpp
#include <iostream>
#include <tuple>

inline std::tuple<int, float> f() {
   return {3, 0.14f};
}

int main() {
  // 構造化束縛
  auto [age, weight] = f();

  std::cout << age << std::endl;
  std::cout << weight << std::endl;
}
```

## やりたいこと

この自作クラスを構造化束縛に対応させます。

```cpp
struct Vec3f {
  union {
    float data[3];
    struct {
      float x, y, z;
    };
  };
};
```

x, y, z成分を分解して束縛できるようにします。

```cpp
Vec3f v;
auto [x, y, z] = v;
```

構造化束縛に対応するには下記の実装が必要です。

1. `std::tuple_size<>`を特殊化する
1. `std::tuple_elements<>`を特殊化する
1. `get()`を定義する

## `std::tuple_size<>`を特殊化する

`std::tuple_size<>`を特殊化して、束縛できる要素数を定義します。

```cpp
namespace std {

template <>
struct tuple_size<Vec3f> : integral_constant<size_t, 3> {};

}
```

## `std::tuple_elements<>`を特殊化する

`std::tuple_elements<>`を特殊化して、`I`番目の要素の型を定義します。

```cpp
namespace std {

template <size_t I>
struct tuple_element<I, Vec3f> {
  using type = float;
};

}
```

## `get()`を定義する

グローバル関数で定義する方法と、メンバ関数で定義する方法があります。
もちろん両方定義しても問題ありません。

グローバル関数の方は`std`名前空間でなくても大丈夫です。

```cpp
struct Vec3f {
  union {
    float data[3];
    struct {
      float x, y, z;
    };
  };

  // メンバ関数として定義する
  template <std::size_t I>
  float get() const {
    return data[I];
  }
  template <std::size_t I>
  float& get() {
    return data[I];
  }
};

// グローバル関数として定義する
template <std::size_t I>
inline float get(const Vec3f& v) {
  return v.data[I];
}
template <std::size_t I>
inline float& get(Vec3f& v) {
  return v.data[I];
}
```
## できた

参照で束縛すれば、値の変更もできます。

```cpp
Vec3f v;
{
  auto& [x, y, z] = v;
  x = 1;
  y = 2;
  z = 3;
}
{
  auto [x, y, z] = v;
  assert(x == 1);
  assert(y == 2);
  assert(z == 3);
}
```