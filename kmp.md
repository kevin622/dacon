# KMP 알고리즘

문자열 찾기 알고리즘! 전에 `ctrl f` 가 사용하는 알고리즘이라고 해서 그냥 들고 왔습니다...

일단 전체코드

```python
'''
출처 : https://devbull.xyz/python-kmp-algorijeumeuro-munjayeol-cajgi/
근데 이 블로그 사진 순서가 좀 뒤죽박죽이어서 잘 봐야됨 ㅠㅠㅠ
설명은 잘 되어 있음
'''
def computeLPS(pat, lps):
    leng = 0  # length of the previous longest prefix suffix

    # 항상 lps[0]==0이므로 while문은 i==1부터 시작한다.
    i = 1
    while i < len(pat):
        # 이전 인덱스에서 같았다면 다음 인덱스만 비교하면 된다.
        if pat[i] == pat[leng]:
            leng += 1
            lps[i] = leng
            i += 1
        else:
            # 일치하지 않는 경우
            if leng != 0:
                # 이전 인덱스에서는 같았으므로 leng을 줄여서 다시 검사
                leng = lps[leng-1]
                # 다시 검사해야 하므로 i는 증가하지 않음
            else:
                # 이전 인덱스에서도 같지 않았다면 lps[i]는 0 이고 i는 1 증가
                lps[i] = 0
                i += 1

def KMPSearch(pat, txt):
    M = len(pat)
    N = len(txt)

    lps = [0]*M

    # Preprocess the pattern
    computeLPS(pat, lps)

    i = 0  # index for txt[]
    j = 0  # index for pat[]
    while i < N:
        # 문자열이 같은 경우 양쪽 인덱스를 모두 증가시킴
        if pat[j] == txt[i]:
            i += 1
            j += 1
        # Pattern을 찾지 못한 경우
        elif pat[j] != txt[i]:
            # j!=0인 경우는 짧은 lps에 대해 재검사
            if j != 0:
                j = lps[j-1]
            # j==0이면 일치하는 부분이 없으므로 인덱스 증가
            else:
                i += 1

        # Pattern을 찾은 경우
        if j == M:
            print("Found pattern at index " + str(i-j))
            # 이전 인덱스의 lps값을 참조하여 계속 검색
            j = lps[j-1]



# 조금 더 긴 텍스트
txt = "ABABDABACDABABCABAB"
pat = "ABABCABAB"
# 본문에서 다룬 예제
#txt = 'ABXABABXAB'
#pat = 'ABXAB'
KMPSearch(pat, txt)

# This code is contributed by Bhavya Jain
```

## LPS

Longest proper Prefix which is Suffix

주어진 문자열의 substring에서 접두어(prefix)와 접미어(suffix)가 일치하는 최대 길이를 나타내는 배열

만약 주어진 문자열이 `ABXAB` 라고 할 때, LPS는 다음과 같이 구함

| index | substring | lps[index] | 설명                                  |
| ----- | --------- | ---------- | ------------------------------------- |
| 0     | A         | 0          | 접두어와 접미어가 존재하지 않음       |
| 1     | AB        | 0          | 접두어와 접미어가 일치하지 않으므로 0 |
| 2     | ABX       | 0          | 접두어와 접미어가 일치하지 않으므로 0 |
| 3     | ABXA      | 1          | A가 일치하므로 1                      |
| 4     | ABXAB     | 2          | AB가 일치하므로 2                     |

이를 배열로 표현하면

```python
lps = [0, 0, 0, 1, 2]
```



그래서 이거 어떻게 구하는데?

### 요렇게 구하지

```python
def computeLPS(pat, lps):
    leng = 0  # length of the previous longest prefix suffix

    # 항상 lps[0]==0이므로 while문은 i==1부터 시작한다.
    i = 1
    while i < len(pat):
        # 이전 인덱스에서 같았다면 다음 인덱스만 비교하면 된다.
        if pat[i] == pat[leng]:
            leng += 1
            lps[i] = leng
            i += 1
        else:
            # 일치하지 않는 경우
            if leng != 0:
                # 이전 인덱스에서는 같았으므로 leng을 줄여서 다시 검사
                leng = lps[leng-1]
                # 다시 검사해야 하므로 i는 증가하지 않음
            else:
                # 이전 인덱스에서도 같지 않았다면 lps[i]는 0 이고 i는 1 증가
                lps[i] = 0
                i += 1
```

`leng` 라는 변수 사용, 이전의 가장 긴 접두어-접미어 길이

| lps    | 0    | 1    | 2    | 3    | 4    | pattern | i    | leng |
| ------ | ---- | ---- | ---- | ---- | ---- | ------- | ---- | ---- |
| lps[i] | 0    |      |      |      |      | ABXAB   | 1    | 0    |

위 상황에서 시작! 길이가 2 이상인 경우에만 생각하면 되므로 i는 1부터 생각, lps[0]은 0으로 초기화
위 경우에서 A와 B가 다르므로, 즉 `pat[leng]`와 `pat[i]`가 다르므로 lps[1]에 0 넣고 i += 1 함

| lps    | 0    | 1    | 2    | 3    | 4    | pattern | i    | leng |
| ------ | ---- | ---- | ---- | ---- | ---- | ------- | ---- | ---- |
| lps[i] | 0    | 0    |      |      |      | ABXAB   | 2    | 0    |

i=1일 때와 동일. 접두어 접미어 같은 게 없으므로 lps[2] = 0, i += 1

| lps    | 0    | 1    | 2    | 3    | 4    | pattern | i    | leng |
| ------ | ---- | ---- | ---- | ---- | ---- | ------- | ---- | ---- |
| lps[i] | 0    | 0    | 0    |      |      | ABXAB   | 3    | 0    |

이 상황부터 중요함!
접두어와 접미어가 같으므로, 즉 `pat[leng]`와 `pat[i]`가 같으므로 다음 코드가 실행이 된다.

```python
        if pat[i] == pat[leng]:
            leng += 1
            lps[i] = leng
            i += 1
```

그 결과

| lps    | 0    | 1    | 2    | 3    | 4    | pattern | i    | leng |
| ------ | ---- | ---- | ---- | ---- | ---- | ------- | ---- | ---- |
| lps[i] | 0    | 0    | 0    | 1    |      | ABXAB   | 4    | 1    |

마지막 제일 중요! 지금 pat[0] 과 pat[3]이 같다는 것을 알고 있기 때문에 pat[1]이 pat[4]와 같은지만 확인하면 된다.

pat[1] == pat[4]이므로 그 결과는 

| lps    | 0    | 1    | 2    | 3    | 4    | pattern | i    | leng |
| ------ | ---- | ---- | ---- | ---- | ---- | ------- | ---- | ---- |
| lps[i] | 0    | 0    | 0    | 1    | 2    | ABXAB   | 5    | 2    |

i가 패턴의 길이와 같아졌으므로 반복문이 끝난다!

## KMP Search

- pattern을 찾고 싶은 text에서 둘다 0번 인덱스에서 시작

| txt  | A    | B    | X    | A    | B    | A    | B    | X    | A    | B    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| pat  | A    | B    | X    | A    | B    |      |      |      |      |      |

패턴 발견!

- 패턴을 찾으면 접두어와 접미어가 일치하는 부분만큼 이동

lps[4]의 값이 2이므로 pat[2] 값 이용한다는 뜻

| txt  | A    | B    | X    | A    | B(여기까지 패턴 확인) | A(확인 못한 부분) | B    | X    | A    | B    |
| ---- | ---- | ---- | ---- | ---- | --------------------- | ----------------- | ---- | ---- | ---- | ---- |
| pat  |      |      |      | A    | B                     | X(pat[4])         | A    | B    |      |      |

- 이동 후 패턴이 안 맞으면 패턴의 이전 인덱스 값인 1에 해당하는 LPS 값을 사용

lps[1]의 값이 0 이므로 pat[0] 이용한다는 뜻

| txt  | A    | B    | X    | A    | B    | A         | B    | X    | A    | B    |
| ---- | ---- | ---- | ---- | ---- | ---- | --------- | ---- | ---- | ---- | ---- |
| pat  |      |      |      |      |      | A(pat[0]) | B    | X    | A    | B    |

코드로 어떻게 쓰지...ㅠㅠ

### 요렇게 쓰면 됩니당

```python
def KMPSearch(pat, txt):
    M = len(pat)
    N = len(txt)

    lps = [0]*M

    # Preprocess the pattern
    computeLPS(pat, lps)

    i = 0  # index for txt[]
    j = 0  # index for pat[]
    while i < N:
        # 문자열이 같은 경우 양쪽 인덱스를 모두 증가시킴
        if pat[j] == txt[i]:
            i += 1
            j += 1
        # Pattern을 찾지 못한 경우
        elif pat[j] != txt[i]:
            # j!=0인 경우는 짧은 lps에 대해 재검사
            if j != 0:
                j = lps[j-1]
            # j==0이면 일치하는 부분이 없으므로 인덱스 증가
            else:
                i += 1

        # Pattern을 찾은 경우
        if j == M:
            print("Found pattern at index " + str(i-j))
            # 이전 인덱스의 lps값을 참조하여 계속 검색
            j = lps[j-1]
```

