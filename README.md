## Laboratory work VI

[![Build Status](https://travis-ci.com/nastya-asya/lab06.svg?branch=master)](https://travis-ci.com/nastya-asya/lab06)

<a href="https://yandex.ru/efir/?stream_id=vGWlFO3of-Rg"><img src="https://raw.githubusercontent.com/tp-labs/lab06/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```sh
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=nastya-asya
$ export GITHUB_EMAIL=nastya.asya08@yandex.ru
$ alias edit=subl
$ alias gsed=sed # for *-nix system
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06
$ cd projects/lab06
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06
```

```sh
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff
```

```sh
$ touch DESCRIPTION && edit DESCRIPTION
$ touch ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"
$ cat > ChangeLog.md <<EOF
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

```sh
$ cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```

```sh
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```

```sh
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

```sh
$ gsed -i 's/lab05/lab06/g' README.md
```

```sh
$ git add .
$ git commit -m"added cpack config"
$ git tag v0.1.0.0
$ git push origin master --tags
```

```sh
$ travis login --auto
Successfully logged in as nastya-asya!
$ travis enable
nastya-asya/lab06: enabled :)
```

```sh
$ cmake -H. -B_build
-- The C compiler identification is GNU 7.5.0
-- The CXX compiler identification is GNU 7.5.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc - works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ - works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/nastya-asya/nastya-asya/workspace/projects/lab06/_build
$ cmake --build _build
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build
$ cpack -G "TGZ"
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/nastya-asya/nastya-asya/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ cd ..
```

```sh
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
-- Configuring done
-- Generating done
-- Build files have been written to: /home/nastya-asya/nastya-asya/workspace/projects/lab06/_build
$ cmake --build _build --target package
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print []
CPack: Create package
CPack: - package: /home/nastya-asya/nastya-asya/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```

```sh
$ mkdir artifacts
$ mv _build/*.tar.gz artifacts
$ tree artifacts
artifacts
├── print-0.1.0.0-Linux.tar.gz
└── screenshot.png

0 directories, 2 file
```

## Report

```sh
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

[![Build Status](https://travis-ci.com/nastya-asya/hw06.svg?branch=master)](https://travis-ci.com/nastya-asya/hw06) [![Build Status](https://ci.appveyor.com/api/projects/status/wkbl6ku2g573k1v4?svg=true)](https://ci.appveyor.com/project/nastya-asya/hw06)

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)


Настройка CPack через определение CPack-переменных внутри файла CMakeLists.txt 

```sh
$ sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_STRING "v\${SOLVER_VERSION}")
' CMakeLists.txt
$ sed -i "" '/project(solver)/a\
set(SOLVER_VERSION\
  \${SOLVER_VERSION_MAJOR}.\${SOLVER_VERSION_MINOR}.\${SOLVER_VERSION_PATCH}.\${SOLVER_VERSION_TWEAK})
' CMakeLists.txt
$ sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_PATCH 0)
' CMakeLists.txt
$ sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_MINOR 0)
' CMakeLists.txt
$ sed -i "" '/project(solver)/a\
set(SOLVER_VERSION_MAJOR 1)
' CMakeLists.txt
```

Создание файлов DESCRIPTION и ChangeLog.md

```sh
$ touch DESCRIPTION && edit DESCRIPTION
$ touch ChangeLog.md
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"
$ cat > ChangeLog.md <<EOF
* ${DATE} nastya-asya <nastya.asya08@yandex.ru> 1.0.0
- Initial RPM release
EOF
```

Создание и заполнение файла CPackConfig.cmake

```sh
$ cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_PACKAGE_CONTACT nastya.asya08@yandex.ru)
set(CPACK_PACKAGE_VERSION_MAJOR \${SOLVER_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${SOLVER_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${SOLVER_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION \${SOLVER_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Application Solver for square equation")
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "solver-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "solver")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_WIX_LICENSE_RTF ${CMAKE_CURRENT_SOURCE_DIR}/LICENSE.txt)
EOF
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```

Добавление CPackConfig.cmake в основной CMakeLists.txt

```sh
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

Создание .travis.yml

```sh
$ cat >> .travis.yml <<EOF
language: cpp
jobs:
  include:
  - os: linux
    script:
    - cmake -H. -B_build
    - cmake --build _build
    - cd _build
    - cpack -G DEB
    - cpack -G RPM
    - cd ..
addons:
  apt:
    packages:
      - cmake
      - cmake-data
      - rpm
EOF
```

Авторизация в travis-ci

```sh
$ travis login --auto 
Successfully logged in as nastya-asya!
$ travis enable 
nastya-asya/hw06: enabled :)
$ cat >> CMakeLists.txt <<EOF
file:
  - ./_build/solver-1.0.0.-Darwin.dmg
  - ./_build/solver-1.0.0.-Linux.deb
  - ./_build/solver-1.0.0.-Linux.rpm
skip_cleanup: true
on:
  tags: true
EOF
```

```sh
$ git add .
$ git commit -m "added cpack config"
$ git tag v1.0.0
$ git push origin master --tags
```

Создание .appveyor.yml

```sh
$ cat >> appveyor.yml <<EOF
image: Visual Studio 2019
platform:
  - x86
  - x64
configuration: Release

build_script:
  - cmd: cmake -H. -B_build
  - cmd: cmake --build _build --config Release
  - cmd: cd _build
  - cmd: ls
  - cmd: cpack -G NSIS
  - cmd: cd ..

artifacts:
  - path: ./_build/*.msi
    name: solver
deploy:
  release: $(APPVEYOR_REPO_TAG_NAME)
  description: 'Release description'
  provider: GitHub
  auth_token:
    secure: tZdq4qXRLud/z27+KjHqLP0jpdTdUkrQ1XmSJMbSAnd2KTvjSqLjRlueHzHPhzLe
  artifact: print
  on:
    APPVEYOR_REPO_TAG: true
EOF
```


В качестве подсказки:
```sh
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2020 The ISC Authors
```
