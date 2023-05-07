---
layout: post
type: tech
date: 2021-09-28 15:30
category: Others
title: Simple Vector Implementation
subtitle: STL Vector 간단하게 구현해보기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2109281530/img/bg.png
hash-tag: [Others, Vector, Cpp]
draft: false
---

사담을 먼저 하자면,,

작년 이맘때쯤 TIL을 열심히 했더랬다

1년이 지난 지금 많은 부분에서 경험을 쌓고 성장을 이루었지만

여전히 주니어 수준도 안되는, 좁쌀만한 나의 지식과 실력에 더 큰 성장을 염원하게 되었다.

이러한 동기에서 다시금 TIL + 1일 1커밋을 시작하려 한다.

작년 이맘때쯤에는 충분한 실력이 안 되어 1일 1커밋을 통해 의미있는 코드 작성을 하기가 어려웠는데, 이제는 부딪혀볼 만한 실력은 된 것 같아 바쁜 와중에 짬을 내어 TIL과 1일 1커밋을 섞어서 진행해보려 한다. (TIL 역시 커밋이기 때문에 1일 1커밋에 포함되는데, 굳이 1일 1커밋을 명시한 것은 TIL과 같은 공부 정리가 아닌 생산적인 코드를 작성하겠다는 다짐을 담은 것이다.)

본론으로 들어가서

과제를 위해 작성했던 간단한 Vector 구현에 대해 설명해보려 한다.

썼던 코드 리뷰하는 거라 TIL이라고 하긴 좀 민망하지만.. 워밍업이라고 생각하기로 했다.

```cpp

/**
 * Title    : HW1.cpp
 * Date     : 21.09.12
 * By       : Park Chan Ho
 *
 * Tested on
 *  gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)
 *  gcc version 9.2.0 (Windows mingw64)
 *  Visual Studio (Visual C++ 2019 Debug x86)
 */

#include <iostream> // std::cout, std::initializer_list
#include <algorithm> // std::for_each, std::copy
#include <array> // std::array
#include <random> // std::mt19937, std::normal_distribution

```

header는 `iostream`, `algorithm`, `array`, `random` 을 include 했다.

`algorithm` 정도를 제외하면, 헤더는 실제 구현에 사용됐다기 보다는 형식을 맞추기 위해 사용하였다.

자세히 설명하면, `iostream` 은 `std::cout`, `std::initializer_list` 을 위해 사용되었다.

cout은 굳이 설명하지 않아도 다들 알거고, `std::initializer_list` 는 `c++11` 이후 사용되는 `{}` 꼴의 initialize를 사용할 수 있게 해주는 형식이다.

`algorithm`은 `std::for_each`, `std::copy`를 위해 사용되었다.

`array`의 `std::array`는 array 꼴의 initialize를 지원하기 위해 사용되었다.

`random`의 `std::mt19937`, `std::normal_distribution`은 테스트 코드에서 그냥 난수 뽑으려고 썼다.

```cpp

class Vector {
private:
    // top for the last element in Vector
    size_t top;

    // available max size of array
    size_t size;

    float* array;

    /**
     * Vector class is dynamically reallocated
     * when a new element being pushed back at
     * an index that exceeds the size of Vector
     */
    void resize()
    {
        float* new_array = new float[size + 32]; // resizing by adding 32 to size

        // copying every element
        for (int i = 0; i < size; i++)
        {
            new_array[i] = array[i];
        }

        // deleting old array;
        delete[] array;

        array = new_array;

        return;
    }
}

```

먼저 벡터는 동적 배열로 구현했다. 즉, 공간이 꽉 차고 새로운 원소가 삽입되면 더 큰 공간이 다시 할당되며, 동적으로 크기가 변하는 배열이다.

private으로는 `top`, `size`, `array`가 존재한다. array는 말 그대로 Vector 내부의 배열이고, 따라서 값을 임의로 건들지 못하게 당연히 private 설정을 해주어야 한다.
top은 스택에서의 그 top으로 array의 끝 (할당된 공간의 끝이 아닌, 원소가 존재하는 끝)을 뜻하고, size는 Vector에 할당된 크기이다. 현재 구현한 벡터는 말했듯 동적 배열이기 때문에, size는 계속 변한다.

이때 작용하는 메소드가 private인 `resize` 메소드다. 역시 외부에 노출될 필요가 없어 private으로 구현했다.

생성자에서도 나오지만 기본 크기는 32이고, resize 될 때도 32개 단위로 증가한다. 사용자에게 기본 크기 역시 조절할 수 있도록 열어주는 것은 간단하겠지만, 굳이 하지는 않았다.

resize에서는 크기 비교 없이 size가 32 더 큰 배열을 할당하고 이전 배열을 지운다. resize를 호출하는 부분에서 크기 비교가 필요하다.

```cpp

public:
    // no arguments constructor
    Vector()
    {
        top = -1;
        size = 32;
        array = new float[size] {0, };
    }
```

기본 생성자이다. 그냥 argument가 없을 때 빈 배열을 만들고 0으로 초기화한다.

```cpp
    // constructor for {} initialization
    Vector(std::initializer_list<float> I)
    {
        top = I.size() - 1;

        if (top > 32)
        {
            size = (I.size() / 32 + 1) * 32;
        }
        else
        {
            size = 32;
        }

        array = new float[size];
        std::copy(I.begin(), I.end(), array);
    }

```

{} 꼴의 초기화를 위해서 만든 생성자이다.

top은 {}의 원소 개수로 정해지고, size는 32 단위로 top 보다 크게 설정한다

그리고 {}안의 원소를 모두 복사한다.

```cpp
    // constructor when using std::array
    template<size_t arrSize>
    Vector(std::array<float, arrSize> newArray)
    {
        top = arrSize - 1;

        if (top > 32)
        {
            size = (arrSize / 32 + 1) * 32;
        }
        else
        {
            size = 32;
        }

        array = new float[size];
        std::copy(newArray.begin(), newArray.end(), array);
    }
```

`std::array`를 argument로 받아 초기화를 하기 위해서 만든 생성자이다.

로직은 위와 동일하고, `std::array`의 <> 제네릭을 사용할 때 `arrSize`를 argument처럼 가져와 사용할 필요가 있어서 template을 사용했는데..

코드 짜는 당시에는 맞게 짰다고 생각했는데, 고민 없이 생각나는대로 막 짜서 그런가 지금 보면 이게 맞나 싶기도 하다.

확실히, template에 대한 공부가 더 필요하다.

```cpp
    // to push back an element
    void push(float num)
    {
        top++;

        if (top == size)
        {
            this->resize();
        }

        array[top] = num;
    }

    // to modify a data at an index
    void modify(size_t idx, float data)
    {
        if (idx >= top)
        {
            std::cout << "ERROR : INDEX OVER TOP";
            return;
        }

        array[idx] = data;
    }
```

벡터 끝에 원소를 추가하는 `push` 함수와 원소를 수정하는 `modify` 함수이다.
`push` 함수는 size를 초과할 때 `resize`를 호출한다
`modify` 함수는 존재하지 않는 원소를 수정할 수 없게 처리하였다

```cpp
    // to check the size of Vector
    size_t getSize() const
    {
        return size;
    }

    // to check the top of Vector
    size_t getTop() const
    {
        return top;
    }

    // find element at an index
    float at(int i) const
    {
        if (i >= size)
        {
            std::cout << "ERROR : OVER THE VECTOR SIZE";
            throw NULL;
        }

        return array[i];
    }

```

위에 두 개는 getter이고

at 메소드는 STL Vector에 존재하는 at과 동일하게 특정 위치의 원소를 찾아준다.

실제 STL Vector의 `at`은 존재하지 않는 원소에 대해서 에러 처리를 해주는데, 실제 그 정도까지 구현하지는 않았고 여기서는 존재하지 않는 원소를 읽을 경우 `NULL`을 throw 했다. try catch로 NULL을 받아서 처리하던가, 아니면 다른 exception을 정의하여 사용하면 될 듯 하다.

```cpp

    // multiply all elements in a vector by a scalar
    void multiply(float s)
    {
        for (int i = 0; i < size; i++)
        {
            array[i] *= s;
        }
    }
```

스칼라 곱을 구현하는 함수이다.

```cpp
    // show all elements in a vector (1, 2, ...)
    void display()
    {
        std::cout << "\n(" << *array;
        std::for_each(array + 1, array + top + 1, [](float& array){ std::cout << ", " << array; });
        std::cout << ")\n";
    }

    // destructor
    ~Vector()
    {
        delete[] array;
    }
```

`display` 함수는 전체 원소를 출력한다. `for_each`로 내에서 람다 함수로 모든 원소에 대해 cout을 진행한다.

소멸자 역시 정의하였다.

```cpp
// Test Code
int main()
{
    // to put random number in vector
    std::mt19937 gen(310);
    std::normal_distribution<float> dist(0, 1);


    Vector v1 = Vector();

    for (int i = 0; i < 20; i++)
    {
        v1.push(dist(gen));
    }
    v1.display();

    // check if it works after exceeding the size of v1
    for (int i = 0; i < 20; i++)
    {
        v1.push(dist(gen));
    }
    v1.display();


    // modifying the value at index 3
    v1.modify(3, 2.2);
    v1.display();


    v1.multiply(1.2);
    v1.display();

    // initializing by using {}
    Vector v2 = {1.2, 2.3, 3.4};
    v2.display();

    // multiply by scalar
    v2.multiply(2.0);
    v2.display();

    // initializing by std::array
    std::array<float, 4> arr = {1.3, 2.4, 3.5, 3.2};
    Vector v3 = Vector(arr);
    v3.display();

}

```

위는 테스트 코드이다.

전체 코드는 [SimpleVector.cpp](https://github.com/charlieppark/STLVector/blob/master/SimpleVector.cpp) 여기에서 확인할 수 있다.

---
