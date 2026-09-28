# Thông Tin Deploy — Checkpoint 5

> Điền file này sau khi deploy xong. `pytest tests/test_cp5.py` đọc file này
> để tìm địa chỉ service của bạn và gọi thử.
>
> **Chỉ ghi TÊN biến môi trường, tuyệt đối không dán giá trị API key vào đây.**
> Repo này công khai — dán khóa vào là mất khóa.

## Thông Tin Học Viên

| Mục         | Nội dung                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------- |
| Họ và tên   | Nguyễn Công Vinh                                                                                |
| Mã học viên | 2A202602519                                                                                     |
| Repo        | https://github.com/What57-ph/K4-L3A-DAY12-NguyenCongVinh-2A202602519-CloudServicesAndDeployment |

## Service

| Mục         | Nội dung                               |
| ----------- | -------------------------------------- |
| Public URL  | https://day12-agent-yf0b.onrender.com/ |
| Platform    | Render                                 |
| Ngày deploy | 28/09/2026                             |

## Biến Môi Trường Đã Set Trên Cloud

Ghi tên biến và **nguồn giá trị**, không ghi giá trị:

| Biến                    | Đã set | Ghi chú                                   |
| ----------------------- | ------ | ----------------------------------------- |
| `PORT`                  | ✅     | platform tự gán                           |
| `AGENT_API_KEY`         | ✅     | đặt trong dashboard, không nằm trong repo |
| `REDIS_URL`             | ✅     | redis://red-dat42297lnhs73bipn4g:6379     |
| `RATE_LIMIT_PER_MINUTE` | ✅     | 10                                        |
| `MONTHLY_BUDGET_USD`    | ✅     | 10.0                                      |
| `LOG_LEVEL`             | ✅     | INFO                                      |

## Lệnh Kiểm Tra

Thay `<URL>` bằng Public URL ở trên:

```bash
# 1. Liveness — mong đợi 200 {"status":"ok"}
curl -i https://day12-agent-yf0b.onrender.com/health

# 2. Readiness — mong đợi 200 {"status":"ready"} (đã nối được Redis)
curl -i https://day12-agent-yf0b.onrender.com/ready

# 3. Không có API key — mong đợi 401
curl -i -X POST https://day12-agent-yf0b.onrender.com/ \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'

# 4. Có API key — mong đợi 200 kèm câu trả lời
curl -i -X POST https://day12-agent-yf0b.onrender.com/ \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $AGENT_API_KEY" \
  -H "X-User-Id: sv-test" \
  -d '{"question":"Deploy là gì?"}'

# 5. Rate limit — gọi 15 lần, những lần cuối phải trả 429
for i in $(seq 1 15); do
  curl -s -o /dev/null -w "%{http_code} " -X POST https://day12-agent-yf0b.onrender.com/ \
    -H "Content-Type: application/json" \
    -H "X-API-Key: $AGENT_API_KEY" \
    -H "X-User-Id: sv-test" \
    -d '{"question":"test"}'
done; echo
```

## Kết Quả Chạy Thật

Dán output của các lệnh trên vào đây:

```
HTTP/1.1 200 OK
Content-Type: application/json
Date: Mon, 28 Sep 2026 09:37:56 GMT
Server: railway-hikari
x-railway-request-id: inbYlqIDTKG06JubpHNmDw
Content-Length: 57
x-hikari-trace: sin1.d1nj
x-railway-edge: sin1
Connection: keep-alive

{"status":"ok","service":"day12-agent","version":"1.0.0"}

HTTP/1.1 200 OK
Date: Mon, 28 Sep 2026 10:33:25 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
rndr-id: aca87c63-c802-4222
Server: cloudflare
vary: Accept-Encoding
x-render-origin-server: uvicorn
cf-cache-status: DYNAMIC
CF-RAY: a42216fb2b0e9c86-SIN
alt-svc: h3=":443"; ma=86400

{"status":"ready","redis":true}

HTTP/1.1 401 Unauthorized
Content-Type: application/json
Date: Mon, 28 Sep 2026 10:34:47 GMT
Server: railway-hikari
x-railway-request-id: opQV8b9SSNeIYYuu2h0iww
Content-Length: 39
x-hikari-trace: sin1.98a6
x-railway-edge: sin1
Connection: keep-alive

{"detail":"invalid or missing API key"}

HTTP/1.1 200 OK
Date: Mon, 28 Sep 2026 10:39:13 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
cf-cache-status: DYNAMIC
rndr-id: 2a7587dd-e110-4ce7
Server: cloudflare
vary: Accept-Encoding
x-render-origin-server: uvicorn
CF-RAY: a4221f7a3cdbc6f3-SIN
alt-svc: h3=":443"; ma=86400

{"answer":"Câu hỏi hay. Deploy là gì thường được giải quyết bằng cách chuẩn hóa môi trường chạy: cùng một image chạy giống nhau ở laptop và trên cloud.","user_id":"sv-test","history_length":0,"cost_usd":2.145e-05,"tokens":{"in":3,"out":35}}

C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>for /L %i in (1,1,15) do curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"

C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
200
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
429
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
429
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
429
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
429
C:\Users\User>curl.exe -s -o NUL -w "%{http_code} " -X POST "https://day12-agent-yf0b.onrender.com/ask" -H "Content-Type: application/json" -H "X-API-Key: %AGENT_API_KEY%" -H "X-User-Id: sv-test" -d "{\"question\":\"test\"}"
429

```

## Ảnh Chụp Màn Hình

Đặt ảnh trong thư mục `screenshots/`:

- `screenshots/dashboard.png` — trang quản lý service trên platform
- `screenshots/health.png` — kết quả gọi `/health` từ trình duyệt hoặc curl

---
