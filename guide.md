# ipTIME N702E Firmware Analysis Guide

본 문서는 **ipTIME N702E 라우터 펌웨어의 획득부터 파일 시스템 추출, 구조 분석, 취약점 분석까지의 전체 과정을 설명하는 가이드**입니다.
펌웨어 리버싱 및 IoT 보안 분석 연구 목적으로 작성되었습니다.

---

# 1. Firmware Acquisition (펌웨어 획득)

분석에 사용할 공식 펌웨어 이미지를 확보합니다.

## 1.1 공식 다운로드

ipTIME 공식 사이트에서 다운로드합니다.

* 사이트
  https://iptime.com/iptime/?page_id=11&page_id_l=2&page_id_m=2

* 모델 검색
  `N702E`

* 최신 펌웨어 다운로드
  `*.bin`

## 1.2 프로젝트 디렉토리 구조

```
project-root
│
├─ firmware
│   └─ N702E_v12.102.bin
│
└─ README.md
```

다운로드한 펌웨어는 `firmware` 디렉토리에 저장합니다.

---

# 2. Environment Setup (분석 환경 구축)

펌웨어 분석을 위해 Linux 환경을 사용하는 것이 좋습니다.

권장 환경

* Ubuntu
* Kali Linux

## 2.1 필수 도구 설치

```bash
sudo apt update

sudo apt install binwalk
sudo apt install squashfs-tools
sudo apt install firmware-mod-kit
sudo apt install qemu-user-static
sudo apt install file
sudo apt install strings
sudo apt install grep
sudo apt install build-essential
```

## 2.2 Python 도구

```bash
pip install binwalk
```

---

# 3. Firmware Structure Analysis (펌웨어 구조 분석)

펌웨어 내부 구조를 확인합니다.

```bash
binwalk firmware/N702E_v12.102.bin
```

예시 출력

```
DECIMAL       HEXADECIMAL     DESCRIPTION
-----------------------------------------------------
0             0x0             uImage header
64            0x40            LZMA compressed data
1048576       0x100000        Squashfs filesystem
```

확인 가능한 정보

* 커널 이미지
* 압축 방식
* 파일 시스템
* 데이터 위치

---

# 4. Firmware Extraction (펌웨어 추출)

`binwalk`를 이용해 펌웨어 내부 파일 시스템을 추출합니다.

```bash
binwalk -eM firmware/N702E_v12.102.bin
```

옵션 설명

```
-e  파일 시스템 추출
-M  중첩된 압축까지 재귀적으로 언패킹
```

추출 결과

```
_firmware/N702E_v12.102.bin.extracted
```

예시 구조

```
N702E_v12.102.bin.extracted
│
├── squashfs-root
│   ├── bin
│   ├── etc
│   ├── sbin
│   ├── usr
│   ├── www
│   └── lib
```

---

# 5. Filesystem Inspection (파일 시스템 분석)

추출된 루트 파일 시스템을 확인합니다.

```bash
cd squashfs-root
ls
```

중요 디렉토리

| Directory | Description |
| --------- | ----------- |
| /bin      | 기본 실행 바이너리  |
| /sbin     | 시스템 관리 바이너리 |
| /etc      | 설정 파일       |
| /www      | 웹 관리 페이지    |
| /lib      | 공유 라이브러리    |
| /usr      | 추가 유틸리티     |

---

# 6. CPU Architecture Identification (CPU 아키텍처 확인)

펌웨어의 CPU 아키텍처를 확인합니다.

```bash
file bin/busybox
```

예시 출력

```
ELF 32-bit LSB executable, MIPS
```

ipTIME 라우터 대부분은 다음 아키텍처를 사용합니다.

* MIPS
* ARM

---

# 7. Useful Tools

| Tool             | Description |
| ---------------- | ----------- |
| binwalk          | 펌웨어 추출      |
| Ghidra           | 리버싱         |
| IDA Pro          | 고급 리버싱      |
| radare2          | CLI 분석      |
| firmware-mod-kit | 펌웨어 수정      |
| qemu             | 에뮬레이션       |

---

# 8. References

* https://binwalk.org
* https://ghidra-sre.org
* https://qemu.org

---

