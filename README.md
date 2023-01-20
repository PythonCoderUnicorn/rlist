# rlist

a repo for showing the {rlist} package, the information is from the rlist guide documentation. 

- rlist package imports jsonlite package to read/write JSON data.
- rlist can handle YAML files

## JSON data

```
js = '[
    {"name": "Apple", "age": 67},
    {"name": "Windows", "age": 78},
    {"name": "Linux", "age": 44}
  ]'

str( list.parse(js, "json") )
```

Prints
```
List of 3
 $ :List of 2
  ..$ name: chr "Apple"
  ..$ age : int 67
 $ :List of 2
  ..$ name: chr "Windows"
  ..$ age : int 78
 $ :List of 2
  ..$ name: chr "Linux"
  ..$ age : int 44
```

## YAML data

```
yam = "
p1:
  name: Apple
  age: 167
p2:
  name: Windows
  age: 89
p3:
  name: Linux
  age: 56
"

parsed_yaml = list.parse(yam, "yaml") 
parsed_yaml
```

Prints
```
$p1
$p1$name
[1] "Apple"

$p1$age
[1] 167

(truncated)
```


## `list.load()` & `list.save()`

this looks at file extension and determines which loader to use

```
j = "https://renkun-ken.github.io/rlist-tutorial/data/sample.json"

small_json = list.load(file= j, type = 'json')
```















---

### Mapping a JSON file

```
j = "https://renkun-ken.github.io/rlist-tutorial/data/sample.json"

small_json = list.load(file= j, type = 'json')

str(small_json)
```

Prints 
```
List of 3
 $ :List of 4
  ..$ Name     : chr "Ken"
  ..$ Age      : int 24
  ..$ Interests: chr [1:3] "reading" "music" "movies"
  ..$ Expertise:List of 3
  .. ..$ R     : int 2
  .. ..$ CSharp: int 4
  .. ..$ Python: int 3

(truncated)
```


### `lapply()` or `list.map()` JSON file

```
#--- method 1

lapply(small_json, function(x) {
  x$Name
})

#--- method 2

list.map(small_json, Name)
```

Prints
```
[[1]]
[1] "Ken"

[[2]]
[1] "James"

[[3]]
[1] "Penny"
```

You can use a function with list.map()

```
list.map(small_json, sum( as.numeric(Expertise) )  )

list.map(small_json, list(Name = Name, range = range(as.numeric(Expertise))))
```

### list.map shorthand

In some cases we need to refer to the item itself, or its index in the list, or even its name. 
In the expression:

- `.` represents the item itself 
- `.i` represents its index
- `.name` represents its name.

```
nums =  c(a=3, b=2, c=1)

list.map(nums, . + 1)
```

prints

```
$a
[1] 4

$b
[1] 3

$c
[1] 2
```


## `list.mapv()`

get a vector instead of a list returned

```
list.mapv(small_json, Age)
```

Prints
```
[1] 24 25 24
```


### `list.maps()`

can use more than one list

```
L1 = list(p1= list(x=1, y=2), 
          p2= list(x=3, y=4), 
          p3= list(x=1, y=3) 
          )

L2 = list(2, 3, 5)

list.maps(a$x * b + a$y, a= L1, b=L2)
```







## `list.select()`

Get the Name and Expertise elements 
```
list.select(small_json, Name, Expertise)
```


## `list.iter()`

just get a value

```
list.iter(small_json, cat(Name, ":", Age, "\n"))
```

Prints
```
Ken : 24 
James : 25 
Penny : 24 
```


## Filter list

`list.filter()` filters a list by an expression that returns TRUE or FALSE.

```
list.filter(small_json, Age >= 25)
```

# pipeR

You can use the pipe operator with a list

```
library(pipeR)

small_json %>>%
  list.filter(Age >= 25) %>>%
  list.mapv(Name)
```


## `list.find()`

```
people %>>%
  list.find(Age >= 25,n = 10) %>>%
  list.mapv(Name)
  
# find index number location
list.findi(people, Age >= 23, 2)
```


## `list.take()`

```
list.take(5:10, 3)      # returns [1] 5 6 7
```

## `list.skip()`

```
list.skip(1:10, 3)     # returns  [1]  4  5  6  7  8  9 10
```

## `list.count()`

```
list.count(people, "R" %in% names(Expertise))
```

## `list.match()`

```
data = list(a1= 3, a3= 7, c= 9)

list.match(data, "a[3]")      

# returns 
$a3
[1] 7
```

## `list.subset()`

```
small_json %>>% 
  subset(Age >= 24, Name)
  
 
small_json %>>%
  list.select(Name, Age) %>>%
  list.stack
```

## `list.order()`

```
list.order(small_json, Age)
```

## `list.sort()`

```
small_json %>>%
  list.sort(Age) %>>%
  list.select(Name, Age)
```

## `list.cases()`

```
list.cases(small_json, Interests)
```

Prints
```
[1] "movies"  "music"   "reading" "sports" 
```


## list.table

table() builds a contingency table of the counts at each combination of factor levels using cross-classifying factors. 
list.table() is a wrapper that creates a table in which each dimension results from the values for an expression.

The function is very handy to serve as a counter. The following examples shows an easy way to know the remainders 
and the number of integers from 1 to 1000 when each is divided by 3.

```
list.table(1:1000, . %% 3)
```

Prints
```
  0   1   2 
333 334 333 
```


## `list.join()`

```
newinfo = list(
    list(Name="Ken", Email="ken@xyz.com"),
    list(Name="Penny", Email="penny@xyz.com"),
    list(Name="James", Email="james@xyz.com")
    )


list.join(small_json, newinfo, Name)
```


## `list.search()`

```
list.search(friends, . == "Ken")
```

## `list.parse()`

convert an object to a list, convert dataframe or a matrix to a list

```
df = data.frame(
  names = c("Linux","Mac","Windows"),
  age = c(58,57,48),
  stringsAsFactors = F
)

list.parse( df)
```


## `list.stack()`

the opposite of list.parse() on a dataframe, creates a dataframe
Take the parsed yaml data and put in list.stack

```
list.stack( parsed_yaml)


list.load("https://renkun-ken.github.io/rlist-tutorial/data/sample.json") %>>%
  list.select(Name, Age) %>>%
  list.stack
```



## `list.flatten()`

```{r}
d = list(
  list(
    a= 10,
    b= 20
  ),
  list(
    c(c= 245,
      d= list(w= 899,
              h= 1788 ))
  )
)

list.flatten(d)

```



## `list.append()`

```
list.append(list(a=100, b= 200, c= 300))
```

## `list.prepend()`

```
list.prepend( list(alpha = 10, beta= 20, ceta= 30))
```

## `list.zip()`

```{r}
str( list.zip(a= c(1,2,3), b= c(10,20,30)) )
```



## `list.rbind()`

```{r}
scores = list(
  score1 = c(10,20,30),
  score2 = c(100, 200, 300),
  score3 = c(1e3, 2e3, 3e3)
)

list.rbind( scores)
```

Prints

```
       [,1] [,2] [,3]
score1   10   20   30
score2  100  200  300
score3 1000 2000 3000
```


```{r}
list.cbind( scores )
```
Prints
```
     score1 score2 score3
[1,]     10    100   1000
[2,]     20    200   2000
[3,]     30    300   3000
```

## list to matrix

```{r}
scores3 = list(
  score1.1 = list(150, 250, 350),
  score2.1 = list(1150, 2150, 3150),
  period = list('one','two','three')
)

scores3_r = list.rbind( scores3)
scores3_r
```

Prints
```
         [,1]  [,2]  [,3]   
score1.1 150   250   350    
score2.1 1150  2150  3150   
period   "one" "two" "three"
```


## lambda

```{r}
x = list(
  a= c(w= 10, d= 20 ),
  b = c(w= 100, d= 200)
)

list.map(x, sum(d))
```
















