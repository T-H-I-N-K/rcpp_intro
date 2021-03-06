# RObject

The `RObject` type is a type that can be assigned to any type of object defined in Rcpp. If you do not know what type is passed to the variable at run time, you can use `RObject`.

## Member functions

`RObject` has the following member functions. These member functions also exist in all other API classes (such as `NumericVector`) in Rcpp.

#### inherits(str)

Returns `true` if this object inherits the class specified by the string `str`.


#### slot(name)

Accesses the slot specified by the character string `name` if this object is `S4`.

#### hasSlot(name)

Returns `true` if there is a slot specified by the character string name.

#### attr(name)

Accesses the attribute specified by the string `name`.


#### attributeNames()

Return the names of all the attributes of this object as `std::vector<std::string>`.

#### hasAttribute(name)

Returns `true` if this object has an attribute with the name specified by the string `name`.

#### isNULL()

Returns true if this object is `NULL`.

#### sexp_type()

Returns `SXPTYPE` of this object as `int`. See the [R internals](https://cran.r-project.org/doc/manuals/r-release/R-ints.html#SEXPTYPEs) for a list of all `SEXPTYPE` defined in R.

#### isObject()

Returns `true` if this object has a "class" attribute.


#### isS4()

Return `true` if this object is a `S4` object.



## Determining type of object assigned to RObject

One useful use of `RObject` is to determine the type of the object. To determine which type the value assigned to `RObject`, use the `is<T>()` function or member function `isS4()` `isNULL()`.

However, matrices and factor vectors can not be determined by only using the function 'is<T>()` because they are vectors with values are set to specific attribute. To determine them, use `Rf_isMatrix()` function or the `Rf_isFactor()` function.

The code example below shows how to determine the type using `RObject`.

```
// [[Rcpp::export]]
void rcpp_type(RObject x){
    if(is<NumericVector>(x)){
        if(Rf_isMatrix(x)) Rcout << "NumericMatrix\n";
        else Rcout << "NumericVector\n";       
    }
    else if(is<IntegerVector>(x)){
        if(Rf_isFactor(x)) Rcout << "factor\n";
        else Rcout << "IntegerVector\n";
    }
    else if(is<CharacterVector>(x))
        Rcout << "CharacterVector\n";
    else if(is<LogicalVector>(x))
        Rcout << "LogicalVector\n";
    else if(is<DataFrame>(x))
        Rcout << "DataFrame\n";
    else if(is<List>(x))
        Rcout << "List\n";
    else if(x.isS4())
        Rcout << "S4\n";
    else if(x.isNULL())
        Rcout << "NULL\n";
    else
        Rcout << "unknown\n";
}
```

Use `as<T>()` to convert `RObject` to another Rcpp type after determining the type.


```
// Converting `RObject` to `NumericVector`
RObject x;
NumericVector v = as<NumericVector>(x);
```
