# 질문: Priority가 동일한 정책들의 특징과 각각의 단점은 무엇인가?

### FIFO (First In First Out)

**FIFO**는 자료구조의 큐(queue)와 같은 원리로 동작하며, 먼저 도착한 프로세스를 먼저 스케줄링하는 방식이다. 구현이 단순하다는 장점이 있지만, 작업 시간이 긴 프로세스가 앞에 있을 경우 뒤따르는 짧은 작업들이 모두 대기해야 한다. 이를 **컨보이 효과(convoy effect)** 라고 한다.

예를 들어:

![alt text](https://private-user-images.githubusercontent.com/102043957/365362855-cf5645c0-4cca-404d-b02c-d1ee8437eda8.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA0MzUsIm5iZiI6MTcyNjk3MDEzNSwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1MzYyODU1LWNmNTY0NWMwLTRjY2EtNDA0ZC1iMDJjLWQxZWU4NDM3ZWRhOC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU1MzVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0yODNiZTg1ZmFmZDQ2NjgyOTVkN2E4MGU1ZWUwMTFhNGYyMjc0MmQxZDJhZDYzYzc3YjEwMGIzMGI5ZThjYTVjJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.fseQ55IMb8LQpnJ3CVPNXtDm5QsA_ns8_hU5w0P5euc)

- 작업 시간이 긴 프로세스 **A**가 먼저 도착하면, 짧은 작업 시간을 가진 **B**, **C**는 **A**가 끝날 때까지 기다려야 한다.
- 이는 시스템의 전체적인 응답 시간과 반환 시간을 증가시키며, 자원 활용률을 저하시킨다.

### RR (Round Robin)

**RR (Round Robin)** 스케줄링은 모든 작업들에게 동일한 **타임 슬라이스(Time Slice)**를 부여하고, 시간이 끝나면 다음 작업으로 순차적으로 넘어가는 방식이다. 이 방식은 각 프로세스에게 공평한 CPU 시간을 제공하여 응답 시간을 개선한다.

하지만 RR을 사용할 때는 **타임 슬라이스를 적절하게 설정**해야 한다. 타임 슬라이스가 너무 짧으면 **컨텍스트 스위치(Context Switch)**가 자주 발생하여 시스템 성능이 저하된다. 반대로 타임 슬라이스가 너무 길면 RR의 장점이 감소하여 FIFO와 비슷한 문제가 발생한다.

예를 들어:

![RR 스케줄링 예시](https://private-user-images.githubusercontent.com/102043957/365363003-c922b30e-1236-4366-bc24-23af5475be75.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA0MzUsIm5iZiI6MTcyNjk3MDEzNSwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1MzYzMDAzLWM5MjJiMzBlLTEyMzYtNDM2Ni1iYzI0LTIzYWY1NDc1YmU3NS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU1MzVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zODg3ZjAwM2E4Y2NiZDEyNWY0YTRkMGJmY2EzMzc5ZjZmYWY3OTAxMTRhYjc1N2M4Yzg2MDJmMTVlNDc3OGQ4JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.lDwxenu_itOCcu3M-16qBBaoImK64wVXYZsNYGwnn4s)

- 각 프로세스는 타임 슬라이스만큼 번갈아가며 실행되기 때문에 평균적인 **응답 시간**이 짧다.
- 하지만 **SJF(Shortest Job First)** 나 **STCF(Shortest Time-to-Completion First)** 에 비해서는 **반환 시간**이 좋지 못하다.

# 결론

- **FIFO 스케줄링**은 구현이 단순하지만 **컨보이 효과**로 인해 단점이 있다.
- **RR 스케줄링**은 응답 시간을 개선하지만, 타임 슬라이스의 설정이 성능에 큰 영향을 미친다.
- 시스템의 특성과 목표에 따라 적절한 스케줄링 알고리즘을 선택해야 한다.

이러한 스케줄링 알고리즘들의 이해는 운영체제의 성능 최적화와 사용자 경험 향상에 필수적이다.
