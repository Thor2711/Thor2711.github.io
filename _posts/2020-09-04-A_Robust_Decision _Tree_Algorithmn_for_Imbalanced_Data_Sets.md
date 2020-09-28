---
layout: post
title: About Decision Tree Algorithm (1)
subtitle: Optimal Decision Tree, Greedy Algorithm, Use Entropy
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [Imbalanced data problem, decision tree]
comments: true
---

## [Review] Optimal classification trees, Bertsimas, Dunn 2017

Decision tree의 컨셉을 정리하려고 자료를 찾아보다가 기본적인 내용이 잘 정리된 paper를 찾았다.

**Optimal classification trees, Bertsimas, Dunn 2017**

### Abstract를 간략히 정리하면, 다음과 같다. 

"Decision tree methods apply heuristics recursively to create each split in isolation, which may not capture well the underlying characteristics of the data set. The optimal decision tree problem attempts to resolve this by creating the entire decision tree at once to achieve global optimal. We present optimal classification tress, a novel formulation of the decision tree problem using modern mixed-integer optimization (MIO) techniques that yields the optimal decision tree for axes-aligned split."

**Decision tree 알고리즘은 heuristic하게 하나의 split씩 순차적으로 만들게되는데, 이런 one-step optimization으로는 사실 global optimal을 찾지 못할 수도 있다. 그럼 global optimal을 찾기 위해서는 tree를 step-by-step이 아닌 한번에 만드는 방법이 있다. 예를 들어, 한 node에서 split 이후 만들어질 수 있는 모든 subtree를 고려해서 optimize하는 것이 있겠지만 이는 NP-complete로 알려져있다 (NP-complete의 개념을 정확히 이해하진 못했지만, polynomial보다 오래 걸리는 문제를 의미하는 것 같음, 즉 상당한 computation time을 요구하는 문제). 이 논문에서는  optimal decision tree를 찾는 문제를 mixed-integer optimization 문제로 바꾸면 구할 수 있다?**

왜 greedy algorithm으로 하나하나의 분기를 최적화하는 것은 global optimal을 찾지 못할까? TSP(Traveling Saleman Problem)와 비슷한 느낌인데, 모든 가능한 case의 비교가 불가능하기 때문에 각 분기에서 가장 최소비용을 주는 path를 찾아간다. 단, 이것은 현재 간 path가 비용을 작게 주더라도 그 이후의 모든 path의 비용이 큰 경우 사실 잘못간 것이 된다. 현재의 최소비용의 선택은 그 다음에 선택에서는 오히려 큰 비용이 될 수 있고, 이것이 greedy algorithm의 한계이다.

모든 case를 고려한 global optimal을 찾는 것은 불가한가? 'Hyafil and Rivest 1976'에서는 비용을 최소화하는 tree를 찾는 것이 NP-Complete라는 것을 증명하였다 (해당 논문에서는 leaf node까지의 거리를 최소화하는 tree를 찾는 것이며, 이것은 leaf node의 misclassification을 최소화하는 tree를 찾는 것과 같은 class의 문제일것 같다).

### Introduction






l
**Here is some bold text**

## Here is a secondary heading

Here's a useless table:

| Number | Next number | Previous number |
| :------ |:--- | :--- |
| Five | Six | Four |
| Ten | Eleven | Nine |
| Seven | Eight | Six |
| Two | Three | One |


How about a yummy crepe?

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg)

It can also be centered!

![Crepe](https://s3-media3.fl.yelpcdn.com/bphoto/cQ1Yoa75m2yUFFbY2xwuqw/348s.jpg){: .mx-auto.d-block :}

Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.
