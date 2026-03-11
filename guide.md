# 펌웨어 분석 가이드 (Firmware Analysis Guide)

본 문서는 `ipTIME N702E` 펌웨어의 획득부터 내부 파일 시스템 언패킹까지의 과정을 단계별로 기술합니다.

## 1. 펌웨어 획득 (Acquisition)
분석에 사용할 공식 펌웨어 이미지를 확보합니다.
* **공식 다운로드**: [ipTIME 고객지원센터](https://iptime.com/iptime/?page_id=11&page_id_l=2&page_id_m=2)에서 모델명 `N702E`를 검색하여 최신 펌웨어(`*.bin`)를 다운로드합니다.
* **저장 경로**: 다운로드한 파일은 프로젝트 내 `/firmware` 디렉토리에 위치시킵니다.

## 2. 파일 시스템 추출 (Unpacking)
`binwalk` 도구를 사용하여 바이너리 내부의 파일 시스템을 식별하고 추출합니다.

```bash
# 1. 파일 포맷 및 구조 스캔
# 펌웨어 바이너리 내부에 존재하는 파일 시스템 및 압축 형태 식별
binwalk firmware/N702E_v12.102.bin

# 2. 파일 시스템 추출 및 재귀적 언패킹
# -e: 파일 시스템 추출
# -M: 중첩된 구조까지 재귀적으로 언패킹
binwalk -eM firmware/N702E_v12.102.bin
```
