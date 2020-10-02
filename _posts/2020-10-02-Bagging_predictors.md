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

Q. 위의 상황이라면, set을 모두 합쳐서 single learning set predictor로 하는거랑 multiple learning set predictor 하는거랑 무슨차이인가?

A. simple하게 simple regression을 생각할 때, 사실 거의 비슷한 performance를 가짐. 하지만, 앞으로 할 얘기인 bagging predictor의 경우는 bootstrap sample을 사용하므로, bagging과 차이가 있을 것을 생각된다. 

**그러나, 문제는 independent한 set이 실제로는 존재하지 않는다는 것이다 > 그래서 대신 bootstrap sample을 마치 new learning set처럼 생각해서 bagging predictor를 쓰자.**

Take repeated bootstrap samples {L^B} from L, and form phi(x, L^B), then take phi_B

(numerical) phi_B(x) = av_B phi(x, L^B)

(categorical) phi_B(x) = argmax_j N_j, where N_j = |{k; phi(x,L^k) = j}|

A critical factor in whether bagging will improve accuracy is the stability of predictor phi. Improvement will occur for unstable procedures where a small change in L can result in large changes in phi. For unstable proceduree bagging works well.

phi는 L의 함수인데, L의 변화에 따라 output phi의 변동이 굉장히 크다면 single predictor는 실제로 bad performance를 줄 가능성이 높고, bagging 하는 것이 분산을 크게 줄일 수 있을 것이다.

Q. Instability가 작은 경우에도 bagging 쓰는게 좋지 않은가? 오히려 안좋아질 수도 있는가?

Bagging은 좋지만 불안정한 predictor를 optimal하게 만드는 좋은 방법이다. 반면에, stable한 predictor에 경우 성능을 약간 떨어뜨릴수도 있다. 뒤에서 한번 알아보자.

### Why Bagging Works.

#### Numeric prediction.

일단 Independent set of sample이 있다고 하고, aggregated predictor를 만들었다고 하자.

phi_A (x)  = E_L phi(x, L).

여기에 (x,y) new sample이 들어오면, MSE는 다음과 같이 구할 수 있다.

(y-phi_A(x))^2 = (y-E_L phi(x,L))^2 = (E_L (y-phi(x,L)))^2 <= E_L [(y-phi(x,L))^2] (Jensen's ineq)

여기서 왼쪽은 aggregated predictor의 Squared error, 오른쪽은 single predictor의 Squared error의 평균. 근데 왼쪽이 항상 작다.
이것은 양쪽식에 new data (x,y)에 대한 expectation을 취해도 동일. 즉, 수많은 test case에 대해서 적용해도 항상 왼쪽이 작다.

**그럼 얼마나 작을까? 사실 이 point가 중요하다.**

Var_L (y-phi(x,L)) = Var_L (phi(x,L))이 클수록 둘의 차이가 커진다. 즉 phi(x,L) 함수가 L에 따라 변동이 크다면 aggregated는 훨씬 좋은 performance를 보이게 된다.  

허나 한가지 문제점은, 우리는 phi_A가 아닌 phi_B를 사용한다는 것이다. 즉, L의 underlying distribution을 쓰는 것이 아닌 P_L 이라는 learning set의 empirical 분포를 사용한다는 것 (이를 bootstrap approximation to P라 부름). 그러면 위의 inequality가 성립하지 않음.

phi_B (x) = phi_A (x, P_L).

**이렇게 되면, 다음 가정이 맞는 경우에 잘 working함을 보장한다. phi가 unstable할 때.**

E_y (y-phi_B(x))^2 = E_y [(y-phi_A(x) + phi_A(x) - phi_B(x))^2] =  E_y (y-phi_A(x))^2 + (phi_A(x) - phi_B(x))^2

즉, (phi_A(x) - phi_B(x))^2 이 큰가 혹은 Var_L(phi(x,L))이 큰가의 문제이다.   

만약 phi가 unstable하다면, phi_B와 phi_A의 bias가 차이가 있겠지만(P > P_L로 차이), variablility를 줄이면서 오히려 좋아질 것이다.

만약 phi가 stable하다면, 오히려 phi_B와 phi_A의 차이가 phi의 variability보다 클 수가 있다. 이런 경우 더 worst.



#### Classification

역시 independent set of sample이 있다고 하자. 이 경우, 수많은 phi(x,L)들은 다음과 같이 불확실성은 표현할 수 있다.

Q(j|x) = P(phi(x,L) = j), phi(x,L)이 j가 될 확률을 의미함.

이제 P(j|x)를 정의하자. P(j|x)는 실제 x-y간 관계를 나타내는 underlying true distribution이다.

그럼 실제로 phi라는 방법이 제대로 targeting하는 확률은 다음과 같이 계산된다.

E_y,L 1(phi(x,L) = y) = \sum_j Q(j|x) P(j|x), 이 식은 phi 방법 자체의 performance의 평균을 의미한다.


수많은 방법 phi에 대해서 다음은 항상 성립한다.

\sum_j Q(j|x) P(j|x) <= max_j P(j|x)

여기서 equality가 성립하려면, P(j|x)가 max인 j에서만 1을 갖는 Q가 되어야함. 

Q(j|x) = 1, argmax_j P(j|x)
         0, o.w

이런 Q가 되려면, phi는 

phi(x) = argmax_j P(j|x) 여야한다. 이것은 Basyes Predictor이다

실제로 우리가 사용하는 phi는 현실적으로 이런 bayes predictor가 되기는 어렵고, 현실적으로 가능한 order-correct phi를 다음과 같이 정의한다.

argmax_j Q(j|x) = argmax_j P(j|x).

한 가지 여기서 overall correct rate를 정의하면

r = \int  \sum_j Q(j|x) P(j|x) P_x (dx) 가 되는데 이것의 상한선 r*는

r* = \int max_j P(j|x) P_x (dx).

물론 order-correct phi의 경우, performance의 평균은 bayes predictor의 performance에 비해 낮지만, phi를 aggregate한다면

phi_A(x) = argmax_j Q(j|x) 이고, 결과적으로 x에서 order-correct phi의 경우 그 aggregate version은 bayes predictor가 된다.

만약 일부의 x \in C 에서만 order-correct인 경우에 correct rate를 구하면, 

r_A = \int_x\inC max_j P(j|x) P_x(dx) + \int_x\inC' P(argmax_j Q(j|x) |x) P_x(dx).

위의 식을 보면, 만약 phi가 대부분의 x에서 order-correct하다면, aggregation은 optimal할 것이다.

반면에 numerical한 case와 다르게, phi가 order-correct하지 않다면 aggregation은 오히려 worse해질 것 이다.(numerical case의 경우, phi의 variability가 큰 경우에는 phi target이 안맞아도 좋아짐..)

numerical, categorical 공통적으로 phi가 unstable한 경우 bagging이 잘 맞는다. 

categorical에서 phi가 stable하다는 것은 majority한 class가 dominant하다는 뜻이고 이는 그냥 써도 performance가 괜찮다는 의미이다. 오히려, bagging으로 이런 관계 자체가 변할 수도 있다.

phi가 unstable하다는 것은 majority class나 다른 class나 비슷하게 발생한다는 의미로 이러한 경우 bagging으로 만든 애가 더 나을 가능성이 커지게 된다. 

