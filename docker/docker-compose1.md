


<B>Dockerfile defines how the image is built. It controls what is inside the container (OS, packages, source code, build steps).<b>

---
---
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/e1a07dc8-8979-4f9a-9e58-bd5285c93d32" />



---
docker-compose.yaml defines how the container is run. It handles ports, volumes, networks, and container relationships.
---
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/a174ebf1-5970-4b0c-a417-10d46de0a45c" />




---

<b>
Use the Dockerfile to define how the image is built.

Use docker-compose.yaml to control how containers run and connect.

Network setup always belongs to the runtime, not the build phase.

</b>
