# 어노테이션 정리 하기

> Request 매핑이랑 GetMapping의 차이가 뭐야? 왜 다들 RequestMapping을 선호하는걸까?

`@RequestMapping`과 `@GetMapping`은 둘 다 Spring Framework에서 HTTP 요청을 처리하기 위한 어노테이션입니다. 하지만 그들의 사용 목적과 차이점이 존재합니다. 기본적인 차이점과 사람들이 왜 `@RequestMapping`을 선호하는지 알아보겠습니다.

#### 1. `@RequestMapping`과 `@GetMapping`의 차이

**1) `@RequestMapping`**

* **역할**: `@RequestMapping`은 HTTP 요청을 처리할 메서드 또는 클래스에 매핑하는 가장 일반적인 어노테이션입니다. HTTP 메서드(GET, POST, PUT, DELETE 등)와 관계없이 여러 유형의 요청을 매핑할 수 있습니다.
*   **유연성**: `@RequestMapping`은 HTTP 메서드뿐만 아니라 경로(path), 헤더(headers), 요청 매개변수(params) 등 다양한 조건을 기반으로 요청을 처리할 수 있는 매우 유연한 어노테이션입니다.

    예:

    ```java
    @RequestMapping(value = "/users", method = RequestMethod.GET)
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
    ```

**2) `@GetMapping`**

*   **역할**: `@GetMapping`은 `@RequestMapping`의 특정한 유형으로, **GET 요청**만을 처리하기 위한 어노테이션입니다. 이것은 HTTP 메서드가 **GET**인 경우에만 사용됩니다. `@GetMapping`은 더 간결하고 직관적으로 GET 요청을 처리할 때 많이 사용됩니다.

    예:

    ```java
    @GetMapping("/users")
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
    ```

#### 2. 왜 `@RequestMapping`을 선호할까?

1. **다양한 HTTP 메서드 처리**: `@RequestMapping`은 GET, POST, PUT, DELETE, PATCH 등 다양한 HTTP 메서드를 지원하며, 하나의 어노테이션에서 처리할 수 있기 때문에 더 유연합니다. 특히 REST API에서는 여러 HTTP 메서드를 사용하는 경우가 많아 `@RequestMapping`이 더 적합할 수 있습니다.
2.  **클래스 레벨 매핑**: `@RequestMapping`은 클래스 레벨에서도 사용할 수 있습니다. 클래스 레벨에 적용하면 모든 메서드에 대해 공통된 경로를 설정할 수 있어 코드를 더 간결하게 유지할 수 있습니다.

    ```java
    @RestController
    @RequestMapping("/users")
    public class UserController {

        @GetMapping
        public List<User> getAllUsers() {
            return userService.getAllUsers();
        }

        @PostMapping
        public User createUser(@RequestBody User user) {
            return userService.createUser(user);
        }
    }
    ```

    위 코드에서 클래스 레벨의 `@RequestMapping("/users")`는 모든 메서드에 대해 `/users` 경로를 적용하므로, 메서드별로 경로를 따로 지정하지 않아도 됩니다.
3. **하위 호환성**: `@RequestMapping`은 스프링 2.5부터 존재해온 어노테이션으로, 오래된 버전의 스프링에서도 사용할 수 있습니다. 반면, `@GetMapping`, `@PostMapping` 등의 특정 메서드 전용 어노테이션은 스프링 4.3 이후에 추가되었습니다. 따라서 오래된 프로젝트나 레거시 코드에서는 주로 `@RequestMapping`을 많이 사용하게 됩니다.
4.  **다기능성**: `@RequestMapping`은 메서드 외에도 요청의 다양한 속성(헤더, 매개변수, 프로듀스/컨슘 등)을 처리할 수 있는 옵션을 제공합니다. 이 때문에 다양한 조건에 따라 요청을 처리하는 복잡한 API에서는 `@RequestMapping`이 더 선호됩니다.

    예를 들어, 특정 헤더가 있는 요청만 처리하는 경우:

    ```java
    @RequestMapping(value = "/users", method = RequestMethod.GET, headers = "X-Custom-Header=foo")
    public List<User> getUsersWithCustomHeader() {
        return userService.getAllUsers();
    }
    ```

#### 3. `@GetMapping`과 같은 메서드 전용 어노테이션의 장점

하지만, `@GetMapping`, `@PostMapping`, `@PutMapping` 등의 HTTP 메서드 전용 어노테이션들도 사용 시 장점이 있습니다.

* **가독성**: 메서드 전용 어노테이션은 요청의 HTTP 메서드를 직관적으로 알 수 있어, 코드 가독성이 높아집니다. 예를 들어, `@GetMapping`을 사용하면 해당 메서드가 GET 요청을 처리한다는 것을 바로 알 수 있습니다.
* **간결성**: 코드가 간결해집니다. `@RequestMapping(method = RequestMethod.GET)` 대신 `@GetMapping`을 사용하는 것이 더 짧고 명확합니다.

#### 결론

* `@RequestMapping`은 다양한 HTTP 메서드를 처리할 수 있고 클래스 레벨에서 공통 경로를 설정하는 데 유리하며, 더 유연하게 요청을 처리할 수 있습니다. 그래서 레거시 코드나 복잡한 API에서는 여전히 많이 사용됩니다.
* **`@GetMapping` 등 HTTP 메서드 전용 어노테이션**은 간결하고 직관적이며, 가독성을 높여줍니다. 간단한 API를 작성할 때는 이러한 메서드 전용 어노테이션을 사용하는 것이 더 좋습니다.

둘 중 어느 것을 사용할지 선택하는 것은 프로젝트의 요구사항에 따라 달라집니다.
