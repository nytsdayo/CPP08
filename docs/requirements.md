# C++ Module 08: 課題要件整理

## モジュール情報

| 項目 | 内容 |
|------|------|
| モジュール名 | C++ - Module 08 |
| テーマ | Templated containers, iterators, algorithms |

---

## 全体ルール

### 命名規則

| 対象 | 規則 |
|------|------|
| ディレクトリ | `ex00`, `ex01`, `ex02` |
| クラス名 | UpperCamelCase |
| ファイル名 | クラス名に準拠（`ClassName.hpp`, `ClassName.cpp`, `ClassName.tpp`） |
| 出力 | 特に指定がない限り、改行文字で終端し標準出力に表示 |

### 使用制限

| 許可 | 禁止 |
|------|------|
| C++標準ライブラリ（ほぼ全て） | C++11以降の機能 |
| STLコンテナ（Module 08で解禁） | Boostライブラリ |
| STLアルゴリズム（Module 08で解禁） | `*printf()`, `*alloc()`, `free()` |
| | `using namespace <ns_name>`（明示的許可がない限り） |
| | `friend`キーワード（明示的許可がない限り） |

### 設計要件

| 要件 | 詳細 |
|------|------|
| メモリ管理 | `new`で確保したメモリのリーク禁止 |
| Orthodox Canonical Form | Module 02〜09のクラスに適用（明示的除外がない限り） |
| ヘッダファイル | 関数テンプレート以外の実装をヘッダに書くと0点 |
| インクルードガード | 必須（二重インクルード防止） |
| ヘッダの独立性 | 各ヘッダが必要な依存関係をすべて含むこと |

---

## モジュール固有ルール

このモジュールの核心的なルール：

**STLの使用が必須。** 演習はSTLなしでも解けるが、STLを使うことがこのモジュールの目的。コンテナ（`vector`, `list`, `map`等）とアルゴリズム（`<algorithm>`ヘッダ）を適切な場面で積極的に使わなければ、コードが動作しても低い評価になる。

テンプレートの定義はヘッダファイルに書くか、宣言をヘッダに・実装を`.tpp`ファイルに分離する。ヘッダファイルは必須、`.tpp`ファイルは任意。

---

## Exercise 00: Easy find

### 基本情報

| 項目 | 内容 |
|------|------|
| ディレクトリ | `ex00/` |
| 提出ファイル | `Makefile`, `main.cpp`, `easyfind.{h, hpp}` |
| 任意ファイル | `easyfind.tpp` |
| 禁止事項 | なし |

### 要件

1. **関数テンプレート`easyfind`を実装する**
   - テンプレートパラメータ: 型`T`
   - 引数: 第1引数は型`T`、第2引数は`int`
   - `T`は整数のコンテナであると仮定する

2. **機能**: 第1引数（コンテナ）から第2引数（整数値）の最初の出現を見つける

3. **エラー処理**: 見つからない場合、例外を投げるかエラー値を返す（選択可能）
   - 標準コンテナの振る舞いを参考にすること

4. **連想コンテナは扱わなくてよい**（`map`, `set`等は対象外）

5. **自分でテストを実装して提出する**

### 暗黙の要件

- `T`はシーケンスコンテナ（`vector`, `list`, `deque`等）を想定
- STLアルゴリズム（`std::find`）の使用が期待されている（モジュール固有ルールより）

---

## Exercise 01: Span

### 基本情報

| 項目 | 内容 |
|------|------|
| ディレクトリ | `ex01/` |
| 提出ファイル | `Makefile`, `main.cpp`, `Span.{h, hpp}`, `Span.cpp` |
| 禁止事項 | なし |

### 要件

1. **`Span`クラスを実装する**
   - 最大`N`個の整数を格納できる
   - `N`は`unsigned int`型
   - コンストラクタの唯一の引数として`N`を受け取る

2. **メンバ関数: `addNumber()`**
   - 1つの数値をSpanに追加する
   - 既に`N`個の要素が格納されている場合、例外を投げる

3. **メンバ関数: `shortestSpan()`**
   - 格納された全数値間の最短距離を返す
   - 数値が0個または1個の場合、例外を投げる

4. **メンバ関数: `longestSpan()`**
   - 格納された全数値間の最長距離を返す
   - 数値が0個または1個の場合、例外を投げる

5. **イテレータ範囲による一括追加**
   - イテレータ範囲を受け取って複数の数値を一度に追加するメンバ関数を実装する
   - ヒント: STLコンテナのメンバ関数にはイテレータ範囲で要素列を追加するものがある

6. **テスト要件**
   - 最低10,000個の数値でテストすること（それ以上が望ましい）

### 期待出力（課題提示のテストコード）

```cpp
int main()
{
    Span sp = Span(5);

    sp.addNumber(6);
    sp.addNumber(3);
    sp.addNumber(17);
    sp.addNumber(9);
    sp.addNumber(11);

    std::cout << sp.shortestSpan() << std::endl;
    std::cout << sp.longestSpan() << std::endl;
    return 0;
}
```

出力:
```
2
14
```

解説: `{3, 6, 9, 11, 17}` において、最短距離は `|9-11|=2`、最長距離は `|17-3|=14`。

### 暗黙の要件

- Orthodox Canonical Formに従う（コピーコンストラクタ、代入演算子等）
- 一括追加関数はテンプレートメンバ関数として実装するのが自然（任意のイテレータ型を受け取るため）
- 容量超過チェックは一括追加時にも必要

---

## Exercise 02: Mutated abomination

### 基本情報

| 項目 | 内容 |
|------|------|
| ディレクトリ | `ex02/` |
| 提出ファイル | `Makefile`, `main.cpp`, `MutantStack.{h, hpp}` |
| 任意ファイル | `MutantStack.tpp` |
| 禁止事項 | なし |

### 要件

1. **`MutantStack`クラスを実装する**
   - `std::stack`をベースに実装する（"implemented in terms of"）
   - `std::stack`のすべてのメンバ関数を提供する
   - 追加機能としてイテレータを提供する

2. **イテレータの提供**
   - `MutantStack<T>::iterator` 型が使えること
   - `begin()`, `end()` メンバ関数を提供する

3. **`std::list`との互換性テスト**
   - 提示テストコードの`MutantStack`を`std::list`に置き換えた場合、同じ出力になること
   - メンバ関数名の対応（`push()` → `push_back()`等）を適宜調整する

4. **自分でテストを実装して提出する**

### 期待される動作（課題提示のテストコード）

```cpp
int main()
{
    MutantStack<int> mstack;

    mstack.push(5);
    mstack.push(17);
    std::cout << mstack.top() << std::endl;   // 17

    mstack.pop();
    std::cout << mstack.size() << std::endl;   // 1

    mstack.push(3);
    mstack.push(5);
    mstack.push(737);
    //[...]
    mstack.push(0);

    MutantStack<int>::iterator it = mstack.begin();
    MutantStack<int>::iterator ite = mstack.end();

    ++it;
    --it;
    while (it != ite)
    {
        std::cout << *it << std::endl;
        ++it;
    }
    std::stack<int> s(mstack);  // std::stackへのコピーが可能
    return 0;
}
```

### 暗黙の要件

- `std::stack`は内部にコンテナ（デフォルトは`std::deque`）を持つコンテナアダプタ
- `std::stack`の`protected`メンバ`c`（内部コンテナ）へのアクセスが鍵
- テンプレートクラスとして実装する（`MutantStack<T>`）
- `const_iterator`, `reverse_iterator`, `const_reverse_iterator` の提供も検討に値する

---

## モジュール全体の学習構造

```
ex00: Easy find
  → STLアルゴリズムを関数テンプレートから使う
  → 「コンテナに対する汎用操作」の入口

ex01: Span
  → クラス内部でSTLコンテナを利用する
  → イテレータ範囲という概念の実践
  → STLアルゴリズム（sort, min_element, max_element等）の活用

ex02: MutantStack
  → コンテナアダプタの内部構造を理解する
  → 継承によるSTL拡張
  → イテレータを自分で公開する設計
```

Module 07（テンプレート基礎）で学んだ関数テンプレート・クラステンプレートの知識が、STLという「テンプレートの実用例」の中でどのように活用されているかを体験するモジュール。
