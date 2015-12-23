# NA NaN Inf


#非数値 NaN

R_NaN

R_PosInf
R_NegInf


#欠損値 NA

NA (Not Available)

NA_INTEGER,
NA_REAL
NA_STRING
NA_LOGICAL


```
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
List scalar_missings() {
  int int_s = NA_INTEGER;
  String chr_s = NA_STRING;
  bool lgl_s = NA_LOGICAL;
  double num_s = NA_REAL;

  return List::create(int_s, chr_s, lgl_s, num_s);
}
```

```
str(scalar_missings())
#> List of 4
#>  $ : int NA
#>  $ : chr NA
#>  $ : logi TRUE
#>  $ : num NA
```

NA_LOGICAL を bool 型に代入した時だけ、挙動が異なる。

Integer
NA_INTEGER を int型 に代入すると、int 型の最小値の値を取る。Rcpp はこのオブジェクトを R に返すとき NA に変換する。しかし、C++ 中では数値として扱われるので注意が必要。

Double
C++ には NAN があるので 非数値は扱える、R の NaN に変換される。

C++ 
NAN == 1; //false、全ての論理比較は false
NAN == NAN; //false

//下には注意が必要
NAN && TRUE; //true
NAN || FALSE; //true

//NAN に数値演算すると NAN になる
NAN +1; //NAN

String クラスは文字列スカラーを扱うために Rcpp が用意したクラスなので NA の扱いは心得ている。

R の論理型は TRUE, FALSE, NA の値を取る、NA をC++の bool 型に変換すると true になってしまうので注意が必要。

ベクターに値としてNA を入れたい場合は、ベクターの型に合わせて NA_INTEGER,
NA_REAL
NA_STRING
NA_LOGICAL
を使う。

ベクタの要素がNA かどうか調べたいときは、ベクターのメソッド ::is_na() を使う。

```
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
LogicalVector is_naC(NumericVector x) {
  int n = x.size();
  LogicalVector out(n);

  for (int i = 0; i < n; ++i) {
    out[i] = NumericVector::is_na(x[i]);
  }
  return out;
}
```

あるいは suger の is_na() を使うと、論理ベクターを返す

```
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
LogicalVector is_naC2(NumericVector x) {
  return is_na(x);
}
```


#無限の値

