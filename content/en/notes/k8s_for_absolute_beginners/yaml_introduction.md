---
title: "Yaml Introduction"
date: 2022-11-01T12:33:52-07:00
draft: false
description : "Yaml Introduction"
meta_image: notes/k8s_for_absolute_beginners/k8s.png
image: notes/k8s_for_absolute_beginners/k8s.png
weight : 15
---
{{< featuredImage >}}

This notes is for the course [Kuberenetes for absolute beginners](https://www.udemy.com/course/learn-kubernetes/)


#### Yaml introduction


According to yaml.org, **"YAML is a human-friendly, data serialization standard for all programming languages."**

Yaml is used to create kubernetes configurations


#### Structure of YAML file

##### Key Value Pair 
The basic type of entry in a YAML file is of a key value pair. After the Key and colon there is a space and then the value.

```
Fruit: Apple
Vegetable: Radish
Liquid: Water
```

##### Array/List
Lists would have a name and a number of items listed under it. The elements of the list would start with a **-**. There can be a n of lists, however the indentation of various elements of the array matters a lot.

```
Fruits:
  - Orange
  - Banana
  - Mango
Vegetables:
  - Potato
  - Tomato
  - Carrot
```

##### Dictionary/Map
A more complex type of YAML file would be a Dictionary/Map.

```
Banana:
  Calories: 200
  Fat: 0.5g
  Carbs: 30g
Grapes:
  Calories: 100
  Fat: 0.4g
  Carbs: 20g
```


{{< box >}}
It is important to indent Yaml properly. Improper indentation would break a sibling / parent relationship between the properties.
{{< /box >}}


#### Advanced YAML structures

##### List containing a list of dictionaries.
```
- Fruits:
    - Banana:
        Calories: 105
        Fat: 0.4g
        Carbs:  27g
    - Grape:
        Calories: 62
        Fat: 0.3g
        Carbs:  16g
- Vegetables:
    - Potato:
        Calories: 105
        Fat: 0.4g
        Carbs:  27g
```

#### Dictionary in Dictionary

```
Banana:
  type:
    name: musa
  Calories: 200
  Fat: 0.5g
  Carbs: 30g
```

{{< box >}}
**A list is ordered**
**A dictionary is unordered**
{{< /box >}}

