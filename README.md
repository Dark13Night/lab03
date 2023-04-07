# report lab03

Представьте, что вы стажер в компании "Formatter Inc.".
## Задание 1

Вам поручили перейти на систему автоматизированной сборки CMake. Исходные файлы находятся в директории formatter_lib. В этой директории находятся файлы для статической библиотеки formatter. Создайте CMakeList.txt в директории formatter_lib, с помощью которого можно будет собирать статическую библиотеку formatter.

Файл CMakeLists.txt в директории formatter_lib

```
cmake_minimum_required(VERSION 3.4)
project(formatter_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(formatter_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```

![VirtualBox_kali-linux-2022 4-virtualbox-amd64_07_04_2023_07_29_54](https://user-images.githubusercontent.com/112771541/230545012-6fbcc96d-131d-4900-940d-4688d4a0df7f.png)

##Задание 2

У компании "Formatter Inc." есть перспективная библиотека, которая является расширением предыдущей библиотеки. Т.к. вы уже овладели навыком созданием CMakeList.txt для статической библиотеки formatter, ваш руководитель поручает заняться созданием CMakeList.txt для библиотеки formatter_ex, которая в свою очередь использует библиотеку formatter.

Файл CMakeLists.txt в директории Formatter_ex_lib

```
cmake_minimum_required(VERSION 3.4)
project(formatter_ex_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib formatter_lib_dir)

add_library(formatter_ex_lib STATIC ${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex.cpp)

target_include_directories(formatter_ex_lib PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_lib
)

target_link_libraries(formatter_ex_lib formatter_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```

![VirtualBox_kali-linux-2022 4-virtualbox-amd64_07_04_2023_07_42_30](https://user-images.githubusercontent.com/112771541/230545515-43c221ce-8f0e-4bbd-8419-dc979530537f.png)


##Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек. Чтобы продемонстрировать как работать с библиотекой formatter_ex, вам необходимо создать два CMakeList.txt для двух простых приложений:

- hello_world, которое использует библиотеку formatter_ex;

Файл CMakeLists.txt в директории hello_world_application

```
cmake_minimum_required(VERSION 3.4)

project(hello_world)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)

add_executable(hello_world ${CMAKE_CURRENT_SOURCE_DIR}/hello_world.cpp)

target_include_directories(hello_world PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
)

target_link_libraries(hello_world formatter_ex_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```

Запускаем

```
$ build/hello_world
```

![VirtualBox_kali-linux-2022 4-virtualbox-amd64_07_04_2023_07_48_00](https://user-images.githubusercontent.com/112771541/230545717-56a7b3d0-a3fc-4c6e-b338-74801c477d4a.png)

- solver, приложение которое испольует статические библиотеки formatter_ex и solver_lib.

Диреткория solver_lib

Исправленный файл solver.cpp

```
#include "solver.h"

#include <cmath>
#include <stdexcept>

void solve(float a, float b, float c, float& x1, float& x2)
{
    float d = (b * b) - (4 * a * c);

    if (d < 0)
    {
        throw std::logic_error{"error: discriminant < 0"};
    }

    x1 = (-b - sqrtf(d)) / (2 * a);
    x2 = (-b + sqrtf(d)) / (2 * a);
}
```

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.4)
project(solver_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(solver_lib ${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp)
```

Директория solver_application

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.4)
project(solver)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib formatter_ex_lib_dir)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib solver_lib_dir)
add_executable(solver ${CMAKE_CURRENT_SOURCE_DIR}/equation.cpp)
target_include_directories(formatter_ex_lib PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/../formatter_ex_lib
${CMAKE_CURRENT_SOURCE_DIR}/../solver_lib
)
target_link_libraries(solver formatter_ex_lib solver_lib)
```

Собираем

```
$ cmake -B build
$ cmake --build build
```

![VirtualBox_kali-linux-2022 4-virtualbox-amd64_07_04_2023_07_55_29](https://user-images.githubusercontent.com/112771541/230546127-ba00fc63-92fa-454c-8218-8ba9799b049e.png)

Запускаем

```
$ build/solver 
```

![VirtualBox_kali-linux-2022 4-virtualbox-amd64_07_04_2023_07_56_00](https://user-images.githubusercontent.com/112771541/230546208-6822f3a1-46fe-4f15-8bec-e9142793f762.png)
