# CUDA의 데이터 처리 

## 1.CUDA의 데이터 흐름

CUDA는 뛰어난 그래픽 카드의 연산 능력을 이용하여 처리하는 방법이다.
기존의 CPU처리 방법에 더 추가해야 하는 과정이 있다. 추가된 과정은 PC의 메모리에 있는 입력 데이터를 그래픽 카드의 메모리로 전달하고 GPU가 처리한 결과를 다시 그래픽 카드의 메모리에서 PC의 메모리로 가져오는 과정이다.

CUDA의 데이터 흐름은

1. 그래픽 카드 메모리 공간을 할당한다.
2. PC의 입력 데이터를 그래픽 카드의 메모리로 복사한다.
3. 강력한 GPU성능을 이용하여 병렬처리한다.
4. 처리된 결과를 그래픽 카드의 메모리에서 PC의 메모리로 복사한다.


다음은 그래픽 카드에 메모리 공간을 할당하고 데이터를 복사하는 방법이다

## 2 그래픽 카드 메모리 사용하기

2-1 그래픽 카드 메모리의 할당(예시)
```bash
cudaError_t cudaMalloc(void** devPtr, size_t count);
```
cudaMalloc() 함수는 그래픽 카드의 DRAM에 메모리 공간을 할당하는 기능을 한다.

첫 번째 인자는 할당할 메모리를 가리키는 포인터를 입력한다.
두 번째 인자로는 할당할 메모리의 크기를 입력한다.
cudaError_t는 함수 호출의 성공 여부를 나타내는 반환값이다.

할당 성공 -> 'cudaSuccess' 를 반환.

할당 실패 -> 원인 약 20가지의 값을 반환.

2-2 PC에서 그래픽 카드로 데이터 복사(예시)
```bash
cudaError_t cudaMemcpy(void* dst, const void* src, size_t count, cudaMemcpyHostToDevice);
```
cudaMemcpy() 함수는 CUDA에서 메모리를 복사할때 사용한다.

PC 메모리에서 GPU 메모리로 또는 GPU메모리에서 PC메모리로 데이터를 전송하는 역할을 한다.

첫 번째 인자는 복사할 목적지가되는 포인터로 cudaMemcpy() 함수로 할당한 그래픽 메모리의 포인터를 넣는다.

두 번째 인자는 복사할 소스가 되는 포인터로 PC에서 Malloc() 함수로 할당한 메모리의 포인터를 입력한다.

세 번째 인자는 복사할 크기를 지정한다.

네 번째 인자는 복사를 수행할 종류를 넣는데 PC에서 그래픽 카드로 복사할 때는 cudaMemcpyHostToDevice를 사용한다.

마지막 인자는 cudaMemcpy의 종류로 아래와 같은 종류를 넣을 수 있다.

|종류|동작|
|---|---|
| CudaMemcpyHostToHost| PC 메모리에서 PC 메모리로 복사 |
| CudaMemcpyHostToHoDevice| PC 메모리에서 그래픽 카드로 복사 |
| CudaMemcpyDeviceToHost| 그래픽 카드 메모리에서 그래픽 카드로 복사 |
| CudaMemcpyDeviceToDevice| 그래픽 카드 메모리에서 그래픽 카드로 복사 |

2-3 그래픽 카드 메모리의 해체 (예시)
```bash
cudaError_t cudaFree(void* devPtr);
```
cudaFree() 함수는 그래픽 카드의 DRAM에 할당된 메모리를 해체한다, 인자로 해체하고자 하는 포인터를 전달한다.
- 예시
```bash
//그래픽 카드에 메모리 사용하기
#include <stdio.h>
 
int main()
{
    int InputData[5] = {1, 2, 3, 4, 5};
    int OutputData[5] = {0};
 
    int* GraphicsCard_memory;
 
    //그래픽카드 메모리의 할당
    cudaMalloc((void**)&GraphicsCard_memory, 5*sizedof(int));
 
    //PC에서 그래픽 카드로 데이터 복사
    cudaMemcpy(GraphicsCard_memory, InputData, 5*sizedof(int), cudaMemcpyHostToDevice);
 
    //그래픽 카드에서 PC로 데이터 복사
    cudaMemcpy(OutputData, GraphicsCard_memory, 5*sizedof(int), cudaMemcpyDeviceToHost);
 
    //결과 출력
    for( int i = 0; i < 5; i++)
    {
        printf(" OutputData[%d] : %d\n", i, OutputData[i]);
    }
 
    //그래픽 카드 메모리의 해체
    cudaFree(GraphicsCard_memory);
 
    return 0;
}
```
