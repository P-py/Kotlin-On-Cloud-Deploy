<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->



<!-- PROJECT LOGO -->
<br />
<div align="center">

<h3 align="center">Kotlin-On-Clound-Deploy</h3>

  <p align="center">
    The deploy on a docker/kubernets enviroment version of a Kotlin application that uses (Kotlin + Retrofit + Resilience4j + Coroutines + Kotest + MockK) to implement unit tests and async functions - reference from: <a href="https://cursos.alura.com.br/course/kotlin-aplicacoes-resilientes-assincronas">Async Kotlin Apps Course</a> + <a href="https://cursos.alura.com.br/course/kotlin-preparando-aplicacao-cloud">Kotlin On Cloud</a>
  </p>
</div>


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

[![MIT License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]
[![Kotlin](https://img.shields.io/badge/Kotlin-000000?style=for-the-badge&logo=kotlin&logoColor=white)](https://www.bing.com/search?q=kotlin+docs&cvid=09bf36b937384482bb11751d2fbd9c1a&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIGCAEQRRg8MgYIAhBFGDwyBggDEEUYQTIGCAQQRRhBMgYIBRBFGEEyCAgGEOkHGPxV0gEIMTEzN2owajmoAgCwAgE&FORM=ANAB01&PC=U531)
[![Docker](https://img.shields.io/badge/Docker-00084d?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)

The application was developed using Hexagonal Architecture, to access examples and know more click <a href="https://beyondxscratch.com/2020/08/23/hexagonal-architecture-example-digging-a-spring-boot-implementation/">here.</a>

Deployed on <a href="[https://www.redhat.com/en/technologies/cloud-computing/openshift](http://route-car-service-test-1pdrsant-dev.apps.sandbox-m3.1530.p1.openshiftapps.com/cars)">RedHat OpenShift</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

Necessary tools to deploy and test the app:
- Docker Engine (could be Docker Desktop)
- [K3D](https://k3d.io/v5.6.3/)
- Maven
- Git

### Deploying / Testing

1. Clone the project
    ```bash
    git clone https://github.com/P-py/Kotlin-On-Cloud-Deploy.git
    ```
2. Enter the main directory 
3. Create the folder k8s in the main/kotlin/br/com/car/
   ```bash
   cd Kotlin-On-Cloud-Deploy/src/main/kotlin/br/com/car/
   mkdir k8s
   ```
5. Create a folder for each dependency of the project
   ```bash
   mkdir app, ingress, mysql, redis
   ```
4. Configure the .yaml files used for the project deploy via k3d/kubernetes
   - Main app
   ```yaml
   ### Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: ###
    spec:
      selector:
        matchLabels:
          app: ###
      template:
        metadata:
          labels:
            app: ###
        spec:
          containers:
            - name: ###
              image: [DockerHub user]/[App Name]:[Tag version]
              ports:
                - containerPort: ###
              env:
                - name: MYSQL_HOST
                  value: ###
                - name: MYSQL_DATABASE
                  value: ###
                - name: MYSQL_PASSWORD
                  value: ###
                - name: MYSQL_USERNAME
                  value: ###
                - name: REDIS_HOST
                  value: ###
   ```
   ```yaml
   ### Service
   apiVersion: v1
   kind: Service
   metadata:
      name: ###
   spec:
      selector:
        app: ###
      ports:
        - port: ###
      type: NodePort
   ```
   - Ingress
   ```yaml
   ###ingress.yaml
   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
      name: ###
   spec:
      rules:
        - http:
            paths:
              - path: ###
                pathType: ###
                backend: 
                  service:
                    name: ###
                    port:
                      number: ###
   ```
   - MySQL
   ```yaml
   ### Deployment
   apiVersion: apps/v1
   kind: Deployment
   metadata:
      name: mysql
      labels:
        app: mysql
        tier: database
   spec:
      selector:
        matchLabels:
          app: mysql
      template:
        metadata:
          labels:
            app: mysql
            tier: database
        spec:
          containers:
            - name: mysqldb-image
              image: mysql:[Version]
              env:
                - name: MYSQL_ROOT_PASSWORD
                  value: ###
                - name: MYSQL_DATABASE
                  value: ###
              ports:
                - containerPort: ###
                  name: mysql
   ```
   ```yaml
   ### Service
   apiVersion: v1
   kind: Service
   metadata:
    name: mysql
    labels:
      app: mysql
      tier: database
    spec:
      selector:
        app: mysql
        tier: database
      ports:
        - port: ###
          targetPort: ###
      clusterIP: ###
   ```
   - Redis
   ```yaml
   ### Deployment
   apiVersion: apps/v1
   kind: Deployment
   metadata:
    name: redis
   spec:
      selector:
        matchLabels:
          app: redis
      template:
        metadata:
          labels:
            app: redis
        spec:
          containers:
            - name: redis-image
              image: redis:bullseye
              ports:
                - containerPort: ###
                  name: ###
   ```
   ```yaml
   ### Service
   apiVersion: v1
   kind: Service
   metadata: 
      name: redis
   spec:
      selector:
        app: redis
      ports:
        - port: ###
          targetPort: ###
      clusterIP: ###
   ```
6. Create the resources for the docker container in the main/resources dir
   ```yaml
   ### application
   spring:
      profiles:
        active: @activatedProperties@
   ```
   ```yaml
   ### dev profiles
   spring:
      datasource:
        driverClassName: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://localhost:3306/car
        username: ###
        password: ###
      cache:
        type: redis
      redis:
        host: localhost
        port: 6379
   ```
   ```yaml
   spring:
   datasource:
     driverClassName: com.mysql.cj.jdbc.Driver
     url: jdbc:mysql://${MYSQL_HOST}/${MYSQL_DATABASE}
     username: ${MYSQL_USERNAME}
     password: ${MYSQL_PASSWORD}
   cache:
     type: redis
   redis:
    host: ${REDIS_HOST}
   ```
7. With a configured and logged DockerHub account, create a image and puhs it to your profile repo
   ```bash
      docker image -t build [App Name]:[Version tag]
      docker tag [Hash] [DockerHub username]/[App Name]:[Version tag]
      docker push [DockerHub username]/[App Name]:[Version tag]
   ```
8. Create the kubernetes locally 
   ```bash
      k3d cluster create --port ###:###@loadbalancer
      ### In the k8s folder
      kubectl apply -f ./mysql/
      kubectl apply -f ./redis/
      kubectl apply -f ./ingress/
      kubectl apply -f ./app/
   ```
<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE EXAMPLES -->
## Usage

Test the deploy @ <a href="http://route-car-service-test-1pdrsant-dev.apps.sandbox-m3.1530.p1.openshiftapps.com/cars">OpenShift Sandbox</a>, should return:

![image](https://github.com/P-py/Kotlin-On-Cloud-Deploy/assets/68572810/d081882b-5f62-48d4-b2f3-24117b23da03)


<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are essential in the tech world, especially for beginners like me, they are what makes the open-source and dev world such an amazing place to learn and create. Any contributions you make are **appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. Please give the project a star!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- LICENSE -->
## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Pedro Santos - pedrosalviano170@gmail.com

Project Link: [https://github.com/P-py/Kotlin-On-Cloud-Deploy](https://github.com/P-py/Kotlin-On-Cloud-Deploy)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

### TO-DO / NOT AVALIABLE YET

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[license-shield]: https://img.shields.io/github/license/P-py/CineSearch.svg?style=for-the-badge
[license-url]: https://github.com/P-py/CineSearch/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/pedro-s-3742b7211/
