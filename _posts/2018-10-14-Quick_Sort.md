# 퀵 정렬(Quick Sort)

- pivot point에 대해서 가장 왼쪽을 left 오른쪽을 right포인트 값으로 잡아 준 후 left>pivot / right<pivoit이 되도록 포인트를 이동하는 식으로 반복 크기가 조건에 맞지 않을 시 포인트를 옮겨주다가 right left가 모두 만났을 때 다음 포인트로 pivot 을 옮겨주는 방식.

- 분할 정복(Divide and Conquer)을 이용하여 정렬을 수행하는 알고리즘
- Pivot이라는 값을 기준을 정함. 보통 맨 앞이나 맨 뒤, 혹은 배열의 중간값이나 랜덤값으로 정함.
- Pivot을 기준으로 작은 값은 왼쪽, 큰 값은 오른쪽으로 옮기는 식의 정렬. 이를 반복하여 분할된 배열의 크기가 1인 경우 모든 배열이 정렬된 것.



## **순서**

1. Pivot point로 설정할 배열의 값 하나를 정함.
2. 가장 왼쪽과 가장 오른쪽 배열의 인덱스를 저장하는 left, right 변수 초기화.
3. right부터 비교 진행. right가 left보다 클 때만 반복하며, 비교한 배열값이 pivot보다 크면 right를 하나 감소시키고 비교를 반복. pivot보다 작으면 left를 하나 증가시키고 비교를 반복.
4. 이후, left 비교 진행. right가 left보다 클 때만 반복하며, 비교한 배열값이 pivot보다 작으면 left를 하나 증가시키고 비교를 반복. pivot보다 큰 배열값을 찾으면 반복 중지.
5. left 인덱스 값과 right 인덱스 값을 바꿔줌.
6. 3~5 과정을 left<right가 만족할 때까지 반복.
7. 과정이 끝나면 left값과 pivot 값을 바꿔줌
8. 맨 왼쪽부터 left - 1 까지, left + 1부터 맨 오른쪽까지 나눠 퀵 정렬 반복



## 시간복잡도

> 퀵 정렬의 시간복잡도는 O(NlogN)

- 배열의 크기 N만큼 비교하며, 총 분할 깊이인 logN만큼 진행 비교한다.
- 최악의 경우는 배열이 이미 정렬되어 있는 경우. 이 경우의 시간복잡도는 O(N^2)
  이를 방지하기 위해 pivot을 전체 배열값중 중간값이나 랜덤값으로 사용.
- 최악의 경우 합병 정렬보다 느린 알고리즘이지만, 최악의 경우가 발생하기 쉽지 않음.
- 일반적으로 합병 정렬보다 20% 이상 빠름.



## 공간복잡도

> **공간복잡도 O(n)**



## 코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

# define MAX_SIZE 25
# define RAND_RANGE 100

////// 전역변수 선언 
clock_t second = 0;

void quick_sort(int *data, int start, int end){

    if(start >= end){
        // 원소가 1개인 경우
        return; 
    }
    
    int pivot = start;
    int i = pivot + 1; // 왼쪽 출발 지점 
    int j = end; // 오른쪽 출발 지점
    int temp;
    
    while(i <= j){
        // 포인터가 엇갈릴때까지 반복
        while(i <= end && data[i] <= data[pivot]){
            i++;
        }
        while(j > start && data[j] >= data[pivot]){
            j--;
        }
        
        if(i > j){
            // 엇갈림
            temp = data[j];
            data[j] = data[pivot];
            data[pivot] = temp;
        }else{
            // i번째와 j번째를 스왑
            temp = data[i];
            data[i] = data[j];
            data[j] = temp;
        }
    } 
    
    // 분할 계산
    quick_sort(data, start, j - 1);
    quick_sort(data, j + 1, end);
}

int main() {
	second = (int)clock();
	int quickArray[10] = {4, 1, 2, 3, 9, 7, 8, 6, 10, 5};
	
	printf("Quick Sort\n");
	printf("정렬 전: ");	
	for(int i=0; i<10; i++){
        printf("%d ", quickArray[i]);
    }
    
	quick_sort(quickArray, 0, 9);
	
	printf("\n정렬 후: ");	
	for(int i=0; i<10; i++){
        printf("%d ", quickArray[i]);
    }
    	
	printf("\n데이터 10개의 정렬 수행시간: %0.4f초\n", (float)(clock() - second) /CLOCKS_PER_SEC);   //요기까지 걸린 시간은?

	return 0;
}
```



## 결과

![bubble_sort](https://i.imgur.com/TEWgDem.png) {:.alignleft}