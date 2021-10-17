---
layout: post
title: "Softeer [인증평가(1차) 기출] 차세대 지능형 교통시스템"
date: "2021-10-17 12:52:23"
categories: [알고리즘]
tags: [알고리즘, Softeer]
---

[문제 링크](https://softeer.ai/practice/info.do?eventIdx=1&psProblemId=580&sw_prbl_sbms_sn=23770)

### 제한시간 : C/C++(1초), Java/Python(2초) | 메모리 제한 : 256MB

>여기 교통 인프라(신호등)와 실시간 통신을 하는 자율주행차가 가로, 세로 N개의 교차로로 구성된 도로를 지나고 있다. 교차로 간 연결된 선을 도로, 도로의 교차점들을 교차로로 생각하자. 자율주행 차는 처음에 제일 왼쪽 위의 교차로로 아래쪽 방향에서 진입하고 있다. (자동차는 도로 한 칸을 가는 데는 1T가 걸린다.)
>
>![1](https://www.softeer.ai/upload/2021/06/20210625_102554745_17901.jpg)
>
>각 교차로의 신호등은 다음과 같은 12가지 상태 중 4가지를 가지고 무한히 반복하는 방식으로 운영된다.
>
>![2](https://www.softeer.ai/upload/2021/06/20210624_144916574_84532.jpg)
>
> 예를 들어 한 교차로의 신호 집합이 <3 2 6 10>과 같이 주어진 경우는 아래와 같다.
>
>![3](https://www.softeer.ai/upload/2021/06/20210624_145007558_84336.jpg)
>
>자율주행 자동차가 멈추지 않고, 시간 T 이내에 갈 수 있는 교차로의 수를 계산하라. 단, 신호가 맞지 않으면 그 교차로에는 갈 수가 없다. 처음에 제일 왼쪽 위의 교차로로 아래쪽 방향에서 진입하고 있으므로, 교차로(1,1)의 신호가 2, 10번 중 하나가 아니면 더 이상 진행을 할 수 없으므로 교차로는 단 하나[교차로(1,1)]로 계산한다.



### 입력형식

> 입력으로는 N과 시간 T가 첫 줄에 주어진다. (1 ≤ N, T ≤ 100)
>
> 다음 N2개의 줄에 각 교차로의 신호 집합이 주어진다. 신호는 항상 4개이며, 순서는 X축부터 진행을 한다.
>
> 예를 들어 N이 2인 경우
> 두 번째 줄. 교차로(1,1)의 신호
> 세 번째 줄. 교차로(2,1)의 신호
> 네 번째 줄. 교차로 (1,2)의 신호
> 마지막 줄. 교차로 (2,2)의 신호
>
> ![4](https://www.softeer.ai/upload/2021/06/20210625_102647233_05272.jpg)



### 출력형식

> 이동 경로에 있는 모든 교차로의 개수를 출력한다. 한번 갔던 교차로는 중복해서 세지 않는다.



### 입력예제

>3 3  
>2 6 12 9  
>7 1 11 6  
>6 3 5 11  
>1 1 12 9  
>3 11 8 2  
>1 7 11 9  
>4 6 2 3  
>2 4 2 4  
>6 9 2 6



### 출력예제

> 6



### 예제 설명

>`[T=0]`  
>자율주행 자동차는 처음에 제일 왼쪽 위의 교차로로 아래쪽 방향에서 진입하고 있고, 그 때 교차로 A의 신호등은 2번이므로 우회전만 가능하다.
>(이동 경로에 있는 교차로 1개 - 교차로 A)
>
>![5](https://www.softeer.ai/upload/2021/06/20210625_102704835_99550.jpg)
>
>`[T=1]`  
>교차로 B로 진입하고 있고, 그 때 교차로 B의 신호등은 1번이므로 직진과 우회전이 가능하다.
>(이동 경로에 있는 교차로 2개 - 교차로 A, 교차로 B)
>
>![6](https://www.softeer.ai/upload/2021/06/20210625_102836409_32651.jpg)
>
>`[T=2, case 1]`  
>교차로 B를 통과해서 갈 수 있는 두가지 경로(교차로 C, 교차로 E 방향) 중, 교차로 C로 진입을 하게 될 때 교차로 C의 신호등은 5번으로 직진과 좌회전 신호이지만 더 이상의 진행은 불가능 하다.
>(이동 경로에 있는 교차로 3개 - 교차로 A, 교차로 B, 교차로 C)
>
>![7](https://www.softeer.ai/upload/2021/06/20210625_102747005_79496.jpg)
>
>`[T=2, case 2]`  
>교차로 B를 통과해서 갈 수 있는 두가지 경로(교차로 C, 교차로 E 방향) 중, 교차로 E로 진입을 하게 될 때 교차로 E의 신호등은 8번으로 직진과 좌회전이 가능하다.
>(이동 경로에 있는 교차로 3개 - 교차로 A, 교차로 B, 교차로 E)
>
>![8](https://www.softeer.ai/upload/2021/06/20210625_103211871_14313.jpg)
>
>`[T=3, case 2-1]`  
>교차로 E를 통과해서 갈 수 있는 두가지 경로(교차로 F, 교차로 H 방향) 중, 교차로 F로 진입을 하게 될 때 교차로 F의 신호등은 9번으로 직진과 우회전이나 우회전만 가능하다.
>(이동 경로에 있는 교차로 4개 - 교차로 A, 교차로 B, 교차로 E, 교차로 F)
>
>![9](https://www.softeer.ai/upload/2021/06/20210625_103339663_38214.jpg)
>
>`[T=3, case 2-2]`  
>교차로 E를 통과해서 갈 수 있는 두가지 경로(교차로 F, 교차로 H 방향) 중, 교차로 H로 진입을 하게 될 때 교차로 H의 신호등은 4번으로 직진과 좌회전, 우회전이나 좌회전과 우회전만 가능하다.
>(이동 경로에 있는 교차로 4개 - 교차로 A, 교차로 B, 교차로 E, 교차로 H)
>
>![10](https://www.softeer.ai/upload/2021/06/20210625_103402015_86288.jpg)
>
>그래서 T=0~3까지 시간동안 이동경로에 있는 모든 교차로는 총 6개 이다.
>(교차로 A, 교차로 B, 교차로 C, 교차로 E, 교차로 F, 교차로 H)



## 풀이

현재 자동차가 위치한 교차로에서 신호에 따라 최대 세가지 방향(직, 좌, 우)이 가능하므로 BFS 알고리즘을 활용해서 자동차가 이동할 수 있는 경로에 대한 모든 경우의 수를 체크한다.

현재 교차로에서 다음 교차로로 진행이 가능하려면 현재 자동차의 방향과 신호의 방향이 같아야 하기 때문에 신호등 1 ~ 12번 배열 첫번째 원소에 신호의 방향을 추가하였다. 신호 방향과 자동차 방향이 같을때만 다음 교차로로 진행할 수 있다.


```java
import java.util.*;
import java.io.*;

public class CITS
{
    static class Pos {
        int y;
        int x;
        int heading[]; //자동차의 방향
        int time; //현재 시간

        public Pos(int y, int x, int[] heading, int time) {
            this.y = y;
            this.x = x;
            this.heading = heading;
            this.time = time;
        }
    }
    static int N = 0, T = 0;
    static int[][][] map;
    //신호등 첫번째 원소는 [n][n][0] 신호의 방향
    static int[][][] trafficLight = {%raw%}{
            {{0, 0}},
            {{0, 1}, {0, 1}, {1, 0}, {-1, 0}},
            {{-1, 0}, {0, 1}, {0, -1}, {-1, 0}},
            {{0, -1}, {0, -1}, {1, 0}, {-1, 0}},
            {{1, 0}, {0, 1}, {0, -1}, {1, 0}},
            {{0, 1}, {0, 1}, {-1, 0}},
            {{-1, 0}, {0, -1}, {-1, 0}},
            {{0, -1}, {0, -1}, {1, 0}},
            {{1, 0}, {0, 1}, {1, 0}},
            {{0, 1}, {0, 1}, {1, 0}},
            {{-1, 0}, {0, 1}, {-1, 0}},
            {{0, -1}, {0, -1}, {-1, 0}},
            {{1, 0}, {0, -1}, {1, 0}}
    }{%endraw%};

    public static void main(String args[])
    {
        try {
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
            String[] str = br.readLine().split(" ");
            N = Integer.parseInt(str[0]);
            T = Integer.parseInt(str[1]);
            map = new int[N][N][4];

            for(int i = 0; i < N; i++) {
                for(int j = 0; j < N; j++) {
                    str = br.readLine().split(" ");
                    for(int k = 0; k < 4; k++) {
                        map[i][j][k] = Integer.parseInt(str[k]);
                    }
                }
            }
            br.close();

            System.out.print(BFS());

        } catch(Exception e) {
            e.printStackTrace();
        }
    }

    static int BFS() {
        int[] firstDir = {-1, 0};
        boolean[][] visited = new boolean[N][N];
        Queue<Pos> queue = new LinkedList<>();
        queue.add(new Pos(0, 0, firstDir, 0)); //큐에 첫 시작점 add

        while(!queue.isEmpty()) {
            Pos pos = queue.poll();
            int curY = pos.y;
            int curX = pos.x;
            int[] curHeading = pos.heading;
            int curTime = pos.time;

            visited[curY][curX] = true; //현재 교차로 방문 처리

            if(curTime == T) continue; //현재 시간이 T라면 큐 add 중단

            int trafficIdx = map[curY][curX][curTime % 4]; //현재 신호 = 각 교차로별 신호 수가 4개이므로 (현재 시간 % 4) 연산
            if(!isCorrectDir(curHeading, trafficLight[trafficIdx][0])) continue; //현재 자동차 방향과 신호 방향 비교 
            for(int i = 1; i < trafficLight[trafficIdx].length; i++) {
                int nextY = curY + trafficLight[trafficIdx][i][0];
                int nextX = curX + trafficLight[trafficIdx][i][1];
                if(nextY < 0 || nextY >= N || nextX < 0 || nextX >= N) continue; //다음 진행 경로가 맵 범위 밖인지 체크

                queue.add(new Pos(nextY, nextX, trafficLight[trafficIdx][i], curTime + 1));
            }
        }
        int answer = 0;
        for(int i = 0; i < N; i++) {
            for(int j = 0; j < N; j++) {
                if(visited[i][j]) answer++;
            }
        }

        return answer;
    }

    static boolean isCorrectDir(int[] curDir, int[] trafficDir) {
        return Objects.deepEquals(curDir, trafficDir);
    }
}
```

