<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-saml2-service-provider</artifactId>
</dependency>


----
server:
  port: 8080

spring:
  security:
    saml2:
      relyingparty:
        registration:
          my-sp:
            entity-id: "http://localhost:8080/saml2/sp"
            assertingparty:
              metadata-uri: "https://localhost:9999/pf/federation/metadata/idp"
            decryption:
              credentials:
                - private-key-location: "classpath:sp_private_pkcs8.key"
                  certificate-location: "classpath:sp_cert.pem"
            signing:
              credentials:
                - private-key-location: "classpath:sp_private_pkcs8.key"
                  certificate-location: "classpath:sp_cert.pem"

                  ------

@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http, 
            RelyingPartyRegistrationRepository relyingPartyRegistrationRepository) throws Exception {
        
        http.authorizeHttpRequests(auth -> auth.anyRequest().authenticated())
            .saml2Login(saml2 -> saml2.defaultSuccessUrl("/home"));

        return http.build();
    }
}


-----

@RestController
public class HomeController {

    @GetMapping("/home")
    public String home(@AuthenticationPrincipal Saml2AuthenticatedPrincipal principal) {
        return "Hello " + principal.getName() + "! Your email is: " + principal.getAttribute("email");
    }
}


                  
