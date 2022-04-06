OAuthLogin을 Interceptor를 이용해 구현해보고 싶었다.

현재는 JWT에 대한 공부가 부족해서 Session으로 인증처리를 할 건데, 추후에는 JWT로 바꿀 것이기 때문에 그에 대한 변경을 염두에 두고 만들어보았다.

문제점이 하나 있다면, OAuthLoginInterceptor를 만들 때 Session방식과 Jwt방식의 객체 혼용해서 만든 List가 생성자로 전달이 가능하다는 점이다. 다른 좋은 설계가 있을지, 개선 방안이 있을지 고민해보도록 한다.

![image](https://user-images.githubusercontent.com/54942017/162008144-3d9b95a5-155e-429b-a404-4ff5c6c3110a.png)

참고) 기울어져있는 글자는 abstract를 의미한다.  
+는 public, -는 private, #은 protected.


~~~java
// 밑의 코드는 간단하게 생략하여 구현해놓았다. 참고만 하길 바란다.

public class OAuthLoginInterceptor implements HandlerInterceptor {
    private List<OAuthLogin> oAuthLogins;
    
    public OAuthLoginInterceptor(List<OAuthLogin> oAuthLogins) {
        // oAuthLogins에 session방식과 jwt방식이 섞여서 들어왔을 가능성이 존재한다.
        this.oAuthLogins = oAuthLogins;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        oAuthLogins.stream()
            .filter(OAuthLogin::isSupported)
            .findFirst()
            .ifPresent(oAuthLogin -> oAuthLogin.login(request));
    
        return false;
    }
}

public abstract OAuthLogin {
    public void login(HttpServletRequest request) {
        String code = request.getParameter("authorization code");
        
        String accessToken = requestAccessToken(code);
        UserInfo userInfo = requestUserInfo(accessToken);
        
        authenticate(request, userInfo);
    }
    
    public abstract void isSupported();
    protected abstract String requestAccessToken(String code);
    protected abstract UserInfo requestUserInfo(Strint accessToken);
    protected abstract void authenticate(HttpServletRequest request, UserInfo userInfo);
}

public abstract OAuthSessionLogin extends OAuthLogin {
    protected void authenticate(HttpServletRequest request, UserInfo userInfo) {
        HttpSession httpSession = request.getSession();
        httpSession.setAttribute("SESSION", userInfo);
    }
}

public GitHubOAuthLogin extends OAuthSessionLogin {
    // Github와 맞게 isSupported(), requestAccessToken(), requestUserInfo() 메서드 구현
}
~~~
