# 四則演算

#ベクトル演算

四則演算は R と同様の記法でOK

```
NumericVector x ;
NumericVector y ;

// expressions involving two vectors
NumericVector res = x + y ;
NumericVector res = x - y ;
NumericVector res = x * y ;
NumericVector res = x / y ;
// one vector, one single value
NumericVector res = x + 2.0 ;
NumericVector res = 2.0 - x;
NumericVector res = y * 2.0 ;
NumericVector res = 2.0 / y;
// two expressions
NumericVector res = x * y + y / 2.0 ;
NumericVector res = x * ( y - 2.0 ) ;
NumericVector res = x / ( y * y ) ;
```

-演算子で符号を反転

```
// negate x
NumericVector res = -x ;
```


#論理ベクター

数値ベクトル同士の値の比較により、論理ベクトルを生成するのも、Rと同様

```
// two integer vectors of the same size
NumericVector x ;
NumericVector y ;
// expressions involving two vectors
LogicalVector res = x < y ;
LogicalVector res = x > y ;
LogicalVector res = x <= y ;
LogicalVector res = x >= y ;
LogicalVector res = x == y ;
LogicalVector res = x != y ;
// one vector, one single value
LogicalVector res = x < 2 ;
LogicalVector res = 2 > x;
LogicalVector res = y <= 2 ;
LogicalVector res = 2 != y;
// two expressions
LogicalVector res = ( x + y ) < ( x*x ) ;
LogicalVector res = ( x + y ) >= ( x*x ) ;
LogicalVector res = ( x + y ) == ( x*x ) ;

// ! 演算子で論理値を反転
// negate the logical expression "y < z"
LogicalVector res = ! ( x < y );
```


論理値ベクターを使って、ベクターの要素にアクセスできる。
x[x < 2]