---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: e1bd45413368abe253ff4ac7733bbdcd3d0a4cc3
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507476"
---
Crie uma classe Java `Todo` ao lado da classe `DemoApplication` usando o seguinte código:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Essa classe é um modelo de domínio mapeado na tabela de `todo` que você criou anteriormente.

Para gerenciar essa classe, você precisará de um repositório. Defina uma nova interface `TodoRepository` no mesmo pacote usando o seguinte código:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

Esse repositório é um repositório reativo que o Spring Data R2DBC gerencia.

Conclua o aplicativo criando um controlador que pode armazenar e recuperar dados. Implemente uma classe `TodoController` no mesmo pacote e adicione o seguinte código:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Por fim, pare o aplicativo e inicie-o novamente usando o seguinte comando:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testar o aplicativo

Para testar o aplicativo, você pode usar cURL.

Primeiro, crie um item "todo" no banco de dados usando o seguinte comando:

```bash
curl --header "Content-Type: application/json" \
    --request POST \
    --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
    http://127.0.0.1:8080
```

Este comando deverá retornar o item criado, conforme mostrado aqui:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Em seguida, recupere os dados usando uma nova solicitação do cURL com o seguinte comando:

```bash
curl http://127.0.0.1:8080
```

Esse comando retornará a lista de itens "todo", incluindo o item que você criou, conforme mostrado aqui:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```
