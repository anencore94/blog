# 파일 시스템

## 파일 시스템, 그리고 필요성

- 데이터를 저장하고 읽고 쓰는 데 있어서, 저장 장치의 특정 block 의 특정 주소를 직접 관리해서 접근한다면 매우 복잡하고 불편함
- 이런 관리를 대신 해주는 소프트웨어
- 디렉토리 구조와 같은 계층 구조를 지원하기에 사용자가 데이터를 파일 단위로 관리하기 쉬움
  - 디렉토리는 여러 하위 디렉토리 또는 하위 파일의 주소를 관리하는 하나의 **파일**
  
## inode

- 파일 시스템에서 사용하는 자료구조
- 모든 파일은 각자의 고유한 inode 를 가지고 있으며 inode 구조체는 소유자 그룹, 접근 모드(읽기, 쓰기, 실행 권한), 파일 형태, 데이터 저장 위치의 주소, 아이노드 숫자(inode number, i-number, 아이넘버) 등 해당 파일에 관한 정보를 가지고 있다
- 파일시스템 내의 파일들은 고유한 아이노드 숫자를 통해 식별 가능하기에, 외부적으로는 번호로 표시되며 내부적으로는 파일에 대한 정보와 데이터블록 주소에 대한 정보를 저장한다.
  
## File System - Block Device 매핑

- Block Device 가 4 KB의 여러 block 으로 이루어져 있을 때 대부분의 block 은 data 자체를 저장하기 위해 쓰임
- 그 외 meta data 를 저장하는 block
  - inode block
    - inode block 에 먼저 접근해서 해당 inode 의 data가 어느 data block 에 저장되어있는지 그 주소를 알 수 있음
    - 전체 디바이스 중 몇 퍼센트를 inode block 으로 사용할 건지 딱 정해져있음
  - bitmap block
    - bitmap 은 어떤 block 들이 사용 가능한지를 bit로 표현
    - Data bitmap, inode bitmap 따로 존재
  - super block
    - file system의 basic config에 대한 정보를 담고 있음
    - block 의 크기, inode의 총 개수 등

## File Descriptor (fd)

- 리눅스에서 파일을 읽고 쓰기 위해서는 반드시 파일을 open() 해야 한다.(시스템 콜)
- 파일이 오픈되면 fd 라는 일종의 index 번호가 반환되며, 이 값은 file 을 open 한 프로세스의 고유 번호라 생각하면 된다
- 시스템에서 사용중이지 않은 가장 낮은 fd 숫자를 반환
