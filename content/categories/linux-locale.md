---
title: "Linux locale 삽질"
date: 2023-08-12T00:00:00+09:00
tags: ["linux","삽질"]
author: "ors"
description: "linux locale 과 관련된 삽질기"
---

최근 진행 중인 project 에 [cucumber](https://cucumber.io/) 를 적용했다.

![cucumber-sample](/images/cucumber-sample.png)


> cucumber는 Test framework 중 하나로, 문서화에 강점이 있다.

Test 가 스펙 문서의 역할을 할 수 있을 것 같아서 검토를 시작했다.
마침 적용하기 좋은 스펙이 있어서 작업 후 PR 을 올렸고, develop 코드에 적용 시킬 수 있었다.
cucumber 를 설정하는 과정에서 재밌는 이슈를 겪었는데, 이 포스트는 `한글 파일 + CI` 와 관련된 내용이다.

### 환경 공유

이슈를 정리하기 앞서, 내가 진행하고 있는 프로젝트 환경은 아래와 같다.
kotlin + spring 으로 프로젝트가 구성되어 있으며, [gitaction](https://docs.github.com/ko/actions)을 통한 CI 를 사용하고 있다.
또한 gitaction runner 는 k8s 위에 실행되고 있다.

> PR 을 올리면 k8s 에서 build 를 수행하고, 해당 결과(reports)를 sonarqube로 전송한다.

또한 cucumber 의 feature file 은 한글로 작성되어 관리되고 있다.

## 이슈

cucumber 설정을 하면서, 겪은 이슈 중 가장 기억에 남는 에러는 아래와 같다.

```text
Cannot fingerprint input file property 'rootSpec$1': Failed to create MD5 hash for file '?????????????????????_???????????????_?????????_?????????_??????.feature' as it does not exist.
```

한글로 작성한 파일이 존재하지 않는다는 에러로, local(mac)에선 재현되지 않았으며, k8s 의 runner 서버에서만 재현이 됐다.

> 진짜 파일이 없는 건 아닌가 싶어서, runner 에서 bash 로 파일을 확인해봤으나, 파일은 존재했고 접근도 잘 됐다.

`제 컴에선 되는데요 ..? 🤔`

### 소설 1. gradle 문제일 것이다.

당장 에러를 만났을 땐, runner 쪽 `.gradle` 에 문제가 생겼거나 clean 이 제대로 수행되지 않았을거라 생각했다.

> 동일한 이슈는 아니지만, 유사한 에러가 [stackoverflow](https://stackoverflow.com/questions/48632019/gradle-build-error-on-windows-failed-to-create-md5-hash-for-file) 에도 올라오기도 했어서, gradle 이슈라고 생각했다.

때문에, k8s의 .gradle 을 삭제하고 다시 clean / build 를 수행했으나 시간만 더 걸릴뿐 동일한 에러가 나왔다.

### 소설 2. java 혹은 gradle 에 UTF8 설정이 안되어있을 것이다.

(위에서 언급했듯) 한글로 작성된 파일만 못읽고 있어서, test 를 수행하는 gradle 혹은 java 에 encoding 이슈라고 추측했다.

> local 과 달리 k8s 에 설치된 java 의 encoding 이 UTF-8 이 아니라서 발생하고 있다고 생각했다.

gradle file 혹은 build.gradle 파일의 설정을 바꿔보았으나, 마찬가지로 동일한 에러가 발생했다.

```text
# gradle setting
org.gradle.jvmargs=-Dfile.encoding=UTF-8
```

```text
tasks.withType<JavaCompile> {
    options.encoding = "UTF-8"
}
```

### 소설 3. runner 쪽 문제일까 ?

~~이쯤에서 한글 파일을 포기하고 영어로 바꿀까 살짝 고민했었다.~~

앞선 추측 두개가 소설임을 확인하고 나서야, runner 쪽을 의심하기 시작했다.
`linux + 한글` 이라는 키워드로 구글링을 해보니, locale 설정을 확인하라는 답글을 찾을 수 있었고 k8s instance 설정을 확인했다.

```text
locale
LANG=
...
LC_ALL=POSIX
```

> 기억에 의존해서 정확하진 않으나, 대부분 설정이 공백이였다.

즉, runner 쪽 locale 문제였으며.. cli 를 통해 locale 을 ko_KR.UTF-8 로 설정하니 해당 이슈는 해결되었다.

> 여담으로 docker file 에 아래 명령어를 추가하여, instance 를 재배포했다.

```dockerfile
RUN localedef -f UTF-8 -i ko_KR ko_KR.UTF-8
ENV LANG=ko_KR.UTF-8 \
    LANGUAGE=ko_KR.UTF-8 \
    LC_ALL=ko_KR.UTF-8
```



