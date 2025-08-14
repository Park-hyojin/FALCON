# FALCON API 문서

## 📡 통신 개요

JSON 기반 TCP 통신, 메시지는 `\n`으로 구분

## 🎯 IDS Detection API (Port 5000)

### 객체 검출 데이터
```json
{
    "type": "detection",
    "camera_id": "A",
    "img_id": 12345,
    "detections": [
        {
            "object_id": 1001,
            "class": "PERSON",
            "confidence": 0.95,
            "bbox": [100, 150, 200, 300],
            "center_x": 150,
            "center_y": 225,
            "event_type": 2
        }
    ]
}
```

### 맵 보정
```json
{
    "type": "map_calibration",
    "camera_id": "A", 
    "matrix": [[1.2, 0.1, 100], [0.0, 1.1, 50], [0.0, 0.0, 1.0]],
    "scale": 2.5
}
```

## 🖥️ Admin Control API (Port 5100)

### CCTV 제어
```json
// 영상 송출 시작
{"type": "command", "data": "CCTV_ON A"}

// 응답
{"type": "response", "status": "OK", "message": "Camera A streaming started"}
```

### 로그 조회
```json
// 이벤트 로그 (기간별)
{"type": "command", "data": "LC_OL 2024-01-01 2024-01-31"}

// 객체 상세 정보 
{"type": "command", "data": "OBJECT_DETAIL 1001"}

// 맵 뷰 (현재 객체 위치)
{"type": "command", "data": "MAP_VIEW CURRENT"}
```

## 🐦 BDS Data API (Port 5200)

### 조류 위험도 전송
```json
{
    "type": "bird_risk",
    "risk_level": 2,  // 1:HIGH, 2:MEDIUM, 3:LOW
    "bird_count": 15,
    "location": "RWY_A"
}
```

## ✈️ Pilot Voice API (Port 5300)

### 음성 요청
```json
// 조류 위험도 문의
{"type": "voice_request", "request_code": "BR_INQ"}

// 활주로 상태 문의  
{"type": "voice_request", "request_code": "RWY_A_STATUS"}

// 이용 가능한 활주로 문의
{"type": "voice_request", "request_code": "RWY_AVAIL_IN"}
```

### 음성 응답
```json
// 위험도 응답: BR_HIGH, BR_MEDIUM, BR_LOW
// 활주로 상태: CLEAR, BLOCKED  
// 이용 가능성: ALL, A_ONLY, B_ONLY, NONE
{
    "type": "voice_response",
    "response_code": "BR_MEDIUM", 
    "message": "Current bird risk level is MEDIUM"
}
```

## 📹 UDP 비디오

### 수신 (Port 4000)
프로토콜: `CAMERA_ID:IMG_ID:JPEG_DATA`

### 전송 (Port 4100)  
프로토콜: `CAMERA_ID:IMG_ID:PROCESSED_JPEG_DATA`

## 🚨 오류 응답

```json
{
    "type": "error",
    "status": "ERROR",
    "message": "Invalid format"
}
```

상태 코드: `OK`, `ERROR`, `INVALID_FORMAT`, `NOT_FOUND` 