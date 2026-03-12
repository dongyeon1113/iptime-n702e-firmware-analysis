# check_password_cache 분석

## 1. 함수 역할
`check_password_cache`는 기존에 캐시에 저장되어 있는 ID와 passwd를 사용자가 입력한 ID와 passwd와 비교하는 역할을 한다.

## 2. 주요 동작
먼저 ID를 비교해서 사용자가 입력한 ID가 맞는지 확인한다.  
이후 ID가 일치하면 `password_cache`와 입력한 passwd가 빈 문자열인지 아닌지를 확인한 뒤, passwd를 비교한다.

## 3. 핵심 코드
~~~c
bool check_password_cache(char *param_1, char *param_2)
{
  int iVar1;
  bool bVar2;
  
  iVar1 = strcmp(param_1, id_cache);
  bVar2 = true;
  if ((iVar1 == 0) && ((password_cache != '\0' || (bVar2 = false, *param_2 != '\0')))) {
    iVar1 = strcmp(param_2, &password_cache);
    bVar2 = iVar1 != 0;
  }
  return bVar2;
}
~~~

## 4. 분석 내용
이 함수는 직접 인증 데이터를 비교하는 핵심 로직이다.  
먼저 사용자가 입력한 ID를 `strcmp`를 통해 cache에 저장되어 있는 ID와 비교한다.  
`strcmp`는 문자열이 같으면 0을 반환하고, 다르면 0이 아닌 값을 반환한다.

이후 ID가 맞는 경우에만 사용자 passwd와 cache에 저장되어 있는 passwd를 비교한다.  
비밀번호 비교 결과, passwd가 같으면 `strcmp`의 반환값은 0이 되고 `bVar2`에는 false가 저장된다.  
반대로 passwd가 다르면 0이 아닌 값이 나오므로 `bVar2`에는 true가 저장된다.

즉 이 함수는 입력한 ID 또는 passwd가 cache 값과 **일치하지 않는지**를 확인하는 함수로 볼 수 있다.

정리하면 다음과 같다.

- ID와 passwd가 모두 일치하면 `false`
- ID 또는 passwd가 일치하지 않으면 `true`

따라서 이 함수의 반환값은 성공 여부보다는 **불일치 여부**를 나타낸다.

## 5. 보안상 의미
이 함수는 실제 ID와 passwd를 비교하는 핵심 지점이기 때문에 인증 로직 분석에서 중요하다.  
특히 반환값이 일반적인 성공/실패 함수와 반대로 보일 수 있으므로 상위 함수에서 이 값을 어떻게 해석하는지 같이 봐야 한다.

현재 코드만 보고 바로 인증 우회가 가능하다고 단정할 수는 없지만
만약 상위 분기에서 반환값을 잘못 해석하거나 특정 조건에서 비교가 건너뛰어진다면 인증 로직에 문제가 생길 가능성도 있어 보인다.  
그래서 이후에는 `check_password_cache`의 반환값이 상위 함수에서 어떻게 사용되는지 추가로 확인할 필요가 있다.
