# LogicalVector と 論理演算

## LogicalVector の正体

C++ の論理値型は `bool` であるので、`LogicalVector` の要素の型も `bool` であると思うかもしれないが、実際には `int` 型です。(R でも論理ベクトルの実体は整数ベクトルである) なぜこのようになっているかというと `bool` で表現できるのは `true` と `false` の２つだけですが、R の論理ベクトルの要素の値には `TRUE`, `FALSE`,`NA` の３つがあり得るためです。

Rcpp では `TRUE` は 1、`FALSE` は 0、`NA` は `NA_LOGICAL`（int の最小値）で表現されています。

|logical|Rcppの記号|int|bool|
|:---:|:---:|:---:|:---:|
|TRUE|TRUE|1 (0以外の値)|true|
|FALSE|FALSE|0|false|
|NA|NA_LOGICAL|int の最小値|true|

## LogicalVector の要素の評価

`LogicalVector` の要素の値を、そのまま if 文の条件式として使用してはいけません。なぜなら、C++ の  if 文の条件式は式の値を`bool` 型として評価するのですが、 `bool` 型は 0 以外の値を全て `true` と評価するので、`LogicalVector` の `NA`（`NA_LOGICAL`） は `true` と評価されてしまうためです。

 `LogicalVector` の要素の値を if 文で評価する方法については、次のコード例を参考にしてください。

```
// [[Rcpp::export]]
LogicalVector rcpp_logical(){
  
  // NA を含む整数ベクトルを作成します
  IntegerVector x = {1,2,3,4,NA_INTEGER};
  
  // 比較演算の結果は LogicalVector となります
  LogicalVector v = (x >= 3); 
  
  // LogicalVector の要素を直接 if 文の条件式で使うと
  // NA_LOGICAL は TRUE と評価されてしまいます
  for(int i=0; i<v.size();++i) {
    if(v[i]) Rprintf("v[%i] is evaluated as true.\n",i);
    else Rprintf("v[%i] is evaluated as false.\n",i);
  } 
  
  // LogicalVector の要素の評価します
  for(int i=0; i<v.size();++i) {
    if(v[i]==TRUE) Rprintf("v[%i] is TRUE.\n",i);
    else if (v[i]==FALSE) Rprintf("v[%i] is FALSE.\n",i);
    else if (v[i]==NA_LOGICAL) Rprintf("v[%i] is NA.\n",i);
    else Rcout << "v[" << i << "] is not 1\n";
  }
  
  // TRUE FALSE NA_LOGICAL の値を表示します
  Rcout << "TRUE " << TRUE << "\n";
  Rcout << "FALSE " << FALSE << "\n";
  Rcout << "NA_LOGICAL " << NA_LOGICAL << "\n";
  
  return v;
}
```

実行結果

```
> rcpp_logical()
v[0] is evaluated as false.
v[1] is evaluated as false.
v[2] is evaluated as true.
v[3] is evaluated as true.
v[4] is evaluated as true.
v[0] is FALSE.
v[1] is FALSE.
v[2] is TRUE.
v[3] is TRUE.
v[4] is NA.
TRUE 1
FALSE 0
NA_LOGICAL -2147483648
[1] FALSE FALSE  TRUE  TRUE    NA
```

## 論理演算

LogicalVector の要素ごとの論理演算には演算子 `&`（論理積） `|`（論理和） `!`（論理否定）を用います。

```
LogicalVector v1 = {1,1,0,0};
LogicalVector v2 = {1,0,1,0};

LogicalVector res1 = v1 & v2;
LogicalVector res2 = v1 | v2;
LogicalVector res3 = !(v1 | v2);

Rcout << res1 << "\n"; // 1 0 0 0
Rcout << res2 << "\n"; // 1 1 1 0
Rcout << res3 << "\n"; // 0 0 0 1
```

## LogicalVector を受け取る関数

LogicalVector を受け取る関数には `all()` `any()` `ifelse()` があります。

### all() と any()

`LogicalVector` v に対して、`all(v)` は、v の全ての要素が `TRUE` の時 `TRUE` を返します。`any(v)` は、v のいずれかのの要素が `TRUE` の時、`TRUE`を返します。

`all()` 関数や `any()` 関数の返値を if 文の条件式としてそのまま用いることはできません。これは `all()` 関数と `any()` 関数の返値の型は `SingleLogicalResult` という型になっているためです。`all()` 関数や `any()` 関数の返値を if 文の条件式として用いるためには関数 `is_true()` `is_false()` `is_na()` を使って返値を `bool` 型に変換します。

下のコード例では、関数 `all()` と `any()` の返値を if 文の条件式として使うときの方法を示します。この例では、全ての if 文の条件式は真となります、そして、`all()`, `any()` の返値を表示します。

```cpp
// [[Rcpp::export]]
List rcpp_logical_03(){
  LogicalVector v1 = LogicalVector::create(1,1,1,NA_LOGICAL);
  LogicalVector v2 = LogicalVector::create(0,1,0,NA_LOGICAL);
  
  // NA を含む LogicalVector に対する all(), any() の挙動は R と同じです
  LogicalVector lv1 = all( v1 );   // NA
  LogicalVector lv2 = all( v2 );   // FALSE
  LogicalVector lv3 = any( v2 ); // TRUE 
  
  // bool に代入する場合
  bool b1 = is_true ( all(v1) );  // false
  bool b2 = is_false( all(v1) );  // false
  bool b3 = is_na   ( all(v1) );  // true

  // if 文の条件式で用いる場合
  if(is_na(all( v1 ))) { // OK 
    Rcout << "all( v1 ) is NA\n";
  }
  
  return List::create(lv1, lv2, lv3, b1, b2, b3);
}
```

### ifelse()

`ifelse(v, x1, x2)` は論理ベクター v を受け取り、v の要素が `TRUE` の時には x1 の対応する要素を, `FALSE` の時には x2 の対応する要素を返します。 x1, x2 はベクターでもスカラーでも良いですが、ベクターの場合にはその長さは v と一致している必要があります。

```cpp
NumericVector v1;
NumericVector v2;

//ベクトルの要素数
int n = v1.length();

// x1, x2 が スカラー, スカラーの場合
IntegerVector res1     = ifelse( v1>v2, 1, 0);
NumericVector res2     = ifelse( v1>v2, 1.0, 0.0);
//CharacterVector res3 = ifelse( v1>v2, "T", "F"); // 対応していない

// ifelse()が文字列スカラーには対応していないので
// 同等の結果を得るためには要素の値が全て同じである文字列ベクトルを用います
CharacterVector chr_v1 = rep(CharacterVector("T"), n);
CharacterVector chr_v2 = rep(CharacterVector("F"), n);
CharacterVector res3   = ifelse( v1>v2, chr_v1, chr_v2);

// x1, x2 が ベクトル, スカラーの場合
IntegerVector int_v1, int_v2;
NumericVector num_v1, num_v2;
IntegerVector   res4 = ifelse( v1>v2, int_v1, 0);
NumericVector   res5 = ifelse( v1>v2, num_v1, 0.0);
CharacterVector res6 = ifelse( v1>v2, chr_v1, Rf_mkChar("F")); //（注）

// x1, x2 が ベクトル, ベクトルの場合
IntegerVector   res7 = ifelse( v1>v2, int_v1, int_v2);
NumericVector   res8 = ifelse( v1>v2, num_v1, num_v2);
CharacterVector res9 = ifelse( v1>v2, chr_v1, chr_v2);
```

（注）：`Rf_mkChar()` はC言語の文字列型 (`char*`) を `CHARSXP` （`CharacterVector` の要素の型）に変換する関数です。



