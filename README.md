# rlist

a repo for showing the {rlist} package, the information is from the rlist guide documentation. 

- rlist package imports jsonlite package to read/write JSON data.
- rlist can handle YAML files


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


### `list.take()`

```
list.take(5:10, 3)      # returns [1] 5 6 7
```


























