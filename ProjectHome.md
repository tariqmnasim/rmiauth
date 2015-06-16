Small library that allows RMI connections authentification with login and password.

It implements socket factories that perform authentification after connection establishes.

### Sample usage ###

On server side:

```
public class Main {

    private static final AuthRMIClientSocketFactory csf = new AuthRMIClientSocketFactory();
    private static final AuthRMIServerSocketFactory ssf = new AuthRMIServerSocketFactory(new Authorizer() {

        @Override
        public boolean authorize(AuthData authData) {
            return authData.login.equals("login") && authData.password.equals("password");
        }
    });

    public static void main(String[] args) throws Throwable {
        // Create and install a security manager
        if (System.getSecurityManager() == null) {
            System.setSecurityManager(new RMISecurityManager());
        }

        Naming.rebind(TestController.RMI_BINDING_NAME,
                new TestControllerRmiImpl(TestController.PORT, csf, ssf));

        while (true) {
            Thread.sleep(100);
        }
    }
}
```

On client side:
```
public class Main {

    public static void main(String[] args) throws Throwable {
        AuthRMIClientSocketFactory.setHostAuthData("127.0.0.1",
                new AuthData("login", "password"));

        // Create and install a security manager
        if (System.getSecurityManager() == null) {
            System.setSecurityManager(new RMISecurityManager());
        }

        TestController test = (TestController) Naming.lookup(TestController.RMI_BINDING_NAME);

        System.out.println(test.f());
    }
}
```