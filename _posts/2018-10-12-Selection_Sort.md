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

void selection_sort(int array[]) {
	int i, j, min, temp;
	printf("정렬 전: ");
	for(i=0; i<12; i++) {
		printf("%d ", array[i]);
	}
	
	for(i=0; i<12; i++) {
		min = i;
		for(int j=i; j<12; j++) {
			if(array[j] < array[min]) {
				min = j;
			}
 		}
 		temp = array[min];
 		array[min] = array[i];
 		array[i] = temp;
	} 
	
	printf("\n정렬 후: "); 
	for(i=0; i<12; i++) {
		printf("%d ", array[i]);
	}
	
}

int main() {
	int array[12] = {32, 15, 2, 50, 0, 1, 100, 29, 88, 69, 20, 999};
	selection_sort(array);

	return 0;
}
```

