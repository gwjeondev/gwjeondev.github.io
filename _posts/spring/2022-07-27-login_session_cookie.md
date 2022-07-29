---
layout: single
title:  "쿠키와 세션 기본 - 로그인"
categories: spring
---
![https://velog.velcdn.com/images/won-developer/post/9d4b3828-8128-46a7-99db-6ef9c5e6de23/image.png](https://velog.velcdn.com/images/won-developer/post/9d4b3828-8128-46a7-99db-6ef9c5e6de23/image.png)

---

> 학습참조
인프런-스프링 MVC 2편 백엔드 웹 개발 활용 기술(김영한님)
> 

---

쿠키와 세션을 이용한 간단한 로그인을 구현해보자! 우선 쿠키와 세션이 무엇인지에 대해 간단하게 알아보자.

# ✅ 쿠키와 세션

## ✔ 쿠키(Cookie)

쿠키란 웹을 사용하는 유저들에게 편리함을 제공하거나, 이를 이용해 정보를 수집할 수 있는 브라우저에 저장하게 되는 작은 텍스트이다.
예를 들어 'Hello'라는 ID를 가지는 유저가 로그인후 잠시 사이트를 닫은후 다시 재접속하여도 로그인 상태를 유지하게끔 하는데, 이는 바로 쿠키를 이용한 것이다.
웹사이트에 접속하면 브라우저는 서버로 요청을 보내는데 서버에서는 해당 요청에 대한 응답에 쿠키를 만들어 브라우저로 보낸다. 해당 브라우저는 응답받은 쿠키를 쿠키저장소에 보관하게 되며, 이후 다시 유저가 웹사이트에 접속하게 되면 쿠키저장소에서 이전에 서버에서 발급해준 쿠키정보를 서버로 요청을 보내게 되는데, 서버에서는 이 쿠키값을 확인하여 재접속이더라도 계속 로그인 상태를 유지할수 있게끔 한다.

![https://images.velog.io/images/won-developer/post/4e041109-8226-4652-95b3-6687edbe1fa2/image.png](https://images.velog.io/images/won-developer/post/4e041109-8226-4652-95b3-6687edbe1fa2/image.png)

## ✔ 세션(Session)

세션이란 쿠키의 보안 이슈에 대해 이를 방지하고자 나온 개념이다. 쿠키만을 이용해서 로그인 또는 사용자를 식별하려면 매우 심각한 보안이슈가 일어난다. 쿠키에는 다음과 같은 여러가지 보안이슈가 있다.

### 🚩 쿠키의 보안 문제

- 쿠키 값은 임의로 변경할 수 있다.
    - 웹브라우저의 개발자모드에서 쿠키를 변경할 수 있는데, 클라이언트가 쿠키를 강제로 변경하면 다른 사용자인 것처럼 사용할 수 있다.
- 쿠키에 보관된 정보는 훔쳐갈 수 있다.
    - 만약 쿠키에 개인정보나, 신용카드 정보가 있다면? 이 정보는 브라우저의 쿠키저장소에 저장되고 네트워크 요청마다 서버로 전달된다. 나의 LOCAL PC의 쿠키가 털릴수도 있고, 네트워크 전송구간에서도 해커에게 털릴수가 있다.
    - 만약 해커가 사용자A의 쿠키를 탈취하여 자신의 브라우저에 쿠키를 입력후 접속하게 된다면 사용자A인 마냥 서비스를 이용할 수 있다.
- 해커가 쿠키를 한번 훔쳐가면 평생 사용할 수 있다.
    - 서버가 보관하고 있는 해당 쿠키에 대한 정보를 지우지 않는 이상은 무한대로 악의적인 요청을 할 수도 있다.

위와 같이 쿠키는 직접 추가, 변경이 가능할 뿐더러 해커가 탈취해가기도 매우 쉬운 환경에 놓여있다. 만약 사용자의 민감한 개인정보를 쿠키에 담을경우 심각한 보안문제가 발생할 것이다. 세션은 이를 위한 대안이다.

### 🚩 세션을 이용한 대안

- 쿠키에는 중요한 정보를 보관하지 않고, 서버에서는 예측 불가능한 임의의 토큰(랜덤값)을 쿠키로 생성하여 브라우저에게 응답하여야 한다. 여기서 예측 불가능이란 숫자 1다음은 2인것처럼 이러한 예측이 불가능 해야한다. 만약 예측이 가능할 경우 해커가 값을 예측하여 쿠키를 변경후 악의적인 접근을 시도 할 수 있다.
- 서버에서는 예측 불가능한 임의의 토큰(랜덤값)을 쿠키로 생성하여 클라이언트에게 응답하고 서버에서는 해당 임의의 토큰값을 가지는 쿠키를 전달받아 사용자의 정보를 매핑하여 관리한다.
- 만약 해커가 임의의 토큰(랜덤값)을 가지는 쿠키를 털어간다고 해도 일정한 시간이 지나면 사용할 수 없도록 서버에서는 해당 토큰값에 대해 유효시간을 설정하여 해당 유효시간이 지날경우 토큰정보를 삭제하고, 또한 해킹이 의심되는 경우 서버에서 해당 토큰을 강제로 제거한다.

이와 같이 사용자의 중요한 정보는 서버에서 관리하고 임의의 토큰(랜덤값)을 쿠키로 만들어 브라우저에게 응답후, 이 쿠키값을 가지고 클라이언트와 서버의 연결을 유지하는 방법을 세션이라고 한다.

![https://images.velog.io/images/won-developer/post/854202a9-9d44-4e9d-8cd1-409b5cd99627/image.png](https://images.velog.io/images/won-developer/post/854202a9-9d44-4e9d-8cd1-409b5cd99627/image.png)

여기까지 쿠키와 세션을 간단히 알아보았고 이제 구현으로 들어가보자.

---

# ✅ 실전 ㄱㄱ

직접 Session을 관리하는 SessionManager와 Servlet에서 제공하는 Session을 사용하여 두가지 방식으로 구현을 해볼 것이다. 해당 구현의 소스코드는 [https://github.com/gwjeondev/loginTest](https://github.com/gwjeondev/loginTest) 에서 확인할 수 있다!

디렉토리는 다음과 같다.

![https://images.velog.io/images/won-developer/post/76635b4f-6cbd-44b4-8f95-a1742cae0563/image.png](https://images.velog.io/images/won-developer/post/76635b4f-6cbd-44b4-8f95-a1742cae0563/image.png)

## ✔ 사전 요구사항

- 아이디를 hong-gildong, 패스워드를 1234로 가지는 회원을 미리 등록함
- localhost:8080에 접속시 session 체크후 로그인화면으로 이동 또는 home화면으로 이동
    - 로그인 화면
        
        ![https://images.velog.io/images/won-developer/post/292888d6-7c5a-413c-89b8-17c173edd04f/image.png](https://images.velog.io/images/won-developer/post/292888d6-7c5a-413c-89b8-17c173edd04f/image.png)
        
    - home 화면
        
        ![https://images.velog.io/images/won-developer/post/2456941e-d240-4ca5-87c8-5495f8b0a96f/image.png](https://images.velog.io/images/won-developer/post/2456941e-d240-4ca5-87c8-5495f8b0a96f/image.png)
        
- View: Thymeleaf
- Server: Spring

---

## ✔ SessionManager

### 🚩 로그인처리후 Session 발급

![https://images.velog.io/images/won-developer/post/d605d8bb-7298-4188-88c4-a4b6025772b1/image.png](https://images.velog.io/images/won-developer/post/d605d8bb-7298-4188-88c4-a4b6025772b1/image.png)

localhost:8080 접속시 로그인 페이지가 나온다. 로그인 ID와 비밀번호를 입력하여 접속하자. 회원 정보는 사전에 등록해두었다. 전송버튼 클릭시 아래 Controller로 요청된다.

### Controller

```java
@PostMapping("/login")
public String login(@ModelAttribute Member member, HttpServletResponse response) {
  Member loginMember = loginService.login(member.getMemberId(), member.getPassword()); //(1)

  if(loginMember == null) { //(2)
      return "redirect:/";
  }

  sessionManager.createSession(loginMember.getMemberId(), response); //(3)
  return "redirect:/"; //(4)
}
```

- (1): 입력된 회원ID와 Password를 가지는 회원이 있는지 확인한다. 올바른 정보인경우 Member객체가 반환되고, 아니라면 null이 반환된다.
- (2): null일 경우 올바른 정보가 입력되지 않았으므로 /로 redirect 시킨다.
- (3): 요청받은 정보가 올바른 회원정보임이 확인되었으니, 세션을 생성한다. MemberId와 response를 argument로 sessionManager.createSession를 호출한다.
    
    ### SessionManager
    
    ```java
    public void createSession(String value, HttpServletResponse response) {
        String token = UUID.randomUUID().toString(); //(1)
        store.put(token, value); //(2)
        Cookie cookie = new Cookie(SessionConst.sessionId, token); //(3)
        response.addCookie(cookie); //(4)
    }
    ```
    
    - (1): 예측불가한 임의의 token값을 생성한다. UUID.randomUUID()는 중복될 확률은 불가능하다고 봐도 무방하고 자세한건 따로 알아보자.
    - (2): (1)에서 생성한 token값을 Key로, Parameter로 전달받은 memberId를 Value로 Store Map(세션 저장소)에 put한다.
    - (3): SessionConst.sessionId는 미리 interface로 생성해둔 상수이다. SessionConst.sessionId를 Key로, (1)에서 생성한 token을 Value로 하여 Cookie를 생성한다.
    
    ```java
    public interface SessionConst {
        String sessionId = "LOGIN_MEMBER";
    }
    ```
    
    - (4): response에 Cookie를 등록한다.
- (4): 정상적으로 세션저장소에 회원정보를 등록하고, response에도 쿠키에 세션정보를 실어 등록하였으므로 /으로 redirect한다.

---

### Controller

```java
@GetMapping("/")
public String home(HttpServletRequest request, Model model) {
    String memberId = sessionManager.getSession(request); //(1)
    if(memberId == null) { //(2)
        return "login";
    }
    Optional<Member> findMemberOptional = memberRepository.findByMemberId(memberId); //(3)
    Member member = findMemberOptional.orElse(null); //(4)
    if(member == null) { //(4)
        return "login";
    }
    model.addAttribute("member", member); //(5)
    return "home"; //(5)
}
```

URL /요청을 처리하는 Controller이다.

- (1): 세션정보를 가져오기 위하여 request를 argument로 sessionManager.getSession를 호출한다.
    
    ### SessionManager
    
    ```java
    public String getSession(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request); //(1)
        if(sessionCookie == null) { //(2)
            return null;
        }
        return store.get(sessionCookie.getValue()); //(3)
    }
    ```
    
    - (1): request요청의 Cookie에 서버에서 관리되는 쿠키정보가 있는지 찾는다. reqeust를 argument로 findCookie를 호출한다.
        
        ```java
        public Cookie findCookie(HttpServletRequest request) {
            //request 요청에 cookie가 없을 경우 null
            if(request.getCookies() == null) { //(1)
                return null;
            }
            return Arrays.stream(request.getCookies()) //(2)
                    .filter(cookie -> cookie.getName().equals(SessionConst.sessionId))
                    .findFirst()
                    .orElse(null);
        }
        ```
        
        - (1): request 요청에 Cookie가 없을 경우 null을 return 한다.
        - (2): request 요청에 Cookie중 서버에서 관리하는 Session Key인 상수`SessionConst.sessionId` 즉 `LOGIN_MEMBER`가 있는지 찾고 있다면 해당 Cookie를 return 한다.
    - (2): (1)에서 유효한 쿠키를 찾지못하고 return 받은 sessionCookie가 null이라면 null을 return 한다.
    - (3): 유효한 쿠키를 찾았을 경우 Cookie의 Value(token값)을 통하여 Store Map(세션저장소)에서 memberId를 찾아온다.
- (2): (1)에서 유효한 Session을 찾지 못한 경우 mebmerId는 null이 되며, login 페이지로 이동시킨다.
- (3): (1)에서 유효한 Session을 통해 정상적인 memberId를 반환받았을 경우 memberRepository에서 member정보를 찾는다.
- (4): memberId를 통하여 member를 찾지 못했을 경우 member는 null이 되며, login 페이지로 이동시킨다.
- (5): model에 member를 추가하여 home 페이지로 이동시킨다.

---

아래는 모든 과정이 정상적으로 마무리되고, home페이지로 이동한 화면이다.

![https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png](https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png)

정상적으로 login된 home화면을 확인할 수 있다.

![https://images.velog.io/images/won-developer/post/d22d3c02-1567-4604-904a-21c3931c7289/image.png](https://images.velog.io/images/won-developer/post/d22d3c02-1567-4604-904a-21c3931c7289/image.png)

F12 개발자모드로 접속해 등록된 Cookie를 확인해보자! 앞으로 다시 사이트에 접속하더라도 이 Cookie에 등록된 임의의 Token값을 통해 로그인상태를 유지할 것이다.

---

### 🚩 로그아웃을 통한 Session 무효화 처리

로그아웃을 통해 서버의 세션저장소에 등록된 정보를 삭제해보자.

![https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png](https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png)

home 화면에서 로그아웃 버튼을 클릭하면 /logout url로 요청된다.

### Controller

```java
@PostMapping("logout")
public String logout(HttpServletRequest request) {
    sessionManager.sessionExpire(request); //(1)
    return "redirect:/"; //(2)
}
```

- (1): 세션정보를 삭제하기 위하여 request를 argument로 sessionManager.sessionExpire를 호출한다.
    
    ### SessionManager
    
    ```java
    public void sessionExpire(HttpServletRequest request) {
        Cookie sessionCookie = findCookie(request); //(1)
        if(sessionCookie != null) { //(2)
            store.remove(sessionCookie.getValue()); //(2)
        }
    }
    ```
    
    - (1): request요청의 Cookie에 서버에서 관리되는 쿠키정보가 있는지 찾는다. reqeust를 argument로 findCookie를 호출한다. findCookie 메서드에 대해서는 위에서 설명했으니 참고하자.
    - (2): 만약 유효한 sessionCookie를 찾았을경우 Cookie의 Value(token값)을 통하여 store Map(세션저장소)에서 세션을 삭제한다.
- (2): /으로 redirect 한다. /요청을 처리하는 Controller에서는 다시 reqeust요청에 Cookie가 있는지 확인후 세션이 삭제되었으므로 최종적으로 login페이지로 이동할 것이다.

---

지금까지 SessionManager를 직접 만들어 처리하는 방식에 대해 알아보았고 로그아웃이 정상적으로 진행되어 세션정보가 삭제되었다면 다시 localhost:8080으로 접속하여도 로그인상태를 유지에 실패할 것이다.

지금까지 성공적으로 쿠키와 세션을 이용하여 로그인을 구현하였는데, 한가지 문제점이 있다. 세션정보를 서버에서 삭제하는 일은 사용자가 로그아웃 버튼을 눌렀을때만 발생한다. 일반적으로 사용자가 웹사이트를 떠날때 브라우저를 종료하지 로그아웃 버튼을 직접 클릭하는 경우는 드물다. 이렇게 된다면 해당 사용자에 대한 세션정보는 서버에 계속 유지될것이고 또한 만약 해커가 브라우저의 쿠키에서 세션Key를 탈취할 경우 보안문제로 이어질수 있다.

즉 이러한 문제를 해결하기 위해선 시간 간격을 두어 얼마의 시간만큼 해당 세션정보를 가지는 request 요청이 없을 경우, 서버의 세션정보를 지워야한다. 예시로 A사용자가 30분간 웹사이트를 이용하지 않는 경우 서버의 세션정보를 삭제 해버리는 것이다. 말로 들었을땐 어려워 보이는 구현이지만 Servlet에서 제공하는 Session기능을 이용할 경우 매우 편리하게 구현할 수 있다. 이뿐만 아니라 지금까지 SessionManager를 통해 구현했던 쿠키와 세션기능을 코드 몇줄로 매우 편리하게 축약할 수 있다.

지금부터 Servlet이 제공하는 Session을 알아보자.

---

## ✔ Servlet Session

### 🚩 로그인처리후 Session 발급

![https://images.velog.io/images/won-developer/post/d605d8bb-7298-4188-88c4-a4b6025772b1/image.png](https://images.velog.io/images/won-developer/post/d605d8bb-7298-4188-88c4-a4b6025772b1/image.png)

localhost:8080 접속시 로그인 페이지가 나온다. 로그인 ID와 비밀번호를 입력하여 접속하자. 회원 정보는 사전에 등록해두었다. 전송버튼 클릭시 아래 Controller로 요청된다.

Controller를 확인하기 전에 reqeust.getSession()이라는 메서드가 사용될것인데, 어떠한 기능을 수행하는지 정리하고 가자. request.getSession()는 세션을 생성한다.

- argument로는 true또는 false가 오는데, 생략하면 default는 true이다.
- true일 경우 세션이 없다면 세션을 생성해서 return하고 세션이 있다면 세션을 return한다.
- false일 경우 세션이 있다면 세션을 return하고 없다면 null을 return한다.

### Controller

```java
@PostMapping("/login")
public String login(@ModelAttribute Member member, HttpServletRequest request) {
    Member loginMember = loginService.login(member.getMemberId(), member.getPassword()); //(1)
    if(loginMember == null) { //(2)
        return "redirect:/"; //(2)
    }
    HttpSession session = request.getSession(); //(3)
    session.setAttribute(SessionConst.sessionId, loginMember.getMemberId()); //(4)
    return "redirect:/"; //(4)
}
```

- (1): 입력된 회원ID와 Password를 가지는 회원이 있는지 확인한다. 올바른 정보인경우 Member객체가 반환되고, 아니라면 null이 반환된다.
- (2): null일 경우 올바른 정보가 입력되지 않았으므로 /로 redirect 시킨다.
- (3): 요청받은 정보가 올바른 회원정보임이 확인되었으니, request.getSession()으로 세션을 생성한다. login을 요청하는 시점에는 세션이 없다고 판단되니 argument를 default로 놔둔다.(true)
- (4): (3)에서 생성된 session에 세션정보를 추가한다. 즉 세션저장소에 사용자를 식별할 수 있는 Key 상수 `SessionConst.sessionId` 즉 `LOGIN_MEMBER`와 사용자의 memberId를 저장한후 /으로 redirect 한다.

![https://images.velog.io/images/won-developer/post/e8328f5d-fb3b-49c3-9597-06ddb3adc0c9/image.png](https://images.velog.io/images/won-developer/post/e8328f5d-fb3b-49c3-9597-06ddb3adc0c9/image.png)

앞에서는 SessionManager에서 Cookie를 직접 new로 생성하여 response.addCookie()를 통해 Cookie정보를 넘겨줬다면 Servlet에서 제공하는 Session은 3번 4번의 과정이 이루어지게 될 경우 Servlet이 자체적으로 Cookie에 정보를 넘겨준다. F12 개발자모드로 쿠키를 확인해보자.

```
JSESSIONID
```

는 Servlet에서 default로 넣어주는 Name이다.

![https://images.velog.io/images/won-developer/post/17cc1208-257b-4cdd-9239-94a36c564207/image.png](https://images.velog.io/images/won-developer/post/17cc1208-257b-4cdd-9239-94a36c564207/image.png)

그리고 지금까지 문제없이 진행됐다면 아마 url의 주소를 봤을때

[http://localhost:8080/;jsessionid=855423ECD5AF593C588A9ED7ABF55CFD](http://localhost:8080/;jsessionid=855423ECD5AF593C588A9ED7ABF55CFD)

라고 입력이 되어있을 것이다. 자세히 보면 jsessionid=855423ECD5AF593C588A9ED7ABF55CFD는 서버에서 발급해준 Cookie와 같다. 이는 Servlet에서 Cookie를 사용하지 못하는 환경에 대비해(브라우저가 쿠키를 사용하지 못하거나, 기타 다른 이유로 인하여)자동으로 url에 해당 정보를 실어주는데 일반적으로 쿠키를 사용하지 못하는 환경은 없다고 봐도 무방하니.. 해당 기능을 꺼주도록 하자. 스프링부트에서는 간단하게 해당 기능을 제공한다. application.properties에 다음내용을 추가하자.

```java
server.servlet.session.tracking-modes=cookie
```

위 내용을 추가후 다시 서버를 시작하고 접속하면 정상적으로 url이 나오는것을 확인할 수 있다.

이제 /으로 redirect 시켰으니 /의 요청을 처리하는 Controller를 확인해보자.

---

### Controller

```java
@GetMapping("/")
public String home(HttpServletRequest request, Model model) {
    HttpSession session = request.getSession(false); //(1)
    if(session == null) { //(2)
        return "login"; //(2)
    }
    String memberId = (String)session.getAttribute(SessionConst.sessionId); //(3)
    Optional<Member> findMemberOptional = memberRepository.findByMemberId(memberId); //(4)
    Member member = findMemberOptional.orElse(null); //(5)
    if(member == null) { //(5)
        return "login"; //(5)
    }
    model.addAttribute("member", member); //(6)
    return "home"; //(6)
}
```

- (1): /으로 요청이고 세션이 없어도 문제없으므로 request.getSession(false)로 세션을 가져오고 argument가 false이므로 세션이 있으면 세션을, 없다면 null을 return 한다.
- (2): 생성된 세션이 없다면 login 페이지로 이동한다.
- (3): 생성된 세션이 있을경우 세션을 생성할 때 Key로 등록했던 상수 `SessionConst.sessionId` 즉 `LOGIN_MEMBER`를 Attribute로 등록했던 mebmerId를 찾는다.
- (4): memberId로 memberRepository에서 회원이 있는지 찾는다.
- (5): 만약 회원을 찾지 못했을 경우 null이 될것이고 login 페이지로 이동한다.
- (6): 정상적으로 회원을 찾았다면 model에 member를 추가하고 home 페이지로 이동한다.

---

아래는 모든 과정이 정상적으로 마무리되고, home페이지로 이동한 화면이다.

![https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png](https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png)

정상적으로 login된 home화면을 확인할 수 있다.

---

### 🚩 로그아웃을 통한 Session 무효화 처리

로그아웃을 통해 서버의 세션저장소에 등록된 정보를 삭제해보자.

![https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png](https://images.velog.io/images/won-developer/post/0925640c-05b7-49e2-863b-3828bf8c51c8/image.png)

home 화면에서 로그아웃 버튼을 클릭하면 /logout url로 요청된다.

### Controller

```java
@PostMapping("/logout")
public String logout(HttpServletRequest request) {
    HttpSession session = request.getSession(false); //(1)
    if(session == null) { //(2)
        return "redirect:/"; //(2)
    }
    session.invalidate(); //(3)
    return "redirect:/"; //(3)
}
```

- (1): logout으로 요청이고 세션이 없어도 문제없으므로 request.getSession(false)로 세션을 가져오고 argument가 false이므로 세션이 있으면 세션을, 없다면 null을 return 한다.
- (2): session이 없어 null을 return 받았다면 /으로 redirect 한다.
- (3): session을 return 받았다면 session.invalidate()로 세션을 지우고 /으로 redirect 한다. /요청을 처리하는 Controller에서는 세션을 확인후 세션정보가 없으니 login 페이지로 이동시킬 것이다.

### 🚩 Session 유효시간 관리하기

스프링부트에서는 간단하게 해당기능을 제공한다. application.properties에 다음 내용을 추가하자.
`server.servlet.session.timeout=1800` 1800초(30분)라는 의미이다.
위 내용을 추가하게 되면 30분간 Session정보를 가지는 request 요청이 없을경우 자동으로 서버에서 세션을 삭제한다.

---