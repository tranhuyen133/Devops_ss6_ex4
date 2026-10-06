# Báo cáo Bài 4: Quản lý tiến trình nền với nohup và tín hiệu Kill

## 1. Mục tiêu
Chạy một script giám sát dưới nền độc lập bằng nohup, tìm PID, và tắt tiến trình an toàn bằng tín hiệu kill.

## 2. Script loop-monitor.sh
#!/bin/bash
while true; do
    echo "System time: $(date)" >> /tmp/monitor.log
    sleep 5
done

(Script ghi thời gian hiện tại vào /tmp/monitor.log mỗi 5 giây.)

## 3. Cấp quyền và chạy nền
chmod +x loop-monitor.sh
nohup ./loop-monitor.sh > /dev/null 2>&1 &

Giải thích:
- nohup: tiến trình không bị tắt khi đóng terminal / ngắt SSH.
- > /dev/null 2>&1: bỏ qua output ra màn hình (đã ghi vào log).
- &: chạy ở nền, trả lại dấu nhắc.

## 4. Kiểm tra log đang được ghi (tail -n 10 /tmp/monitor.log)
System time: Tue Oct  6 12:47:20 +07 2026
System time: Tue Oct  6 12:47:25 +07 2026
System time: Tue Oct  6 12:47:30 +07 2026
System time: Tue Oct  6 12:47:35 +07 2026
System time: Tue Oct  6 12:47:40 +07 2026
System time: Tue Oct  6 12:47:45 +07 2026
System time: Tue Oct  6 12:47:50 +07 2026
System time: Tue Oct  6 12:47:55 +07 2026
System time: Tue Oct  6 12:48:00 +07 2026

## 5. Tìm PID (ps aux | grep loop-monitor.sh)
macpro  22740  ...  /bin/bash ./loop-monitor.sh
=> PID = 22740

## 6. Tắt tiến trình bằng tín hiệu
# Dùng SIGTERM (15) trước - cách tắt lịch sự
kill -15 22740

# Kiểm tra: ps aux | grep loop-monitor.sh => tiến trình đã biến mất
# (Nếu không tắt được mới dùng SIGKILL: kill -9 22740)

## 7. Giải thích tín hiệu
- SIGTERM (15): yêu cầu tiến trình tự tắt một cách êm ái, cho phép dọn dẹp trước khi thoát.
- SIGKILL (9): buộc tắt ngay lập tức, không cho dọn dẹp; chỉ dùng khi SIGTERM không hiệu quả.

## 8. Kết luận
Script chạy nền độc lập với terminal nhờ nohup, ghi log đều đặn mỗi 5 giây. Tiến trình được tìm bằng PID và tắt an toàn bằng SIGTERM (15).
