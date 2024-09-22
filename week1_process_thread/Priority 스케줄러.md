# 질문: Priority가 존재하는 정책들의 특징과 각각의 장점은 무엇인가?

### SJF (Shortest Job First)

**SJF**는 CPU 사용 시간이 짧은 프로세스를 먼저 스케줄링하는 방식이다. 이는 시스템 전체의 평균 대기 시간과 반환 시간을 최소화하는 데 효과적이다. 그러나 **비선점 스케줄링 방식**이기 때문에 기존에 실행되고 있는 프로세스의 실행 시간이 길 경우, 뒤따르는 짧은 작업들이 모두 대기해야 할 수 있다. 또한, 실행 시간이 긴 작업의 경우 **기아 현상**이 발생할 수 있다.

![SJF 스케줄링 예시](https://private-user-images.githubusercontent.com/102043957/365364267-23ce3af6-ca01-4607-8512-3b39ce663593.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1MzY0MjY3LTIzY2UzYWY2LWNhMDEtNDYwNy04NTEyLTNiMzljZTY2MzU5My5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04ZGQ1N2FiZGFmNTlkNmFiODZmOGFjYjRiODkxOGJiYWY0ZGFkNTBlM2Q4ZjZlMDEwOGM0Y2UxZDI0YjZhNzA2JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.iUOiPk43UKx2OjsjOCBzUj6q49FA5PFvefbfY13V5gE)

사용 중인 CPU를 선점할 수 없기 때문에 **프로세스 A**가 먼저 실행 중일 때, **프로세스 B**와 **C**가 도착하면 실행 시간이 짧음에도 불구하고 **A**의 실행이 끝나기를 기다려야 한다.

### STCF (Shortest Time-to-Completion First)

**STCF**는 SJF와 마찬가지로 CPU 사용 시간이 짧은 프로세스를 먼저 실행하지만, **선점 스케줄링**을 가능하게 만든 방식이다. 실행 시간이 긴 작업이 처리되는 도중에도 강제로 CPU를 빼앗고, 작업 시간이 짧은 프로세스를 실행시킬 수 있다. 하지만 **STCF** 역시 우선순위가 낮은 작업은 기아 현상이 발생할 수 있다.

![STCF 스케줄링 예시](https://private-user-images.githubusercontent.com/102043957/365364289-9fe9af0a-91e7-4a61-8f82-509565ffb490.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1MzY0Mjg5LTlmZTlhZjBhLTkxZTctNGE2MS04ZjgyLTUwOTU2NWZmYjQ5MC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1mNzUwNjFkYzM5OTlkY2MxZjRhYTBhMWRjNTNmZmY5MDBhOWM4ZmY5ZGM2NWYyMmM3MGU1NDc0Njk2N2JhZmE0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.K6vyl913uDk6qj-q9WzuQMATaBFNk9fdMRIBw7sF8GQ)

**STCF**의 경우 CPU를 강제로 선점할 수 있기 때문에, **B**, **C**를 먼저 실행하고 실행 시간이 긴 **A**를 나중에 실행할 수 있다.

### SJF와 STCF의 실행 시간 예측

**SJF**와 **STCF**는 각 작업의 CPU 사용 시간을 미리 알고 있다고 가정한다. 하지만 실제로 이는 불가능하다. 따라서 다음 CPU 실행 시간은 일반적으로 측정된 이전의 CPU 실행 시간들의 길이를 **지수 평균**하여 예측한다.

![CPU 실행 시간 예측 공식](https://private-user-images.githubusercontent.com/102043957/365413982-e4f3f06b-ff1c-4bc4-a4a7-059bf89d85ff.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1NDEzOTgyLWU0ZjNmMDZiLWZmMWMtNGJjNC1hNGE3LTA1OWJmODlkODVmZi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zOTdiMjYwZDAyOTY5NTgyOGExOGY5OTFiODkwNWY4ODQxYjRlMWU0MzQzNzA4NDJlYmI4MzhkNTNhYjg1ZDdjJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.7lYYWeR1qm26tlPSnKggGf1TJ57oX3l8CrNJ2uH8K-s)

![지수 평균 예시](https://private-user-images.githubusercontent.com/102043957/365414296-c369b10e-865e-4ed9-a7fe-9a50d454898f.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1NDE0Mjk2LWMzNjliMTBlLTg2NWUtNGVkOS1hN2ZlLTlhNTBkNDU0ODk4Zi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04ZDZmMDk3ZDk4NWY4NWYzN2Q2OGU5N2MxZThmNTI2ZTAwZDY0YmRhNjhjOTZlYjExN2FmZDliZGFjNjgzZjBjJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.IvQO7892Zdviyl3VDHzJf1Rvo9QY64DBNym2bGwyNEo)

일반적으로 **가중치 α**는 **1/2**로 설정한다. 이는 다음 CPU 실행 시간을 예측할 때, 이전 실제 CPU 버스트와 예측치의 평균을 사용하겠다는 의미이다.

# MLFQ (Multi-Level Feedback Queue)

**MLFQ**는 스케줄링 큐를 여러 개 두고, 각 큐 사이에 우선순위를 부여하여 스케줄링하는 방식이다. 아래와 같은 **스케줄링 규칙**을 가진다.

- **규칙 1**: 우선순위가 높은 프로세스들을 먼저 수행한다.
- **규칙 2**: 작업들이 같은 우선순위를 갖는다면 **라운드 로빈**으로 수행한다.
- **규칙 3**: 새로운 프로세스가 시스템에 들어오면 가장 높은 우선순위를 부여한다.
- **규칙 4**: 작업은 모든 우선순위에서 주어진 **타임 슬라이스(time slice)** 를 모두 사용하면 우선순위가 감소한다.
- **규칙 5**: 일정 시간 후 시스템의 모든 작업을 우선순위가 가장 높은 큐로 이동한다.

## 우선순위 변경 방법 (How To Change Priority)

처음 프로세스가 **Ready** 상태가 되면서 스케줄링 큐에 들어오면 가장 높은 우선순위를 가진 큐로 들어간다. 그리고 타임 슬라이스만큼 실행한 후에는 그 다음 우선순위를 가진 큐로 이동한다. 만약 작업이 **CPU-bound 프로세스**인 경우, 빠르게 타임 슬라이스를 다 소모하고 우선순위가 빠르게 내려갈 것이고, **I/O-bound 프로세스**는 높은 우선순위를 길게 유지할 것이다.

![MLFQ 우선순위 변경](https://private-user-images.githubusercontent.com/102043957/365420502-acdc6ecd-39a2-4c27-b8f8-9b8a664fad58.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1NDIwNTAyLWFjZGM2ZWNkLTM5YTItNGMyNy1iOGY4LTliOGE2NjRmYWQ1OC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0xMTUwYjAzZWMwZWIwYzk3YTAxNDhkNTM4OTk2YjA5ZDZjYzYxZDk0NWFhNjFlNGE3MDJkYWNkZGM0N2Y0OWI5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.OMDej-y9CmDdTE-aQypF7yAqteXY7Kv-PlRXih6F7UQ)

하지만 이 상태로는 두 가지 문제가 있다.

#### 1. 스케줄러 속이기 (Gaming the Scheduler)

교묘하게 타임 슬라이스보다 약간 짧게 실행하면서 높은 우선순위를 유지할 수 있다. 이를 예방하기 위해 스케줄러는 타임 슬라이스 시간을 **누적하여 추적**함으로써 문제를 해결할 수 있다.

#### 2. 기아 현상 (Starvation)

인터랙티브 작업이 많을 경우 **CPU-bound 프로세스**는 계속 실행되지 못할 수 있다. 이는 **우선순위 부스팅(priority boost)**으로 해결할 수 있다.

## 다른 타임 슬라이스 설정 (Different Time Slice)

위의 규칙에 따르면 자동으로 **I/O-bound 프로세스**는 높은 우선순위 큐에 있게 되고, **CPU-bound 프로세스**는 낮은 우선순위를 가지게 된다. 만약 타임 슬라이스를 모든 우선순위 큐에 대해서 동일하게 설정하면, CPU-bound 프로세스가 처리될 때 **컨텍스트 스위치(Context Switch)** 가 많이 발생하여 오버헤드가 커질 수 있다. 이를 보완한 규칙이 **규칙 4번**이다. CPU-bound 프로세스가 주로 위치하게 되는 낮은 우선순위 큐의 타임 슬라이스를 길게 함으로써 컨텍스트 스위치에 따른 오버헤드를 줄일 수 있다.

![다른 타임 슬라이스 설정](https://private-user-images.githubusercontent.com/102043957/365420192-b38e5443-14c4-4b82-81a2-d67da0709578.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1NDIwMTkyLWIzOGU1NDQzLTE0YzQtNGI4Mi04MWEyLWQ2N2RhMDcwOTU3OC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01NmYzMTcyODJmNTFiNzNjZDJkZDYxMDZiOTU1Yzg4ZjM0ZDg1NzE3YmU0NjViMTM3ZTBmOTc0MTYzMmQ5YWQyJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.0b6OuELMlP4mCSRqSkJaUFtSKkWEy6pk-ymrql95qq4)

## 우선순위 부스팅 (The Priority Boost)

프로세스는 시간이 지남에 따라 특정 시점에 I/O를 많이 할 수도 있고, CPU를 많이 사용할 수도 있다. 즉, 특정 시점에 CPU를 많이 사용한다고 해서 프로세스가 종료될 때까지 CPU를 많이 쓰는 것이 아니다. 이를 보완하기 위한 규칙이 **규칙 5번**이다.

규칙 5번이 없으면 한 번 우선순위가 내려가면, 이후에 I/O 작업을 주로 하는 구간으로 가더라도 이를 반영할 수 없다.

![우선순위 부스팅 예시](https://private-user-images.githubusercontent.com/102043957/365420409-20b9dc7f-a05f-4aec-b22f-633fc66c5d7c.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjY5NzA2MjIsIm5iZiI6MTcyNjk3MDMyMiwicGF0aCI6Ii8xMDIwNDM5NTcvMzY1NDIwNDA5LTIwYjlkYzdmLWEwNWYtNGFlYy1iMjJmLTYzM2ZjNjZjNWQ3Yy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwOTIyJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDkyMlQwMTU4NDJaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1lMjRhMWVhMzliOTVkMzRmMGE1ZTNkMDllZGZmZjQwZmQ1M2VlY2Y3ZTc2YWUwNjYyODNhOGQ3ZjQzM2U0ZTEzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.480bfg06hBsMAErH_sCW7GLtqnWf5f0k4gdJE7n1q1o)
