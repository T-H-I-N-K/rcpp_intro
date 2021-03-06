# NA NaN Inf NULL


## Notations of NA NaN Inf

To express the value of `Inf` `-Inf` `NaN` in Rcpp, use the symbol `R_PosInf` `R_NegInf` `R_NaN`.

|R|Rcpp|
|---|---|
|`Inf|`R_PosInf`|
|`-Inf`|`R_NegInf`|
|`NaN`|`R_NaN`|

On the other hand, for `NA`, different symbol of `NA`  are defined for each `Vector` type.

| Vector | symbol of NA |
|---|---|
|`NumericVector`|`NA_REAL`|
|`IntegerVector`|`NA_INTEGER`|
|`LogicalVector`|`NA_LOGICAL`|
|`CharacterVector`|`NA_STRING`|

The following code example shows how to use these symbols to create `Vector` object.

```cpp
NumericVector   v1 = NumericVector::create( 1, NA_REAL, R_NaN, R_PosInf, R_NegInf);
IntegerVector   v2 = IntegerVector::create( 1, NA_INTEGER);
CharacterVector v3 = CharacterVector::create( "A", NA_STRING);
LogicalVector   v4 = LogicalVector::create( true, NA_LOGICAL);
```

## Evaluating NA NaN Inf

### Evaluating all the elements of a vector at once

To evaluate all the `NA` `NaN` `Inf` `-Inf` elements in a vector at once, use the function `is_na()` `is_nan()` `is_infinite()`.

In the code example below, we create a vector containing `NA` `NaN` `Inf` `-Inf` and evaluate it. From this example we can see that the `is_na()` evaluates both `NA` and` NaN` as `TRUE` (same as R's `is.na()`).

```cpp
NumericVector v =
    NumericVector::create( 1, NA_REAL, R_NaN, R_PosInf, R_NegInf);
LogicalVector l1 = is_na(v);
LogicalVector l2 = is_nan(v);
LogicalVector l3 = is_infinite(v);
Rcout << l1 << "\n"; // 0 1 1 0 0
Rcout << l2 << "\n"; // 0 0 1 0 0
Rcout << l3 << "\n"; // 0 0 0 1 1
```

You can remove `NA` `NaN` `Inf` from a vector by using these functions. You can also use `na_omit()` to remove `NA`.

The code example below shows how to remove `NA` from a vector using the `is_na()` and `na_omit()`.


```
// Creating a Vector object containg NA
NumericVector v =
    NumericVector::create( 1, NA_REAL, 2, NA_REAL, 3);

// Removeing NA from the vector
NumericVector v1 = v[!is_na(v)];
NumericVector v2 = na_omit(v);
```

### Evaluating single element of a vector

If you want to evaluate `NA` `NaN` `Inf` `-Inf` on single element of a vector, use the static member function `Vector::is_na()`, `traits::is_nan<RTYPE>()`, `traits:: is_infinite<RTYPE>()`. In `RTYPE`, specify `SEXPTYPE` of the vector to be evaluated.


```cpp
// [[Rcpp::export]]
void rcpp_is_na2() {

    // Creating Vector object containing NA NaN Inf -Inf
    NumericVector v =
        NumericVector::create(1, NA_REAL, R_NaN, R_PosInf, R_NegInf);

    // Evaluating the value for each element of the vector
    int n = v.length();
    for (int i = 0; i < n; ++i) {
        if(NumericVector::is_na(v[i]))
            Rprintf("v[%i] is NA.\n", i);
        if(Rcpp::traits::is_nan<REALSXP>(v[i]))
            Rprintf("v[%i] is NaN.\n", i);
        if(Rcpp::traits::is_infinite<REALSXP>(v[i]))
            Rprintf("v[%i] is Inf or -Inf.\n", i);
    }
}
```

Here is the list of `SEXPTYPE` of the major Vector class.

|SEXPTYPE|Vector|
|---|---|
|`LGLSXP`|LogicalVector|
|`INTSXP`|IntegerVector|
|`REALSXP`|NumericVector|
|`CPLXSXP`|ComplexVector|
|`STRSXP`|CharacterVector (StringVector)|




## NULL

You use `R_NilValue` to handle` NULL` in Rcpp. The code example below shows how to check `NULL` in the elements of a `List` object and how to assign `NULL` to clear the value of an attribute.

```
// [[Rcpp::export]]
List rcpp_list()
{
    // Create a List object with element names
    // One of the two elements is NULL
    List L = List::create(Named("x",NumericVector({1,2,3})),
                          Named("y",R_NilValue));

    // Checking NULL
    for(int i=0; i<L.length(); ++i){
        if(L[i]==R_NilValue) {
            Rprintf("L[%i] is NULL.\n\n", i+1);
        }
    }

    // Delete the value of the attribute (element name) of the object
    L.attr("names") = R_NilValue;

    return(L);
}
```

Execution result

```
> rcpp_list()
L[2] is NULL.

[[1]]
[1] 1 2 3

[[2]]
NULL
```




## Points to note when handling NA with Rcpp

Internally `NA_INTEGER` and `NA_LOGICAL` are equivalent to the minimum value of `int` (-2147483648). Although, functions and operators defined in Rcpp handle the minimum value of `int` appropriately as` NA` (that is, make the result of operation on `NA` element as `NA`). Standard C++ functions and operators treat the minimum value of `int` as integer value. So if you add 1 to `NA_INTEGER`, the element is no longer minimum value of `int`, so it is not　treated as `NA`.

In addition, if you assign `NA_LOGICAL` to `bool` type, it will always be evaluated as `true`. This is because `bool` evaluates all numbers other than 0 as `true`.

On the other hand, since `nan` and　`inf` are defined in `double`, the result of the operation on　`nan` `inf`  in standard C ++ will be the same result as R.

The table below summarizes how values are evaluated when assigning the value of `NA` `Inf` `-Inf` `NaN` to `Vector` or scalar type in C++.

|                 |      NA     |     NaN     |    Inf      |    -Inf     |
|:---------------:|:-----------:|:-----------:|:-----------:|:-----------:|
| `NumericVector` | `NA_REAL`   |  `R_NaN`    | `R_PosInf`  | `R_NegInf`  |
| `IntegerVector` | `NA_INTEGER`|`NA_INTEGER` |`NA_INTEGER` |`NA_INTEGER` |
| `LogicalVector` | `NA_LOGICAL`|`NA_LOGICAL` |`NA_LOGICAL` |`NA_LOGICAL` |
|`CharacterVector`| `NA_STRING` |    "NaN"    |    "Inf"    |    "-Inf"   |
|     `String`    | `NA_STRING` |    "NaN"    |    "Inf"    |    "-Inf"   |
|     `double`    |    `nan`    |    `nan`    |    `inf`    |    `-inf`   |
|      `int`      | -2147483648 | -2147483648 | -2147483648 | -2147483648 |
|      `bool`     |    `true`   |    `true`   |    `true`   |    `true`   |

The code example below shows the difference in results when computing using the Rcpp operator and the standard C++ operator against `NA_INTEGER`. From this result, the operator of Rcpp evaluates the result of the operation on `NA` as `NA`, but you can see that the standard C++ operator treats `NA_INTEGER` as just a integer value.


```cpp
// [[Rcpp::export]]
List rcpp_na_sum(){

    // Creating an integer vector containing NA
    IntegerVector v1  = IntegerVector::create(1,NA_INTEGER,3);

    // Applying the "+" operator between vector and scalar defined in Rcpp
    IntegerVector res1 = v1 + 1;

    // Applying the "+" operator between int and int defined in standard C++
    IntegerVector res2(3);
    for(int i=0; i<v1.length(); ++i){
        res2[i] = v1[i] + 1;
    }

    // Outputing the result as named list
    return List::create(Named("Rcpp plus", res1),
                        Named("C++  plus", res2));
}
```

Execution result

```
> rcpp_na_sum()
$`Rcpp plus`
[1]  2 NA  4

$`C++  plus`
[1]  2 -2147483647 4
```
