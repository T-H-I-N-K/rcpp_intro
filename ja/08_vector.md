# ベクトル

* [ベクトルオブジェクトの作成](#ベクトルオブジェクトの作成)
* [ベクトル要素へのアクセス](#ベクトル要素へのアクセス)
* [メンバ関数](#メンバ関数)
* [静的メンバ関数](静的メンバ関数)

##ベクトルオブジェクトの作成


```cpp
// v <- rep(0, 3) と同等
NumericVector v (3);

// v <- rep(1, 3) と同等
NumericVector v (3,1);

// v <- c(1,2,3) と同等
NumericVector v = NumericVector::create(1,2,3);

// v <- c(1,2,3) と同等、C++11 の初期化リストで作成
NumericVector v = {1,2,3};

// v <- c(x=1, y=2, z=3) と同様 名前付きベクトル
NumericVector v =
  NumericVector::create(Named("x",1), Named("y")=2 , _["z"]=3);
```

##ベクトル要素へのアクセス

Rと同様に、要素番号（整数・実数ベクトル）、要素名（文字列ベクトル）、論理ベクトルを用いて、ベクターの要素にアクセスして、値の取得・代入を行うことができます。

要素へのアクセスには `[]` または `()` を用います。`[]` を用いた場合はベクトルの範囲外へのアクセスを無視しますが、`()` を用いた場合は実行時にエラーとなります。

また、要素番号を用いて Rcpp でベクトルの要素にアクセスする際に気をつけるべき重要な点として、R ではベクトルの最初の要素は 1 番であるのに対して、Rcpp では 0 番から始まるということがあります。間違いやすいので注意してください。

**【重要】： Rcpp は C++ のスタイルに従い、ベクトルや行列の要素番号は ０ から始まります。**

```cpp
// [[Rcpp::export]]
void rcpp_vector_access(){

  //ベクトルの作成
  NumericVector v  {10,20,30,40,50};

  //要素名を設定します
  v.names() = CharacterVector({"A","B","C","D","E"});

  //ベクトルの要素にアクセスするためのベクトルを用意します
  NumericVector   numeric = {1,3};
  IntegerVector   integer = {1,3};
  CharacterVector character = {"B","D"};
  LogicalVector   logical = {false, true, false, true, false};

  //ベクトル要素の値を取得します
  double x1 = v[0];
  double x2 = v["A"];
  NumericVector res1 = v[numeric];
  NumericVector res2 = v[integer];
  NumericVector res3 = v[character];
  NumericVector res4 = v[logical];

  //ベクトル要素へ値を代入します
  v[0]   = 100;
  v["A"] = 100;
  NumericVector v2 {100,200};
  v[numeric]   = v2;
  v[integer]   = v2;
  v[character] = v2;
  v[logical]   = v2;
}
```






## メンバ関数

メンバ関数（メソッドとも呼ばれる）とは、個々のオブジェクトと結びついた関数です。呼び出し方が通常の関数とは少し異なっており、 `v.length()` のような形式で呼び出す。

```cpp
NumericVector v = {1,2,3,4,5};
int n = v.length(); // 5
```

### length() size()

要素数を返します。

### names()

要素名を文字列ベクトルで返します。

### offset(name) findName(name)

文字列 `name` で指定した要素名の要素の要素番号を返します。

### fill(x)

このベクトルの全ての要素をスカラー値 `x` で埋めます。

### sort()

このベクトルを昇順でソートしたベクトルを返します。

### assign( first_it, last_it)

イテレータ `first_it`、`last_it` で指定された範囲の値を、このベクトルに代入します。

### push_back(x)

このベクトルの末尾にスカラー値 `x` を追加します。

### push_back( x, name )

このベクトルの末尾にスカラー値 `x` を追加します。追加した要素の名前を文字列 `name` で指定します。

### push_front(x)

このベクトルの先頭にスカラー値 `x` を追加します。

### push_front( x, name )

このベクトルの先頭にスカラー値 `x` を追加します。追加した要素の名前を文字列 `name` で指定します。

### begin()

このベクトルの先頭を指し示すイテレータを返します。

### end()

このベクトルの末尾（最後の要素の１個後ろ）を指し示すイテレータを返します。

### insert( i, x )

このベクトルの要素番号 i の位置の前にスカラー値 x を追加し、追加された要素へのイテレータを返します。

### insert( it, x )

このベクトルのイテレータ it で指し示す位置の直前にスカラー値 x を追加し、追加された要素へのイテレータを返します。

### erase(i)

このベクトルの `i` 番目の要素を削除し、削除した直後の要素へのイテレータを返します。

### erase(it)

イテレータ it で指定された要素を削除し、削除した直後の要素へのイテレータを返します。

### erase( first_i, first_i )

first_i 番目 から last_i 番目 までの要素を削除し、削除した直後の要素へのイテレータを返します。

### erase( first_it, last_it )

イテレータ first_it と last_it - 1 で指定される範囲の要素を削除し、削除した直後の要素へのイテレータを返します。

### containsElementNamed(name)

このベクトルが文字列 name で指定された名前の要素を持っている場合には true を返します。


## 静的メンバ関数

静的メンバ関数は `NumericVector::create()` のような形式で呼び出します。

### get_na()

このベクトルの型に対応した `NA` 値を返します。

### is_na(x)

ベクトルの要素 x が `NA` である場合には `true` を返します。

### create( x1, x2, ...)

スカラー値 x1, x2, ...  を要素とするベクトルを作成します。指定できる引数の数は20個まで対応しています。

### import( first_it , last_it )

イテレーター `first_it`, `last_it` で指定された範囲の値で満たされたベクトルを作成します。

### import_transform( first_it, last_it, func)

イテレーター `first_it`, `last_it` で指定された範囲の値を、`func` で指定した関数で変換した値で満たされたベクトルを作成します。
