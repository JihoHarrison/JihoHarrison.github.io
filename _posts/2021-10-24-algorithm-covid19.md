---
layout: post
title:  "[알고리즘] 2021 카카오 채용연계형 인턴십 거리두기 확인하기 😷"
categories: [Algorithm]
---

## 접근 방법
 * 이번 문제는 처음에 접근할 때 너무 어렵게 접근 했던 것 같다. 결국에는 규칙을 찾아내고, 구현을 시도했지만 약 4~5개의 테스트 케이스만 통과하고 다른 테스트 케이스들은 통과하지 못했다. 여러가지 문제점들이 존재했다. 탐색 알고리즘 즉, 재귀를 사용하지 않고 단순히 기본적인 반복문으로 접근을 하려다 보니 모든 조건들을 알맞게 판별 한 후 마지막 __answer__ 에 답을 넣어주는 부분에서 문제가 생겼다.

 * 리턴되는 answer 자체의 크기는 5인데 for문을 몇 겹씩 겹치다 보니 5보다 초과되는 인덱스에 1 또는 0이 들어가면서 에러를 뱉어냈고,
 __1 또는 0__ 이 answer에 들어가는 순간 메서드 자체를 리턴시키자니 한 개의 메서드에 구현을 하고있던 터라 반복문 중간에 __return__ 을 걸어주는 것도 불가능 해 보였다.

 * 약 5일간 사투를 벌인 끝에 결국 해결하지 못한 채로 팀원들과의 스터디 후 답안을 찾아보았다. 이게 왠걸.. __BFS__ 를 적용해야했고, 굳이 적용시키지 않더라도 구현되는 개념을 알고 있어야지 기본적인 반복문만 이용하더라도 구현이 가능했다.

## 문제 설명 및 풀이 방법
[["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]

---
 * 다음과 같은 2차원 문자열 배열이 주어진다. 최상위 배열 각 요소들 5개는 대기실을 표현하고, 그 요소 안의 하나의 1차원 배열들은 면접 참여자들의 위치를 나타낸다. __맨해튼 거리__ 를 이용하라고 나와있는데, 처음에는 `Math.abs`를 이용하여 절댓값을 판별해야 하는줄 알았지만 __Math 클래스__ 를 사용하지 않을 수 있는 규칙을 찾아내었다. 참가자들의 위치는 맨해튼 거리 __2__ 밖에 위치해야 하기도 하지만 만약 맨해튼 거리가 대각선으로 2보다 작고 파티션을 사이에 두고 앉아있다면 괜찮다. 하지만 둘 사이의 거리가 __2__ 이내인데 파티션이 그 경로에 하나라도 존재하지 않을 경우 거리두기를 지키지 못 한 것으로 판단되어 __answer__ 값에는 __0__ 이 들어간다.

  * 이 말을 내가 찾은 규칙으로 풀어보자면, 해당하는 인덱스가 __"P"__ 일 경우  상하좌우의 값을 비교한다.
  만약 그 위치에 __"P"__ 가 또 존재한다면, 이는 참가자들이 붙어서 앉아있는 경우 이므로 거리두기를 지키지 못한 것으로 판단한다.

  * 인덱스가 __"O"__ 일 경우, 역시 상하좌우의 값을 비교한다. 자기 자신의 인덱스가 __"O"__ 이고 그 주변 상하좌우에 __"P"__ 가  두 개 이상 존재한다면, 이는 파티션을 사이에 두고 앉은 경우가 아니므로 거리두기를 지키지 못한 것으로 판단한다.

  * 인덱스가 __"X"__ 일 경우에는 이미 해당하는 인덱스가 사이에 존재하기 때문에 고려하지 않는다.

## 많이 이상한 첫 번째 풀이
```java
public static int[] solution(String[][] places) {
    int[] answer = new int[5];
    int ans = 0;
    for (int i = 0; i < places.length; i++) {
        String[][] cusTable = new String[5][5];
        String[][] newCusTable = new String[5][5];
        for (int j = 0; j < places[i].length; j++) {
            cusTable[i] = places[i][j].split("");
            for (int k = 0; k < 5; k++) { // 여기가 각 문자열의 요소 반복문
                newCusTable[j][k] = cusTable[i][k];
            }
        }
        int cnt = 0;
        for (int l = 0; l < newCusTable.length; l++) {
            for (int m = 0; m < newCusTable[i].length; m++) {
                cnt = 0;
                if (newCusTable[l][m].equals("X")) continue;
                if (newCusTable[l][m].equals("P")) {
                    cnt++;
                }
                if (l > 0) {
                    if (newCusTable[l - 1][m].equals("P")) {
                        cnt++;
                    } else {
                        cnt += 0;
                    }
                }
                if (l < newCusTable.length - 1) {
                    if (newCusTable[l + 1][m].equals("P")) {
                        cnt++;
                   } else {
                        cnt += 0;
                   }
               }
               if (m > 0) {
                    if (newCusTable[l][m - 1].equals("P")) {
                        cnt++;
                   } else {
                        cnt += 0;
                   }
               }
               if (m < newCusTable[l].length - 1) {
                    if (newCusTable[l][m + 1].equals("P")) {
                        cnt++;
                    } else {
                        cnt += 0;
                    }
                }
           }
        }
            if (cnt >= 2) {
                answer[ans++] = 0;
            } else {
                answer[ans++] = 1;
            }
        }
    return answer;
    }
}
```
 * 내가 봐도 정말 답이 없는 풀이 방법이다. for문을 저렇게 겹쳐서 구현 할 생각을 한 것 부터 너무 생각없이 접근했다.
 예시 테스트 케이스를 포함한 4~5개의 테스트 케이스를 통과했지만 대다수를 통과하지 못하고 14점 정도를 받은 코드이다.
 __newCusTable__ 이라는 새로운 배열에 값을 복사한 후 비교를 한 이유는 이미 만들어지지 않은 배열에 대해서 연산을 수행 하려하니
 __NullPointerException__ 이 발생해서 해당 이슈를 해결하기 위한 임시방편의 조치이다..
 
 * 정작 문제가 생겼던 부분은 __answer__ 에 값을 넣어주는 부분이다.
```java
if (cnt >= 2) {
    answer[ans++] = 0;
} else {
    answer[ans++] = 1;
}
```
 * 이 부분을 보면 값이 잘 넣어지고 있는 듯 보이지만 for문이 5번을 다 돌고난 후 다시 겹쳐진 __for__ 문이 돌때 또 영향을 받게 된다.
 * __"P"__ 의 값을 가지고 있는 배열에서는 항상 1의 값을 가지게 된다. __"P"__ 의 값이 하나도 존재하지 않는 경우, 이 경우에만 __cnt__ 값이 커지지 않아 __0__ 이라는 값을 가지게 된다.

 ## 수정된 해결방법
 ```java
public static int[] solution(String[][] places) {
    int[] answer = new int[5];

    for (int i = 0; i < places.length; i++) {
        answer[i] = solutionPractice(places[i]);
    }

    return answer;
}

public static int solutionPractice(String[] places) {
    
    String[][] table = makeTable(places);

    for (int i = 0; i < table.length; i++) {
        for (int j = 0; j < table[i].length; j++) {
            int cnt = 0;
            if (table[i][j].equals("X")) continue;
            if (table[i][j].equals("P")) {
                cnt++;
            }
            if (i > 0) {
                if (table[i - 1][j].equals("P")) {
                    cnt++;
                }
            }
            if (i < table.length - 1) {
                if (table[i + 1][j].equals("P")) {
                    cnt++;
                }
            }
            if (j > 0) {
                if (table[i][j - 1].equals("P")) {
                    cnt++;
                }
            }
            if (j < table[i].length - 1) {
                if (table[i][j + 1].equals("P")) {
                    cnt++;
                } else {
                    cnt += 0;
                }
            }
            if (cnt >= 2) {
                return 0;
            }
        }
    }
    return 1;
}

public static String[][] makeTable(String[] places) {
    String[][] newCusTable = new String[5][5];
    for (int i = 0; i < places.length; i++) {
        String[] str = places[i].split("");
        for (int j = 0; j < str.length; j++) {
            newCusTable[i][j] = str[j];
        }
    }
    return newCusTable;
}
 ```
 * 맨 아래 __makeTable__ 메서드에서는 각 배열을 한 줄씩 연산하는 __places__ 배열을 매개변수로 갖는다.
 이를 이용해서 한 번에 하나의 대기실에 대해서만 연산을 수행하고 있다.

 * __solutionPractice__ 에서는 아래서 만든 __makeTable__ 을 이용해서 각 요소들의 값을 상하좌우로 비교하며 __cnt__ 가  2보다 큰지 확인한다.
 cnt가 2 이상이라는 것은 참가자들의 거리두기가 잘 지켜지지 않는다는 의미이고, 이는 __answer__ 에 넣어줄 값을 결정해주고 __return__ 된다.이렇게 되면 해당하는 대기실에 대해서는 return이 수행되기 때문에 1 또는 0의 값만 넣어주고 더 이상 __answer__ 값에 영향을 주지 않게 된다. 위에서 __answer__ 에 값을 넣어주는 부분에 대한 문제를 __return__ 을 통해서 해결하게 된 것이다.

 * 맨 위의 __solution__ 을 보면, 이 메서드는 결국 각각의 대기실만 차례대로 __solutionPractice__ 에 넣어주면 되기 때문에 for문을 5번만 돌려주면 된다

## 결론

  _탐색 알고리즘을 더 깊이있게 공부하자..._