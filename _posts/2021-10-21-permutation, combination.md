---
layout: post
title: "Java (중복)순열, (중복)조합 구현"
date: "2021-10-21 21:25:32"
categories: [알고리즘]
tags: [알고리즘]
---

C++이나 파이썬은 표준 라이브러리에 순열, 조합 함수가 존재하므로 바로 선언해서 사용하면 되지만, 자바에는 존재하지 않기 때문에 구조를 대충 외워놓으면 알고리즘 문제를 풀 때 시간 단축에 많은 도움이 된다.

중복 순열, 중복 조합은 기존의 순열, 조합 소스에서 이전 숫자 중복 방문을 체크하는 visited 구문만 제외시켜주면 된다.  

DFS로 구현

### 순열

```java
public class Permutation {
    public static void main(String[] args) {
        boolean[] visited = new boolean[5];
        permutation(5, 3, 0, visited, "");
    }

    static void permutation(int n, int r, int depth, boolean[] visited, String answer) {
        if(r == depth) {
            System.out.println(answer);
            return;
        }

        for(int i = 0; i < n; i++) {
            if(!visited[i]) {
                visited[i] = true;
                permutation(n, r, depth + 1, visited, answer + Integer.toString(i + 1));
                visited[i] = false;
            }
        }

    }
}
```



### 중복 순열

```java
public class PermutationWithRepetition {
    public static void main(String[] args) {
        permutationWithRepetition(5, 3, 0, "");
    }

    static void permutationWithRepetition(int n, int r, int depth, String answer) {
        if(r == depth) {
            System.out.println(answer);
            return;
        }

        for(int i = 0; i < n; i++) {
            permutationWithRepetition(n, r, depth + 1, answer + Integer.toString(i + 1));
        }
    }
}
```



### 조합

```java
public class Combination {
    public static void main(String[] args) {
        boolean[] visited = new boolean[100];
        int[] answer = new int[2];
        combination(100, 2, 0, 0, visited, answer);
    }

    static void combination(int n, int r, int depth, int start, boolean[] visited, int[] answer) {
        if(r == depth) {
            for(int i = 0; i < 2; i++) {
                System.out.print(answer[i] + " ");
            }
            System.out.println();
            return;
        }

        for(int i = start; i < n; i++) {
            if(!visited[i]) {
                visited[i] = true;
                answer[depth] = i + 1;
                combination(n, r, depth + 1, i + 1, visited, answer);
                visited[i] = false;
                answer[depth] = 0;
            }
        }
    }
}
```



### 중복 조합

```java
public class CombinationWithRepetition {
    public static void main(String[] args) {
        combinationWithRepetition(5, 2, 0, 0, "");
    }

    static void combinationWithRepetition(int n, int r, int depth, int start, String answer) {
        if(r == depth) {
            System.out.println(answer);
            return;
        }

        for(int i = start; i < n; i++) {
            combinationWithRepetition(n, r, depth + 1, i, answer + Integer.toString(i + 1));
        }
    }
}
```