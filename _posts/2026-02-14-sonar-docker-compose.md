---
title: 'Docker Compose for SonarQube: A Simple YAML Template'
date: 2026-02-14
permalink: /posts/2026/02/14/sonar-docker-compose
author_profile: false
tags:
 - Docker
 - SonarQube
excerpt: "Basic yaml file for Docker Compose to run SonarQube."
---

## Docker Compose for SonarQube Community 

```yml
services:
  sonarqube:
    image: sonarqube:community
    restart: unless-stopped
    depends_on:
      - db
    environment:
      - SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonar
      - SONAR_JDBC_USERNAME=sonar
      - SONAR_JDBC_PASSWORD=sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
    networks:
      - sonarnet

  db:
    image: postgres:15
    restart: unless-stopped
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
      - POSTGRES_DB=sonar
    volumes:
      - postgresql_data:/var/lib/postgresql/data
    networks:
      - sonarnet

networks:
  sonarnet:

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql_data:
```


Then, we pull the images:

```bash
docker compose pull
```

And run the server:

```bash
docker compose up -d
```

To setup Sonar, we access to `<ip>:9000` and update the password. 
The default is:
- User: `admin`
- Password: `admin`

## Update the docker image for Sonar

```bash
docker compose down
docker compose pull 
docker compose up -d   
```

## Links:

- [https://docs.sonarsource.com/sonarqube-server/server-installation/from-docker-image/set-up-and-start-container](https://docs.sonarsource.com/sonarqube-server/server-installation/from-docker-image/set-up-and-start-container)