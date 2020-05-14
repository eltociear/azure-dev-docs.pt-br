---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: d4b9becdce2b78e928b97b7d980024eac5871df2
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369806"
---
Crie uma classe Java `Todo`, ao lado da classe `DemoApplication`:

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

Para gerenciar essa classe, você precisará de um repositório. Defina uma nova interface `TodoRepository` no mesmo pacote:

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

Por fim, pare o aplicativo e inicie-o novamente:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testar o aplicativo

Para testar o aplicativo, você pode usar cURL.

Primeiro, crie um item "todo" no banco de dados:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

Este comando deve retornar o item criado:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Em seguida, recupere os dados usando uma nova solicitação cURL:

```bash
curl http://127.0.0.1:8080
```

Esse comando retornará a lista de itens "todo", incluindo o item que você criou:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```
