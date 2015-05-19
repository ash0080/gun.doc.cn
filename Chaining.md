

Chained functions may be context independent of their proceeding function, or they may be dependent on the results of the proceeding function.  The Chaining Dependency Table below shows the relationship between immediately adjacent, chained functions.  

### Examples
  - ```gun.put({hello: "world").key("my/data")``` 
    - **```key```** proceeded by *```put```* is dependent on the results of *```put```*
    -  i.e. the **```key```** will be assigned to the same object as the *```put```*
  - ```gun.put({hello: "world").key("my/data").val(function(data){})``` 
    - **```val```** proceeded by *```key```* is independent of the results of *```key```*, therefore ```val``` will work its way back to the previous dependent function, in this case the ```put``` function
    - i.e., both ```key``` and ```val``` are dependent on the results of ```put```


### Chaining Dependency Table

|        |  put  |  get  |  path |  map  |  all  |  key  |  val  |  on   |
|:------:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
| *put*  |   D   |***i***|   D   |   D   |***i***|   D   |   D   |***i***|
| *get*  |   D   |***i***|   D   |   D   |***i***|   D   |   D   |***i***|
| *path* |   D   |***i***|   D   |   D   |***i***|   D   |   D   |***i***|
| *map*  |   D   |***i***|   D   |   D   |***i***|   D   |   D   |***i***|
| *all*  |***i***|***i***|   D   |   D   |***i***|***i***|   D   |   D   |
| *key*  |***i***|***i***|***i***|***i***|***i***|***i***|***i***|***i***|
| *val*  |***i***|***i***|***i***|***i***|***i***|***i***|***i***|***i***|
| *on*   |***i***|***i***|***i***|***i***|***i***|***i***|***i***|***i***|

Rows represent the initial function, while columns represent the subsequent function.
  - D : Subsequent function is dependent on the previous function.  
  - ***i*** : Subsequent function is independent of the previous function.  