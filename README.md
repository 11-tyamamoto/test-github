# test-github
GitHubの機能を試すためのリポジトリです。
---
##settings.json
<pre>
{
    "java.configuration.maven.globalSettings": "C:\\xxx\\conf\\settings.xml",
    "maven.executable.path": "C:\\xxx\\bin\\mvn.cmd",
    "[java]": {
        "editor.formatOnSave": true
    },
    "[html]": {
        "editor.tabSize": 2,
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "[xml]": {
        "editor.tabSize": 2,
        "editor.formatOnSave": true
    },
    "[yaml]": {
        "editor.tabSize": 2,
        "editor.defaultFormatter": "esbenp.prettier-vscode"
    },
    "editor.detectIndentation": false,
    "java.configuration.runtimes": [],
    "editor.codeActionsOnSave": {
        "source.organizeImports": true
    },
    "java.inlayHints.parameterNames.enabled": "none",
    "java.compile.nullAnalysis.mode": "disabled"
}
</pre>
---
##settings.json - project
<pre>
{
    "java.configuration.runtimes": [
        {
            "name": "JavaSE-21",
            "path": "C:\\xxx\\jdk-21.0.1",
            "default": true
        }
    ],
    "java.configuration.updateBuildConfiguration": "automatic"    
}
</pre>
---
##SecurityConfig
<pre>
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.securityMatcher(AntPathRequestMatcher.antMatcher("/h2-console/**"))
                .authorizeHttpRequests(authorize -> authorize
                        .requestMatchers(AntPathRequestMatcher.antMatcher("/h2-console/**")).permitAll())
                .csrf(csrf -> csrf
                        .ignoringRequestMatchers(AntPathRequestMatcher.antMatcher("/h2-console/**")))
                .headers(headers -> headers.frameOptions(
                        frame -> frame.sameOrigin()))
                .securityMatcher("/**")
                .formLogin(Customizer.withDefaults())
                .csrf(Customizer.withDefaults())
                .headers(Customizer.withDefaults())
                .authorizeHttpRequests(authorize -> authorize
                        .requestMatchers(PathRequest.toStaticResources().atCommonLocations()).permitAll()
                        .requestMatchers(AntPathRequestMatcher.antMatcher("/login")).permitAll()
                        .anyRequest().authenticated());
        http.formLogin((login) -> login
                .loginProcessingUrl("/login")
                .loginPage("/login")
                .usernameParameter("userId")
                .passwordParameter("password")
                .defaultSuccessUrl("/user/list", true)
                .failureUrl("/login?error"));
        http.logout((logout) -> logout
                .logoutRequestMatcher(new AntPathRequestMatcher("/logout"))
                .logoutUrl("/logout")
                .logoutSuccessUrl("/login?logout"));

        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
</pre>
    
---
##UserDetailsService
<pre>
@Service
public class LoginUserDetailsService implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
        LoginUser loginUser = new LoginUser("user", encoder.encode("ppp"), "user@xxx.co.jp", List.of("P", "A"));
        return new LoginUserDetails(loginUser);
    }

}
</pre>
---
##UserDetails
<pre>
public class LoginUserDetails implements UserDetails {

    private final LoginUser loginUser;

    private Collection<? extends GrantedAuthority> authorities;

    public LoginUserDetails(LoginUser loginUser) {
        this.loginUser = loginUser;
        this.authorities = AuthorityUtils.createAuthorityList(loginUser.authorities());
    }    
</pre>
---
##User
<pre>
public record LoginUser(String name, String password, String email, List<String> authorities) {
</pre>
---
###mybatis scan
<pre>
org.mybatis.spring.mapper.MapperScannerConfigurer
basePackage
</pre>
