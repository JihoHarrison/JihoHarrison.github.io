---
layout: post
title: "[알고리즘] 2017 카카오코드 본선 단체사진 찍기 📷"
date: 2021-11-05 18:34:10 +0700
categories: [Algorithm]
---

## 문제 설명

 * 하나의 문자열 배열에 대한 모든 경우의 수를 구하고 두 가지의 조건을 만족하는 값이 있는지 조건 판별해야 한다.

 * 모든 경우의 수를 구하고 그 중에서 만족하는 조건이 있는지 판별해야 하기 때문에 dfs의 개념을 적용시켰다.

## dfs()

 * 재귀로 동작하는 메서드이다.

 * dfs 메서드에는 names라는 문자열을 받게 되는데 이 곳을 통해서 모든 경우의 수의 배열을 한 글자씩 받게된다. 보통 이 부분에 문자열 배열을 통해서 한 글자씩 받아온다고 설계할 수 도 있다. (내가 그랬다)
 재귀에 필요한, 값을 증가시키면서 재귀를 수행시킬 매개변수로 배열의 인덱스를 받는다고 할 수도 있다. 함수가 호출될 때 dfs 메서드는 최초로 빈 문자열을 받고, kakaoExample[i]를 통해서 총 8개의 문자열을 순서대로 가지게 된다. 이 때 계속해서 재귀를 수행하며 필요한 문자열의 갯수가 만족되면 check 메서드를 호출하여 완성된 하나의 문자열을 넣어주고, 해당 문자열에 대한 조건 판별을 시작하게 된다.

## check()

 * dfs() 메서드에서 전달받은 하나의 완성된 문자열에 대한 조건 판별을 해주는 메서드이다.

 * indexOf 연산자로 카카오 프렌즈 이름 배열에서 subString() 연산을 수행 한 각각의 요소를 가져온다.

 * 각각의 분리된 문자열 또는 문자들로 조건식에 넣어서 각 세 개의 연산별로 분리해준다.

 * 하나의 완성된 카카오 프렌즈 이름 배열로 datas 배열의 크기만큼 입려된 조건을 검사하면서 수행되기 때문에 data의 연산을 판별하는 동안 false를 만나지 않는다면 당연하게도 true가 리턴된다. 이는 곧 false가 걸리지 않은 datas의 두 가지 조건을 만족한다는 뜻이고, totalCnt를 1씩 증가시키는 조건을 만족하게 된다. 이 로직으로 dfs로 수행되는 모든 경우의 수에 대해 조건을 검사하게 된다.

## 풀이!

```java
public class TakePicture {
  public static int totalCnt = 0;
  public static String[] kakaoFriendNames = {"A", "C", "F", "J", "M", "N", "R", "T"}
  public static void main(String[] args) {
    String[] data1 = {"N~F=0", "R~T>2"};
    String[] data2 = {"M~C<2", "C~M>1"};  
    solution(2, data1);
  } 

  public static int solution(int n, String[] data) {
    boolean[] isVisited = new boolean[8];
    dfs("", isVisited, data);
    int answer = totalCnt;
    System.out.println(answer);
    return answer;
  } 
  public static void dfs(String names, boolean[] isVisited, String[] datas) {
    if (names.length() == kakaoFriendNames.length - 1) {
        if (check(names, datas)) {
            totalCnt++;
        }
        return;
      }
    for (int i = 0; i < 8; i++) {
        if (!isVisited[i]) {
            isVisited[i] = true;
            String name = names + kakaoFriendNames[i];
            dfs(name, isVisited, datas);
            isVisited[i] = false;
        }
    }
} 
public static boolean check(String searchExample, String[] datas) {
    for (int i = 0; i < datas.length; i++) {
        int sugPos = searchExample.indexOf(datas[i].substring(0, 1));
        int othPos = searchExample.indexOf(datas[i].substring(2, 3));
        String op = datas[i].substring(3, 4);
        int index = datas[i].charAt(4) - '0';
        int resultPos = Math.abs(sugPos - othPos);
        if (op.equals("=")) {
            if (!(resultPos == index + 1)) {
                return false;
            }
        } else if (op.equals(">")) {
            if (!(resultPos > index + 1)) {
                return false;
            }
        } else if (op.equals("<")) {
            if (!(resultPos < index + 1)) {
                return false;
            }
        }
    }
    return true;
  }
}
```