# Fractional knapsack

부분 배낭 문제는 n개의 물건이 있고, 각 물건은 무게와 가치를 가지고 있을 때, 최대의 가치를 갖도록 한정된 용량의 배낭에 넣을 물건들을 정하는 문제이다.

- 입력 : n개의 물건과 각 물건의 무게 `weight[]`와 가치 `value[]`, 배낭의 용량`capacity`
- 출력 : 배낭에 담은 물건 리스트 `L[]`와 배낭에 담은 물건의 가치 합`total_value`

------

### 주어진 문제

![image](https://user-images.githubusercontent.com/80511341/114664600-e58dc380-9d36-11eb-8cce-92cd2ebd8a51.png)

● 다음 그림과 같은 4개의 금속 분말이 있고 배낭의 최대 용량은 40g이다.

------

## 그리디 알고리즘으로 해결

- n개의 물건이 있고, 각 물건은 무게와 가치를 가지고 있음
- 배낭이 한정된 무게의 물건들을 담을 수 있을 때, **욕심을 내어** ( = 물건을 쪼갤 수 있다) 최대의 가치를 갖도록 배낭에 넣을 물건을 정함

## 작동 과정

#### STEP 1

각 물건에 대해 단위 무게 당 가치를 계산한다.

```java
double perValue[] = new double[value.length];   //단위 무게 당 가치(value/weight)

for(int i = 0; i < perValue.length; i++){       //단위 무게 당 가치를 perValue 배열에 저장
            perValue[i] = (double)value[i]/(double)weight[i];
            priority[i] = i;
        }
```

| 물건 | 단위 그램 당 가치  |
| :--: | :----------------: |
| 백금 | 6만원 (60만원/10g) |
|  금  | 5만원 (75만원/15g) |
|  은  | 4천원 (10만원/25g) |
| 주석 | 1천원 (5만원/50g)  |



#### STEP 2

물건들을 단위 무게 당 가치를 기준으로 내림차순 정렬하고, 가방 속 물건의 리스트  `L`, 물건의 무게 `total_weight`, 물건의 가치 `total_value`를 초기화한다.

```java
for(int i = 0; i < perValue.length; i++)
            for(int j = 0; j < perValue.length; j++)        
                if(perValue[i] > perValue[j]){
                    temp1 = perValue[i];
                    perValue[i] = perValue[j];
                    perValue[j] = temp1;

                    temp2 = priority[i];
                    priority[i] = priority[j];
                    priority[j] = temp2;
                }                                          
               
double total_value = 0; 				//가방 속 물건의 가치
double total_weight = 0; 				//가방 무게   
String L[] = new String[value.length];  //가방 속 물건 이름
```

=> weight[priority]가 단위 그램당 가치가 가장 높은 백금을 기준으로 내림차순 정렬된다.



#### STEP 3

while 루프문을 통해 단위 무게당 가치가 가장 큰 물건을 가져다 배낭에 담는다.

```java
while(total_weight + weight[priority[x]] <= capacity){
            L[x] = list[priority[x]];
            total_weight = total_weight + weight[priority[x]];
            total_value = total_value + value[priority[x]];
            x++;                                            
        }
```

=> 만일 가져온 물건을 배낭에 넣었을 때 배낭의 용량이 초과되면, 가져온 물건을 통째로 담을 수 없게 되어 루프가 종료된다.



#### STEP 4

현재까지 배낭에 담은 물건들의 무게`total_weight`가 배낭의 용량 `capacity`보다 작으면 물건 x를 배낭의 용량에서 물건의 무게를 뺀 만큼 배낭에 담고, 담은 만큼의 가치를 증가시킨다.

```java
 if(capacity - total_weight > 0){ 	// 배낭에 물건을 부분적으로 담을 여유가 있으면
            L[x] = list[priority[x]];
            total_value = total_value + perValue[x] * (capacity - total_weight);
            x++;
        }		//우선순위의 숫자가 큰 것이 먼저 실행되도록 설정
```



### 전체 코드

```java
import java.util.Scanner;

public class Main {
    private static void Fractional_Knapsack(int capacity, String[] list, int[] value, int[] weight){
        double total_value = 0;                             //가방 속 물건의 가치
        double total_weight = 0;                            //가방 무게
        double perValue[] = new double[value.length];       //단위 무게 당 가치(value/weight)
        int priority[] = new int[value.length];             //인덱스
        String L[] = new String[value.length];              //가방 속 물건 이름
        int x = 0;

        for(int i = 0; i < perValue.length; i++){           //단위 무게 당 가치를 perValue 배열에 저장
            perValue[i] = (double)value[i]/(double)weight[i];
            priority[i] = i;
        }

        double temp1 = 0.0;
        int temp2 = 0;

        for(int i = 0; i < perValue.length; i++)
            for(int j = 0; j < perValue.length; j++)		//perValue 값이 큰 순으로 정렬(내림차순)
                if(perValue[i] > perValue[j]){
                    temp1 = perValue[i];
                    perValue[i] = perValue[j];
                    perValue[j] = temp1;

                    temp2 = priority[i];
                    priority[i] = priority[j];
                    priority[j] = temp2;
                }                             	//기존 값의 인덱스를 유지하기 위해 priority 배열도 같이 정렬

        while(total_weight + weight[priority[x]] <= capacity){
            L[x] = list[priority[x]];
            total_weight = total_weight + weight[priority[x]];
            total_value = total_value + value[priority[x]];
            x++;                               	//x를 S에서 제거
        }

        if(capacity - total_weight > 0){       	// 배낭에 물건을 부분적으로 담을 여유가 있으면
            L[x] = list[priority[x]];
            total_value = total_value + perValue[x] * (capacity - total_weight);
            x++;
        }                                      	//우선순위의 숫자가 큰 것이 먼저 실행되도록 설정

        System.out.println("배낭에 담은 물건 리스트는 ");
        for(int i = 0; i < x; i++)
            System.out.println(L[i]);

        System.out.println("이고, 물건 가치의 합은 " + total_value + "이다.");
    }

    public static void main(String[] args){
        Scanner scanner = new Scanner(System.in);

        int n = scanner.nextInt();          	//물건의 개수
        int capacity = scanner.nextInt();  	 	//배낭의 용량
        String[] list = new String[n];
        int[] weight = new int[n];
        int[] value = new int[n];

        for(int i = 0; i < n; i++){
            list[i] = scanner.next();
            weight[i] = scanner.nextInt();     //물건의 무게
            value[i] = scanner.nextInt();      //물건의 가치
        }

        Fractional_Knapsack(capacity, list, value, weight);
    }
}
```

**코드 실행 결과**

![image](https://user-images.githubusercontent.com/80511341/114668804-1d4b3a00-9d3c-11eb-91cd-30cd5d752cd6.png)

------

## 시간 복잡도

> n개의 물건 단위 무게 당 가치를 계산 - O(n) 
>
> 물건의 단위 무게 당 가치를 내림차순으로 정렬 - O(nlogn) 
>
> while 루프의 수행 - n * O(1) 
>
> 배낭의 용량 - 물건들의 무게 > 0인 상황에서 부족한 용량만큼 배낭에 추가 - O(1)

STEP 1에서 단위 무게 당 가치를 계산하는 데 걸리는 시간은 O(n)이 걸리고 STEP 2에서 단위 무게 당 가치에 대해서 정렬하는 데 O(nlogn) 만큼의 시간이 걸린다. 또한 STEP 3에서 while 문을 수행하는 데 걸리는 시간은 O(1)이 필요하고, STEP 4에서 if 문에서 각각 O(1) 만큼 n 번 반복하므로 O(1) * n이 걸린다. 따라서 알고리즘의 시간 복잡도는 다음과 같이 표현할 수 있다.

***O(n) + O(nlogn) + n* * *O(1) + O(1) = O(nlogn)***

**시간 복잡도 그래프**

<img width="723" alt="그래프" src="https://user-images.githubusercontent.com/80919306/114660971-27b40680-9d31-11eb-8b0d-bb3851ba3b58.PNG">
