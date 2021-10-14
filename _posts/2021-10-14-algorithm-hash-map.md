---
layout: post
title:  "[알고리즘] 2019 KAKAO BLIND RECRUITMENT 오픈채팅방 💬"
categories: [Algorithm]
---

## 문제 및 풀이 설명
 * 이번 문제는 생각보다 쉽게 풀려서 뭔가 당황스러웠다. 2단계도 난이도가 어려운건 확 어렵고 아닌건 쉬운 그런 느낌인가보다..
 일단 들었던 생각은 `HashMap`을 사용해서 풀어야 겠다는 생각이 바로 들었고, 이를 적용시켜서 한 줄 두 줄 작성해나가다 보니 재미있는 문제였다.

 * `"Enter uid1234 Muzi", "Enter uid4567 Prodo", "Leave uid1234", "Enter uid1234 Prodo", "Change uid4567 Ryan"` 이러한 형태의 문자열이 주어지는데 
 공백을 기준으로 항상 첫 번째 문자열은 사용자가 들어오고 나간 기록을 나타내고, 두 번째는 해당 사용자를 식별하기 위한 사용자 고유의 아이디가 제공된다.
 문자열의 마지막에 위치한 문자열은 사용자의 닉네임이 되고, 이는 중복을 허용한다. 중복을 허용하기 때문에 각 사용자를 구별 할 때는 두 번째 문자열인 사용자 고유의 아이디로 사용자를 식별한다.
 또한 사용자가 채팅방을 나간 기록인 __Leave__ 뒤에는 사용자 고유의 아이디만 존재한다. 즉 다른 문자열들과 달리 공백을 기준으로 두 개의 문자열로 이루어진 형태이다.
 `Enter` `Leave` `Change` 각각의 이벤트에 대한 로직들을 for문 안에서 조건문으로 분기 시켜 주었다.

 * 사용자가 입장 했을 때 사용자 고유의 아이디 값을 이용하여 해시테이블 내에서 이미 값이 존재하는 중복된 키 값이 존재 한다면 중복되지 않도록 처리 시켜주고,
 처음 입장한 사용자라면 해시테이블에 값을 넣어준다. 복잡해 보이지만 이 모든 로직은 `put(key, value);`로 해결된다.
 
 * 사용자가 채팅방을 나갔을 때는 해시테이블의 값을 지워줘야 하나 생각이 들겠지만, 어차피 다시 들어왔을 때 사용자 고유의 아이디로 판별을 해야하니 채팅방을 나갔다는 문자열만 출력 해 주면 된다고 판단했다.

 * 사용자가 채팅방을 나간 후 닉네임을 변경하고 들어왔을 때는 중복되는 값을 확인하고 해시테이블 내의 키 값을 찾아 교체를 해 주어야 한다. `replace(key, value);`를 사용하였다.
 한 가지 더 적자면, 굳이 `replace`가 아니고 `put`을 그대로 사용해도 동일한 로직이 적용되기 때문에 상관 없을 것 같다. 하지만 명칭 상 `replace`가 더 어울리기에 적용했다.

 * 이렇게 각각의 상황에서 어떤 로직이 들어가야 하나 정리 해 보았다. 한 가지 추가 된 사항은 이렇게 정리된 로직들이 돌면서 먼저 하나의 완성된 해시 테이블을 완성하고
 이 반복문이 종료 된 이후에 다시 다른 반복문으로 넘어가서 완성된 해시테이블 값을 가지고 사용자가 들왔을 경우, 나갔을 경우에는 메세지를 출력 해주고 사용자가 나간 후 이름을 바꿨을 때는 아무런 메세지를 출력 해 주지 않는다는 로직을 수행한다. 그렇게 각각의 저장 된 이벤트 들을 출력 해 주는 문자열도 존재해야 하기 때문에 `j`라는 변수를 추가하여 메세지를 출력 해 줄 상황에서는 이벤트에 맞는 로직을 수행하고 나중에 `HashMap`을 반복해서 돌려줄 만큼의 `j`값을 1씩 증가시켜 준다. 

## Code
```java
import java.util.*;
class Solution {
    public static String[] solution(String[] record) {

        int k = 0;
        int j = 0;
        int cnt = 0;
        
        HashMap<String, String> user_name = new HashMap<String, String>();

        for (int i = 0; i < record.length; i++) {
            String[] temp = record[i].split(" ");
            if (temp[0].equals("Enter")) { // 중복되는 값 없게 키 값이 이미 있으면 교체
                user_name.put(temp[1], temp[2]);
                j++; // 출력이 필요한 만큼 공간을 만들어 주어야 하므로 1씩 증가
            } else if (temp[0].equals("Leave")) { // 나갔다는 것을 출력은 해 줘야해
                j++; // 출력이 필요한 만큼 공간을 만들어 주어야 하므로 1씩 증가
            } else if (temp[0].equals("Change")) {
                user_name.replace(temp[1], temp[2]); // 얘는 바꾸기만 하고
                // change는 출력 해 줄 필요 없이 해당하는 로직만 수행하여 해시테이블만 갱신시키는 역할
            }
        }
        // j가 증가 된 만큼 메세지를 넣어놓을 배열이 만들어졌을 것이고
        String[] answer = new String[j];

        // 완성된 해시테이블로 출력 작업만 수행 해주면 끝
        for (; k < record.length; k++) {
            String[] temp = record[k].split(" ");
            if (temp[0].equals("Enter")) {
                answer[cnt++] = user_name.get(temp[1]) + "님이 들어왔습니다.";
            } else if (temp[0].equals("Leave")) {
                answer[cnt++] = user_name.get(temp[1]) + "님이 나갔습니다.";
            }
        }
        return answer;
    }
}
```

## 실행 결과
![backpressure](/img/10-14-algorithm/result.png)