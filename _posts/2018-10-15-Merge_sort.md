# 병합 정렬(Merge Sort)

- 반으로 나눈 다음 나중에 합치며 정렬하는 정렬방식(분할정복법)
- 단점
  - 정렬된 배열을 담을 임시 배열이 하나 더 필요(추가되는 메모리 공간)



## **알고리즘 단계**

![merge_sort](https://i.imgur.com/QTbz2bM.jpg)

1. 분할: 해결하고자 하는 문제를 작은 크기의 동일한 문제들로 분할
2. 정복: 각각의 작은 문제를 순환적(Recursion)으로 해결
3. 합병: 작은 문제의 해를 합하여(merge) 원래 문제에 대한 해를 구함



## 시간복잡도

> 분할정복을 이용하기 때문에 O(NlogN)

- 항상 절반씩 나눠서 실행하기 때문에 최악의 경우도 O(NlogN) 이다.



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
int data[MAX_SIZE] = {};
int sortedOfMergeArray[MAX_SIZE] = {};
int length = sizeof(data)/sizeof(int); // 배열의 크기

////// 함수 선언
void Swap(int *arr, int a, int b);
void seed_init();
void random_number(int *array, int length);
void printArray(int *array);\
void merge_sort(int *data, int start, int end);
void merge(int *data, int start, int mid, int end);

int main() {
	seed_init();
	
	// 합병 정렬
	random_number(data, length);
	printf("\n\nMerge Sort\n");
	printf("정렬 전: ");
	printArray(data);
    second = (int)clock();
	merge_sort(data, 0, length-1);
	printf("\n정렬 후: ");	
	printArray(data);
	printf("\n데이터 %d개의 정렬 수행시간: %0.4f초\n", length, (float)(clock() - second) /CLOCKS_PER_SEC);
	
	return 0;
}

// 스왑 함수 
void Swap(int *arr, int a, int b) {
	int temp = arr[a];
	arr[a] = arr[b];
	arr[b] = temp;
}

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

// 배열 출력 
void printArray(int *array) {
	for(int i=0; i<length; i++) {
		printf("%d ", array[i]);
	}
}

// 병합 정렬(분할&정복) 
void merge_sort(int *data, int start, int end) {
	int mid;

	if(start < end) {
		mid = (start+end)/2;
		merge_sort(data, start, mid);
		merge_sort(data, mid+1, end);
		merge(data, start, mid, end);
	}
}

// 병합  정렬(합병) 
void merge(int *data, int start, int mid, int end) {
	int i = start;
	int j = mid+1;
	int k = start;
	int tmp[MAX_SIZE]; // 임시 배열
	
	while(i<=mid && j<=end) {
		if(data[i] <= data[j]) {
			tmp[k++] = data[i++];
 		} 
		else {
 			tmp[k++] = data[j++];
		}
	} 
	while(i<=mid) {
		tmp[k++] = data[i++];
	}
	while(j<=end) {
		tmp[k++] = data[j++];
	}
	for(int a=start; a<=end; a++) {
		data[a] = tmp[a];
	}
}

```



## 결과

![bubble_sort](https://i.imgur.com/icsSVUR.png) {:.alignleft}