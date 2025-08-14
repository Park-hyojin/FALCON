# FALCON 시스템 아키텍처

## 🏗️ 시스템 구조

### 계층 설계
- **Network Layer**: TCP/UDP 통신 인터페이스
- **Processing Layer**: 비디오/검출 데이터 처리
- **Data Layer**: MySQL 데이터 저장

### 멀티스레딩 구조
```
Main Server (PyQt6 GUI)
├── Video Processor Thread    # 비디오 프레임 처리
├── Detection Processor Thread # 객체 검출 처리  
├── Video Communicator Thread  # UDP 비디오 통신
├── Detection Communicator Thread # TCP 데이터 통신
└── Calibration Thread        # 맵 보정 처리
```

## 🔄 데이터 플로우

### 비디오 처리
```
IDS Video → UDP Receiver → Video Processor → Frame Buffer → Main GUI → Admin GUI
```

### 객체 검출
```
IDS Detection → TCP Server → Access Control → Detection Processor → Database
```

## 🧩 핵심 모듈

### Video Processor
- 프레임 버퍼링 (15프레임, 0.5초)
- FPS 계산 및 순차 처리

### Detection Processor  
- 검출 결과 처리 및 시각화
- 중복 객체 필터링
- 크롭 이미지 자동 저장

### Detection Communicator
- 4개 TCP 서버 관리 (5000, 5100, 5200, 5300)
- 접근 제어 및 활주로 상태 관리
- 좌표 변환 및 구역 판별

## 🔐 보안 및 접근 제어

### 권한 레벨
- **OPEN**: 자유 접근
- **AUTH_ONLY**: 허가된 객체만
- **NO_ENTRY**: 접근 금지

### 이벤트 타입
- **HAZARD**: 위험 상황
- **UNAUTH**: 무허가 접근  
- **RESCUE**: 구조 상황

### 공항 구역
- **활주로**: RWY_A, RWY_B
- **유도로**: TWY_A, TWY_B, TWY_C, TWY_D  
- **잔디**: GRASS_A, GRASS_B

## ⚡ 성능 특징

- **실시간 처리**: 30 FPS 비디오 스트림 지원
- **버퍼링**: 원형 버퍼로 안정적 처리
- **자동 복구**: 네트워크 끊김 시 재연결
- **메모리 관리**: 오래된 데이터 자동 정리 