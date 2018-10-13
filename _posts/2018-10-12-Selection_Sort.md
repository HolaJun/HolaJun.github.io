# 선택 정렬(Selection Sort)

- 가장 원시적이고 기본적인 알고리즘
- 가장 작은 수를 찾아 맨 앞으로 보낸다.
- 간단하면서 느린 정렬에 속함. 적은 데이터를 정렬할 경우 효율적.



## **순서**

1. 주어진 값중 최소값을 찾는다.
2. 최소값을 맨 앞에 위치한 값과 교체한다
3. 맨 앞에 위치한 값을 제외한 나머지 값을 같은 방법으로 교체한다



## 시간복잡도

> 선택 정렬의 시간복잡도는 O(N^2)

- 데이터의 갯수가 N개일 때, N(N+1)/2 번의 연산을 수행함
- 가장 큰 차수인 N^2만 보고, O(N^2)라 표현함.
- 어떤 경우에든 정렬되있어도 비교횟수가 같기 때문에, 최악이나 평균시간이나 시간복잡도가 같음.
- 거품정렬(bubble sort)보다 항상 우수함.
- 삽입정렬(insetion sort)보다 효율적으로 실행됨

## 코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

# define MAX_SIZE 25
# define RAND_RANGE 100


// 난수에 매번 다른 시드를 주기 위한 함수 
void seed_init() {
	srand((unsigned int)time(NULL));
} 

// 난수 발생 함수 
void random_number(int *array, int length) {
	bool overlapCheck = false; // 원소값 중복체크 변수 
	 
	for(int i=0; i<length; i++) {
		while(true) {
			array[i] = (rand()%RAND_RANGE)+1;
			overlapCheck = false;
			
			for(int j=0; j<i; j++) {
				if(array[j] == array[i]) {
					overlapCheck = true;
					break;
				}
			}
			if(!overlapCheck) {
				break;
			}
		}	
	}	
}

// 선택 정렬 
void selection_sort(int* array, int length) {
	int i, j, min, temp;
	
	random_number(array, length);
	
	printf("정렬 전: ");
	for(i=0; i<length; i++) {
		printf("%d ", array[i]);
	}
	
	for(i=0; i<length; i++) {
		min = i;
		for(int j=i+1; j<length; j++) {
			// 최솟값 탐색 
			if(array[j] < array[min]) {
				min = j;
			}
 		}
 		temp = array[min];
 		array[min] = array[i];
 		array[i] = temp;
	} 
	
	printf("\n정렬 후: "); 
	for(i=0; i<length; i++) {
		printf("%d ", array[i]);
	}
	
}

int main() {
	clock_t second = 0;
	int data[MAX_SIZE] = {};
	int length = sizeof(data)/sizeof(int); // 배열의 크기
	seed_init();
	
	second = (int)clock();
	printf("Selection Sort\n");
	selection_sort(data, length);
	printf("\n데이터 %d개의 정렬 수행시간: %0.4f초\n", MAX_SIZE, (float)(clock() - second) /CLOCKS_PER_SEC);   //요기까지 걸린 시간은?
	
	return 0;
}
```

