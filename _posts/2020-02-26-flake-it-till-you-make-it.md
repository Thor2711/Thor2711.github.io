---
layout: post
title: Hackerrank 문제풀이
subtitle: Excerpt from Soulshaping by Jeff Brown
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [books, test]
---
## Formining a Magic Square.

3 X 3 Magic square 만들기 문제로, 일단 magic square라는 것은 모든 row sum, col sum, diagonal sum이 같은 행렬을 의미한다. 해당 문제는 어떤 3 X 3 행렬 s가 주어질 때, s와 L1 norm이 가장 작은 magic square 간의 distance를 산출하는 문제이다.

처음에는 주어진 s에서 어떤 element를 바꾸어야 magic square를 만들 수 있는지, all case를 고려하지 않는 빠른 방법이 있는가를 생각했지만 뚜렷한 방법은 떠오르지 않았다...
결국은 3 X 3의 case가 적은 문제이기 때문에 3 X 3의 모든 magic square case를 찾아서 주어진 s와 차이를 구하는 방법이 이 문제의 의도 아닐까 싶다.

일단, 3 X 3 행렬의 경우 모든 row sum, col sum, diagonal sum은 15가 되어야 한다. 왜냐하면, 1~9까지의 합을 3으로 나누면 15이기 때문.

1~9까지의 합이 15가 되는 3개 숫자 조합은 다음과 같다.
1, 5, 9
1, 6, 8
2, 4, 9
2, 5, 8
2, 6, 7
3, 4, 8
3, 5, 7
4, 5, 6

모든 case를 고려할 때, 한가지 point는 정가운데 있어야할 원소의 경우는 위의 8개 숫자 조합 중에서 4가지 숫자 조합에 포함되어야한다.
각 숫자별 조합에 몇 번 포함되어 있는지 살펴보면,
1-2, 2-3, 3-2, 4-3, 5-4, 6-3, 7-2, 8-3, 9-2
이므로 가운데는 5가 들어가 있어야함. 또한 4개의 꼭지점에 있는 숫자는 총 3번 포함되어있어야하므로, 2,4,6,8 이 있어야한다.

그럼 가능한 모든 case는 (-는 위의 조합들을 참고하여 자동으로 채워짐) 8개이다. 밑에는 그중 4가지 case.

2 - 4
- 5 -
6 - 8,

8 - 4
- 5 -
6 - 2,

2 - 6
- 5 -
4 - 8,

8 - 6
- 5 -
4 - 2


결국 다음의 코드로 solution을 구할 수 있다.

def formingMagicSquare(s):
    all_cases = [
        [[2,9,4],
         [7,5,3],
         [6,1,8]],
        [[2,7,6],
         [9,5,1],
         [4,3,8]],
        [[8,3,4],
         [1,5,9],
         [6,7,2]],
        [[8,1,6],
         [3,5,7],
         [4,9,2]],
        [[4,9,2],
         [3,5,7],
         [8,1,6]],
        [[6,7,2],
         [1,5,9],
         [8,3,4]],
        [[4,3,8],
         [9,5,1],
         [2,7,6]],
        [[6,1,8],
         [7,5,3],
         [2,9,4]]
        ]
    
    answer = []
    for mat in all_cases:
        answer += [sum([sum([abs(mat[i][j] - s[i][j]) for j in range(len(mat[i]))]) for i in range(len(mat))])]
    
    return min(answer)
        
