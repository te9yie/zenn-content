---
title: "型を定数として扱う"
emoji: "🤷"
type: "tech"
topics: [cpp]
published: true
---

## 静的ローカル変数のアドレスを利用する

```cpp
#include <cstdint>

template <typename T>
struct type2int {
  static std::intptr_t value() {
    static int i = 0;
    return reinterpret_cast<std::intptr_t>(&i);
  }
};

// assert(type2int<int>::value() != type2int<float>::value());
// assert(type2int<float>::value() == type2int<float>::value());
```

型ごとにテンプレートのインスタンス化が行われるので、型ごとに異なる値が取得できる。

## インクリメンタルな値にする

```cpp
namespace detail {

struct type2int_ {
  static int next() {
    static int count = 0;
    return ++count;
  }
};

}  // namespace detail

template <typename T>
struct type2int {
  static int value() {
    static int i = detail::type2int_::next();
    return i;
  }
};

// assert(type2int<int>::value() != type2int<float>::value());
// assert(type2int<float>::value() == type2int<float>::value());
```

こちらの場合、定数の型をある程度自由に変えることができる。
オーバーフローには気をつけて。