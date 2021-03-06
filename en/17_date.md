# Date

`Date` is a scalar type corresponding to an element of `DateVector`.

##　Creating Date object

```cpp
Date d;       //"1970-01-01"
Date d(1);    //"1970-01-01" + 1day
Date d(1.1);  //"1970-01-01" + ceil(1.1)day
Date( "2000-01-01", "%Y-%m-%d"); //default format is "%Y-%m-%d"
Date( 1, 2, 2000); // 2000-01-02 Date(mon, day, year)
Date( 2000, 1, 2); // 2000-01-02 Date(year, mon, day)
```
## Operators

`Date` has operators `+, -, <, >, >=, <=, ==, !=`. By using these operators, you can perform addition of days (`+`), difference calculation of date (`-`), comparison of dates (`<, <=, >, >=, ==, !=`) .
```
// [[Rcpp::export]]
DateVector rcpp_date1(){
    // Creating Date objects
    Date d1("2000-01-01");
    Date d2("2000-02-01");

    int  i  = d2 - d1; // difference of dates
    bool b  = d2 > d1; // comparison of dates

    Rcout << i << "\n"; // 31
    Rcout << b << "\n"; // 1


    DateVector date(1);
    date[0] = d1 + 1;  // adding 1 day to d1

    return date; // 2000-01-02
}
```



## Member functions


#### getDay()

Returns the day of the date.

#### getMonth()

Returns the month of the date.

#### getYear()

Returns the year of the date.

#### getWeekday()

Returns the day of the week as an int. (1:Sun 2:Mon 3:Tue 4:Wed 5:Thu 6:Sat)

#### getYearday()

Returns the number of the date through the year with January 1st as 1 and December 31st as 365.

#### is_na()

Returns `true` if this object is NA.

## Execution result

```
Date d("2016-1-1");
Rcout << d.getDay() << endl;     //1
Rcout << d.getMonth() << endl;   //1
Rcout << d.getYear() << endl;    //2016
Rcout << d.getWeekday() << endl; //6
Rcout << d.getYearday() << endl; //1
```
