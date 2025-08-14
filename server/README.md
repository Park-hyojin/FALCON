# FALCON 공항 보안 모니터링 서버

실시간 객체 검출 및 활주로 보안 관리 시스템

## 🎯 주요 기능

- **실시간 객체 검출**: IDS 시스템과 UDP/TCP 통신으로 영상 및 검출 데이터 처리
- **활주로 보안 관리**: 무허가 접근 탐지 및 활주로 상태 모니터링 
- **조류 위험도 분석**: BDS 시스템과 연동한 실시간 위험도 평가
- **음성 요청 처리**: 조종사 문의에 대한 자동 응답
- **통합 GUI**: PyQt6 기반 실시간 모니터링 인터페이스

## 🏗️ 시스템 구조

```
IDS (영상) ─UDP:4000─┐
IDS (검출) ─TCP:5000─┤
Admin GUI ─TCP:5100─┼─ FALCON SERVER ─ MySQL DB
BDS (조류) ─TCP:5200─┤
Pilot GUI ─TCP:5300─┘
```

## 📁 프로젝트 구조

```
server/
├── server.py              # 메인 서버
├── config.py              # 설정 파일
├── falcon/                # 핵심 처리 모듈
│   ├── video_processor.py
│   ├── detection_processor.py  
│   ├── udp_stream.py
│   └── tcp_stream.py
├── network/               # TCP/UDP 통신
├── db/                    # 데이터베이스
└── debug/                 # 디버그 도구
```

## 🚀 설치 및 실행

### 1. 환경 설정
```bash
# Python 3.8+ 필요
pip install -r requirements.txt

# MySQL 설정
mysql -u root -p
CREATE DATABASE falcon_db;
python db/migration.py
```

### 2. 설정 수정
`config.py`에서 DB 정보 및 네트워크 설정 수정

### 3. 실행
```bash
python server.py
```

## 🔧 네트워크 포트

| 포트 | 프로토콜 | 용도 |
|------|----------|------|
| 4000 | UDP | IDS 영상 수신 |
| 4100 | UDP | Admin GUI 영상 전송 |
| 5000 | TCP | IDS 검출 데이터 |
| 5100 | TCP | Admin GUI 제어 |
| 5200 | TCP | BDS 조류 데이터 |
| 5300 | TCP | Pilot 음성 요청 |

## 📚 문서

- [시스템 아키텍처](ARCHITECTURE.md)
- [API 문서](API_DOCUMENTATION.md)
- [배포 가이드](DEPLOYMENT.md) 