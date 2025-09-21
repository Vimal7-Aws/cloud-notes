
---

<br/>
<br/>


<img width="673" height="398" alt="image" src="https://github.com/user-attachments/assets/b1979aa2-c317-4640-887d-209ff9dac527" />

<br/><br/>


# 🌟 Richardson Maturity Model (REST API Levels)

The **Richardson Maturity Model** shows the progression of an API from a simple RPC style to a full RESTful API with hypermedia support.  

---

## 🟢 Level 0 — Remote Procedure Call (RPC)

- 🔌 **HTTP = RPC Transport**: HTTP is used just to call functions on the server, similar to remote procedure calls.  
- 📝 **Action-Oriented URLs**: API requests map to specific server functions, with URLs often representing **actions** rather than resources.  
- 📮 **Single Endpoint**: Uses one URL and HTTP method (often `POST`) for all interactions.  

> _Example:_  
> `POST /doAction` → calls a server-side method.  

---

## 🟡 Level 1 — Resources

- 📦 **Resources Introduced**: The client now interacts with **resources** instead of just functions.  
- 🧾 **Plural Nouns**: Resources named using plural nouns (e.g. `/users`, `/orders`).  
- 🗂️ **Distinct URLs per Resource**: Each resource gets its own URL.  

> _Example:_  
> `POST /users` → create a new user  
> `GET /users` → list all users  

---

## 🟠 Level 2 — HTTP Verbs

- 📝 **Standard HTTP Methods** used to perform CRUD operations:
  - `GET` — retrieve a resource.
  - `POST` — create a resource.
  - `PUT` — update/replace a resource.
  - `DELETE` — delete a resource.
- 🎯 **Semantic Meaning**: Each verb has its intended meaning.
- 🛠️ **Framework Support**: Spring’s annotations (`@GetMapping`, `@PostMapping`, etc.) encourage correct verb usage.

> _Example:_  
> `GET /users/123` → retrieve user #123  
> `PUT /users/123` → update user #123  

---

## 🔵 Level 3 — Hypermedia as the Engine of Application State (HATEOAS)

- 🔗 **Hypermedia Links in Responses**: The API returns links to related actions/resources so the client can discover them dynamically.  
- 🖥️ **Dynamic Frontends**: A frontend can render action buttons based on the hypermedia links returned.  
- 📚 **Spring Support**: Spring MVC doesn’t do HATEOAS out-of-the-box, but libraries like **Spring HATEOAS** can be integrated.

> _Example Response:_  
> ```json
> {
>   "id": 123,
>   "name": "Alice",
>   "_links": {
>     "self": { "href": "/users/123" },
>     "orders": { "href": "/users/123/orders" }
>   }
> }
> ```

---

## 📊 Quick Comparison Table

| Level | 📝 Description | 🔗 Key Feature |
|-------|---------------|----------------|
| 0️⃣ RPC | HTTP used like a transport for function calls | Single endpoint & method |
| 1️⃣ Resources | Introduces resources with distinct URLs | Plural nouns, resource-based URLs |
| 2️⃣ HTTP Verbs | Uses correct HTTP methods for CRUD | GET, POST, PUT, DELETE |
| 3️⃣ HATEOAS | Hypermedia drives application state | Links in responses, discoverable actions |

---

> 🌟 **Takeaway:** Each level builds on the previous one. Level 3 (HATEOAS) is considered a fully RESTful API.

Would you like me to add an **ASCII diagram** showing how a client interacts with an API at each level (0–3)?
