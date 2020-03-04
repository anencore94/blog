# fsync

## 시스템 콜

- user level 의 응용 프로그램에서 kernel level 의 os 에게 (시스템 자원 관련된) 기능 수행을 요청하기 위한 API
  - os 의 kernel 이 제공하는 서비스를 응용 프로그램이 사용하고 싶은 경우를 위해 제공하는 인터페이스

## 종류

- 프로세스 제어 : process control
  - end, abort, execute, wait time, ...
- 파일 조작 : file manipulation
  - open, close, read, write, get file attribute, create file, ...
- 장치 관리 : device management
  - request device, release device, attach, detach, ...
- 정보 유지 : information maintenance
  - time, date, ...
- 통신 : communication
  - create session, remove session, ...

## fs 관련 시스템 콜 중 일부

### write()

```c
#include <unistd.h>

ssize_t write(int fd, const void *buf, size_t count);
```

- fd 로 데이터 쓰기 또는 전송
  - fd : file descriptor
  - buf : 쓰기를 할 데이터 메모리 영역
  - count : 쓸 데이터의 size (byte 수)
  - return : 실제로 쓴 데이터의 byte 수
    - count 보다 작거나 0, -1 의 경우에는 정상적으로 write 되지 않은 것을 의미

### fsync()

```c
#include <unistd.h>

int fsync(int fd);
```

- write() 함수는 os 의 kernel 이 buffer 까지만 write 하고(caching) write 가 끝났다고 return 함
  - os 는 효율을 위해서 이렇게 kernel buffer 에 저장된 데이터를 나중에 실제 disk 에 write 함
- fsync() 함수는 fd 와 관련된 모든 데이터를(kernel 에 저장된 buffer와 meta 포함) 무조건 disk 에 write 하라는 함수 (disk 와의 동기화)
  - fd : 쓰기로 open 된 file descriptor
  - return : 0 (정상) or -1 (오류 발생)
    - 반환값이 0이면 충분한 시간이 흐른 뒤에는 disk 에 반영한다는 것을 보장할 수 있음
