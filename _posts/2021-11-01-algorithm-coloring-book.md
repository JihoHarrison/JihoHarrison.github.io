---
layout: post
title: "[알고리즘] 2017 카카오코드 예선 카카오 컬러링북 📕"
date: 2021-11-01 18:34:10 +0700
categories: [Algorithm]
---

## 문제 설명
 * __BFS__ 로 접근해야 하는 문제이다.

 * __Queue__ 를 사용하기 위해서 전역 변수로 활용하였고 __Node 클래스__ 를 생성하여 현재 좌표의 위치와 상, 하, 좌, 우의 위치를 비교할 수 있게 구현하였다.

## bfs()
 * __bfs()__ 메서드는 매개변수로 현재 위치 값(x, y) 그리고 탐색에 필요한 배열의 총 크기(m, n), 마지막으로 탐색을 진행 할 2차원 배열을 받는다(graph).

 * __x좌표__ 상에서 1씩 증감하며 좌, 우의 위치와 비교할 수 있는 전역변수 dx를 사용했다.

 * 마찬가지로 __y좌표__ 상에서 1씩 증감하며 상, 하의 위치와 현재 위치를 비교할 수 있는 전역변수 dy를 사용했다.

 * 해당 __Node__ 를 방문했는지의 여부는 역시 전역으로 선언했던 Boolean 형태의 2차원 배열인 __visited__ 를 사용했다.

 * __dx, dy__ 는 2차원 배열의 상, 하, 좌, 우 4개의 위치와 현재 좌표가 비교 될 것이고,
 비교 되기 전에 전체 배열의 크기의 좌표값을 넘어가는지, 탐색하려는 좌표가 0보다 작아지는지의 조건을 비교하여 배열 범위가 벗어나지 않도록 한다.

 * 조건을 만족했다면 for문 4번을 돌며 상, 하, 좌, 우 값을 비교하게 되는데, 탐색하려는 인덱스의 값이 같고
 해당 인덱스를 방문했는지의 여부도 검사 후 아직 방문하지 않았다면 __visited__ 의 해당 인덱스의 값에 true를 넣어줌으로써 방문을 완료했다는 표시를 한다.
 그리고 나서 전역으로 선언된 cnt의 값도 1씩 증가시키며 해당 인덱스의 크기를 증가시킨다.

 * __q.offer(new Node(nx, ny));__ 는 값이 같고 방문하지 않은 상, 하, 좌, 우가 연결된 위치의 인덱스를 __nx, ny__ 에 넣어 __LinkedList__ 의 속성을 이용 한 것을 볼 수 있다.

## solution()
 * __solution()__ 메서드는 문제에서 주어진대로 배열의 크기(m, n), 탐색을 수행 할 배열(picture)의 크기를 받는다.

 * __bfs()__ 메서드와 다른 점은 방문 여부를 표시하는 __visited__ 요소를 판별 할 때 __nx, ny__ 값이 아닌 자기 자신의 방문 여부를 검사한다.
 이미 방문 한 상태라면 __bfs__ 로 탐색이 수행 되고 __cnt__ 값이 증가 한 상태이니 아무것도 수행하지 않는다.
 1만큼 떨어진 값, 2만큼 떨어진 값 처럼 하나의 값이 여러 위치에 걸쳐 연결되어있는 상태라면 상, 하, 좌, 우 하나씩만 비교하면서 같은 값인지,
 방문을 했었는지 등을 판별하여 또 그 값의 상, 하, 좌, 우 값을 비교하는 식으로 동작한다.

 * cnt의 경우에는 __bfs()__ 메서드가 한 번씩 수행되고 끝날 때 마다 1로 초기화를 진행 해 준다.
 1로 초기화를 해 주는 이유는 __picture__ 인덱스 값이 0보다 크고, __visited__ 인덱스를 방문하지 않았을 경우 최소한 한 개의 영역이 존재하기 때문이다.


## 구현 코드

 ```java

public class ColoringBook {
   static boolean[][] visited;
   static int cnt = 0;
   static int[] dx = {1, -1, 0, 0,};
   static int[] dy = {0, 0, 1, -1};
   static Queue<Node> q = new LinkedList<Node>();

   static class Node {
      int x;
      int y;

      public Node(int x, int y) {
          this.x = x;
          this.y = y;
      }
  }

  public static void main(String[] args) {
      int [][] picture = { {1, 1, 1, 0}, {1, 2, 2, 0}, {1, 0, 0, 1}, {0, 0, 0, 1}, {0, 0, 0, 3}, {0, 0, 0, 3} };
      solution(6,4, picture);
  }

  public static int[] solution(int m, int n, int[][] picture) {
    int numberOfArea = 0;
    int maxSizeOfOneArea = 0;
    visited = new boolean[m][n];

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if(picture[i][j] > 0 && !visited[i][j]){
                cnt = 1;
                bfs(i, j, m, n, picture);
                numberOfArea++;
                if(cnt > maxSizeOfOneArea){
                    maxSizeOfOneArea = cnt;
                }
            }
        }
    }

    int[] answer = new int[2];
    answer[0] = numberOfArea;
    answer[1] = maxSizeOfOneArea;
    return answer;
  }

 public static void bfs(int x, int y, int m, int n, int[][] graph) {
    visited[x][y] = true;
    q.offer(new Node(x, y));

    while (!q.isEmpty()) {
      Node now = q.poll();
        for (int i = 0; i < 4; i++) {
            int nx = now.x + dx[i];
            int ny = now.y + dy[i];
            if (0 <= nx && nx < m && ny >= 0 && ny < n) {
                if (graph[nx][ny] == graph[x][y] && !visited[nx][ny]) {
                    visited[nx][ny] = true;
                    q.offer(new Node(nx, ny));
                    // cnt는 면적 계산
                    cnt++;
                }
            }
        }
    }
 }
}

 ```
