# FALCON 배포 가이드

## 🎯 시스템 요구사항

- **OS**: Ubuntu 20.04+ (권장)
- **Python**: 3.8+
- **MySQL**: 8.0+
- **Hardware**: CPU 4코어, RAM 8GB, SSD 50GB

## 🔧 설치

### 1. Python 환경
```bash
python3 -m venv falcon_env
source falcon_env/bin/activate
pip install -r requirements.txt
```

### 2. MySQL 설정
```bash
sudo apt install -y mysql-server
mysql -u root -p
```

```sql
CREATE DATABASE falcon_db;
CREATE USER 'falcon_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON falcon_db.* TO 'falcon_user'@'localhost';
FLUSH PRIVILEGES;
```

### 3. 데이터베이스 초기화
```bash
python db/migration.py
```

### 4. 설정 수정
`config.py`에서 DB 정보 수정:
```python
DB_HOST = "localhost"
DB_USER = "falcon_user" 
DB_PASSWORD = "your_password"
DEFAULT_CLIENT_HOST = "192.168.x.x"  # Admin GUI IP
```

## 🚀 실행

### 개발/테스트
```bash
python server.py
```

### 운영 서비스 (Linux)
```bash
# 서비스 파일 생성
sudo nano /etc/systemd/system/falcon-server.service
```

```ini
[Unit]
Description=FALCON Server
After=mysql.service

[Service]
Type=simple
User=falcon
WorkingDirectory=/home/falcon/falcon-server
ExecStart=/home/falcon/falcon_env/bin/python server.py
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable falcon-server
sudo systemctl start falcon-server
sudo systemctl status falcon-server
```

## 🔒 보안

### 방화벽 설정
```bash
sudo ufw enable
sudo ufw allow from 192.168.1.0/24 to any port 4000:5300
```

### 로그 모니터링
```bash
tail -f logs/server.log
```

## 🔄 백업

### 데이터베이스 백업
```bash
#!/bin/bash
mysqldump -u falcon_user -p falcon_db > backup_$(date +%Y%m%d).sql
gzip backup_$(date +%Y%m%d).sql
```

## 🚨 문제 해결

### 서비스 실패
```bash
sudo systemctl status falcon-server
sudo journalctl -u falcon-server -f
```

### 포트 충돌
```bash
sudo netstat -tlnp | grep :5000
sudo kill -9 <PID>
```

### DB 연결 오류
```bash
mysql -u falcon_user -p falcon_db
``` 