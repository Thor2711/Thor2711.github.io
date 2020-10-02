---
layout: post
title: Bagging predictors
subtitle: Bagging, Why bagging predictor works?
gh-repo: daattali/beautiful-jekyll
gh-badge: [star, fork, follow]
tags: [bagging]
comments: true
---

## [Review] Bagging Predictos, Leo Breiman, 1996


### Abstract 내용은 다음과 같다.

bagging predictors is a method for generating multiple versions of a predictor and using these to get an aggregated predictors.
the multiple versions are formed by making bootstrap replicates of the learning set and using these as new learning sets.
Tree & subset selection in linear regression을 예제로 bagging gives better accuracy를 보이겠다.

Bagging predictor의 성능 향상의 key는 single predictor의 instability를 줄일 수 있다는 것에 있다.
Instability가 큰 경우, bagging predictor로 성능 향상을 할 수 있다는 것을 보여주는 것이 해당 논문의 의의이다.


### Intro

Learning set L = (y_n, x_n)이 있고, predictor phi(x,L)라 하자. phi는 L에 의해서 결정되는 임의의 선정된 prediction method를 의미.

Suppose sequence of learning sets {L_k} from the same underlying distribution as L. L과 같은 분포에서 나온 independent한 learning sets {L_k}가 있다. 

이런 learning sets가 있을 때, single learning set predictor phi(x,L) 보다 좋은 predictor를 {L_k}로 만들 수 있을까?

가장 simple한 idea는 multiple predictors {phi (x, L_k)}를 이용해서 다음의 aggregated predictor를 사용하는 것이다.

(numerical) phi_A (x) = E_L phi(x,L)
(categorical) phi_A (x) = argmax_j N_j, where N_j = |{k; phi(x,L_k) = j} |

일반적으로 생각할 때, targeting이 제대로 된 prediction method phi에 대해서 average 혹은 majority vote를 한다면 variability가 줄어들게 하기 때문이다.

위에서 한가지 필요한 조건은 targeting이 잘 되어있는 phi라는 것이다.

Q. 위의 상황이라면, set을 모두 합쳐서 single learning set predictor로 하는거랑 multiple learning set predictor 하는거랑 무슨차이인가?
A. simple하게 simple regression을 생각할 때, 사실 거의 비슷한 performance를 가짐. 하지만, 앞으로 할 얘기인 bagging predictor의 경우는 bootstrap sample을 사용하므로, bagging과 차이가 있을 것을 생각된다. 

** 그러나, 문제는 independent한 set이 실제로는 존재하지 않는다는 것이다 > 그래서 대신 bootstrap sample을 마치 new learning set처럼 생각해서 bagging predictor를 쓰자.

Take repeated bootstrap samples {L^B} from L, and form phi(x, L^B), then take phi_B

(numerical) phi_B(x) = av_B phi(x, L^B)
(categorical) phi_B(x) = argmax_j N_j, where N_j = |{k; phi(x,L^k) = j}|

A critical factor in whether bagging will improwve accuracy is the stability of predictor phi. Improvement will occur for unstable procedures where a small change in L can result in large changes in phi. For unstable proceduree bagging works well.

(위의 이야기의 기본 전제는 phi 자체의 targeting은 좋은 경우이다.)
phi는 L의 함수인데, L의 변화에 따라 output phi의 변동이 굉장히 크다면 single predictor는 실제로 bad performance를 줄 가능성이 높고, bagging 하는 것이 분산을 크게 줄일 수 있을 것이다.

Q. Instability가 작은 경우에도 bagging 쓰는게 좋지 않은가? 오히려 안좋아질 수도 있는가?
Bagging은 좋지만 불안정한 predictor를 optimal하게 만드는 좋은 방법이다. 반면에, stable한 predictor에 경우 성능을 약간 떨어뜨릴수도 있다. 뒤에서 한번 알아보자.

-- Why Bagging Works.

--- Numeric prediction.

일단 Independent set of sample이 있다고 하고, aggregated predictor를 만들었다고 하자.ㅣ
phi_A (x)  = E_L phi(x, L).

여기에 (x,y) new sample이 들어오면, MSE는 다음과 같이 구할 수 있다.

(y-phi_A(x))^2 = (y-E_L phi(x,L))^2 = (E_L (y-phi(x,L)))^2 <= E_L [(y-phi(x,L))^2] (Jensen's ineq)

여기서 왼쪽은 aggregated predictor의 Squared error, 오른쪽은 single predictor의 Squared error의 평균. 근데 왼쪽이 항상 작다.
이것은 양쪽식에 new data (x,y)에 대한 expectation을 취해도 동일. 즉, 수많은 test case에 대해서 적용해도 항상 왼쪽이 작다.

그럼 얼마나 작을까? 사실 이 point가 중요하다.

Var_L (y-phi(x,L)) = Var_L (phi(x,L))이 클수록 둘의 차이가 커진다 -> 즉 phi(x,L) 함수가 L에 따라 변동이 크다면 aggregated는 훨씬 좋은 performance를 보이게 된다.  
 
Decision tree의 컨셉을 정리하려고 자료를 찾아보다가 기본적인 내용이 잘 정리된 paper를 찾았다.

**Optimal classification trees, Bertsimas, Dunn 2017**

### Abstract를 간략히 정리하면, 다음과 같다. 

"Decision tree methods apply heuristics recursively to create each split in isolation, which may not capture well the underlying characteristics of the data set. The optimal decision tree problem attempts to resolve this by creating the entire decision tree at once to achieve global optimal. We present optimal classification tress, a novel formulation of the decision tree problem using modern mixed-integer optimization (MIO) techniques that yields the optimal decision tree for axes-aligned split."

**Decision tree 알고리즘은 heuristic하게 하나의 split씩 순차적으로 만들게되는데, 이런 one-step optimization으로는 사실 global optimal을 찾지 못할 수도 있다. 그럼 global optimal을 찾기 위해서는 tree를 step-by-step이 아닌 한번에 만드는 방법이 있다. 예를 들어, 한 node에서 split 이후 만들어질 수 있는 모든 subtree를 고려해서 optimize하는 것이 있겠지만 이는 NP-complete로 알려져있다 (NP-complete의 개념을 정확히 이해하진 못했지만, polynomial보다 오래 걸리는 문제를 의미하는 것 같음, 즉 상당한 computation time을 요구하는 문제). 이 논문에서는  optimal decision tree를 찾는 문제를 mixed-integer optimization 문제로 바꾸면 구할 수 있다?**

왜 greedy algorithm으로 하나하나의 분기를 최적화하는 것은 global optimal을 찾지 못할까? TSP(Traveling Saleman Problem)와 비슷한 느낌인데, 모든 가능한 case의 비교가 불가능하기 때문에 각 분기에서 가장 최소비용을 주는 path를 찾아간다. 단, 이것은 현재 간 path가 비용을 작게 주더라도 그 이후의 모든 path의 비용이 큰 경우 사실 잘못간 것이 된다. 현재의 최소비용의 선택은 그 다음에 선택에서는 오히려 큰 비용이 될 수 있고, 이것이 greedy algorithm의 한계이다.

모든 case를 고려한 global optimal을 찾는 것은 불가한가? 'Hyafil and Rivest 1976'에서는 비용을 최소화하는 tree를 찾는 것이 NP-Complete라는 것을 증명하였다 (해당 논문에서는 leaf node까지의 거리를 최소화하는 tree를 optimal tree라 정의하는데, 결국 이것은 misclassification rate를 가장 min하는 tree를 찾는 것과 step이 동일하므로 같은 NP-complete.)

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
