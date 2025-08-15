
---

## 🧠 1. [OWASP Top 10 CI/CD Security Risks (JA)](https://coky-t.gitbook.io/owasp-top-10-ci-cd-security-risks-ja)

Проект OWASP описывает 10 ключевых угроз в цепочках CI/CD:

1. **Недостаточные механизмы управления потоком**
    
2. **Слабое управление доступом и идентификацией**
    
3. **Злоупотребление зависимостями (Supply Chain Attacks)**
    
4. **Отравление пайплайна (Poisoned Pipeline Execution)**
    
5. **Недостаточный контроль на уровне пайплайнов (PBAC)**
    
6. **Плохая гигиена секретов и токенов**
    
7. **Небезопасные конфигурации системы**
    
8. **Неуправляемое использование сторонних сервисов**
    
9. **Отсутствие валидации целостности артефактов**
    
10. **Недостаточное логирование и мониторинг**
    

🔗 [Полный гайд на японском языке](https://coky-t.gitbook.io/owasp-top-10-ci-cd-security-risks-ja)

---

## 2. [hahwul / DevSecOps Roadmap (GitHub)](https://github.com/hahwul/DevSecOps)

Репозиторий содержит:

- Жизненный цикл DevSecOps (Design → Code → Build → Test → Release → Deploy → Operate)
    
- Инструменты: SAST, DAST, SCA, Secrets Detection, Compliance и другие
    
- Рекомендации по интеграции безопасности в DevOps
    

🔗 [Перейти в репозиторий](https://github.com/hahwul/DevSecOps)

---

## 3. [DevSecOps против традиционного DevOps (CyberKshetra)](https://cyberkshetra.gitbook.io/noels-cyberkshetra-blogspace/technical-cyber-articles/devsecops-making-a-difference-from-traditional-devops)

Автор объясняет:

- Как DevSecOps отличается от классического DevOps
    
- Почему безопасность должна быть встроена на каждом этапе CI/CD
    
- Роль автоматизации в обеспечении безопасности
    

🔗 [Читать статью](https://cyberkshetra.gitbook.io/noels-cyberkshetra-blogspace/technical-cyber-articles/devsecops-making-a-difference-from-traditional-devops)

---

## 4. [Практический GitLab CI/CD Lab (GitLab Handbook)](https://handbook.gitlab.com/handbook/customer-success/professional-services-engineering/education-services/gitlabcicdhandsonlab1/)

Подробный учебный материал от GitLab:

- Настройка CI/CD пайплайнов
    
- Практика безопасной автоматизации
    
- Интеграция с GitLab Runner, Registry, и Kubernetes
    

🔗 [Открыть гайд](https://handbook.gitlab.com/handbook/customer-success/professional-services-engineering/education-services/gitlabcicdhandsonlab1/)

---

## 5. [Exposed Docker Daemon (OWASP SKF Write-Up)](https://skf.gitbook.io/asvs-write-ups/exposed-docker-daemon/exposed-docker)

Уязвимость:

- Когда Docker Daemon (`/var/run/docker.sock`) открыт или монтирован в контейнер, злоумышленник может получить доступ к хосту.
    

🔗 [Реальный пример уязвимости](https://skf.gitbook.io/asvs-write-ups/exposed-docker-daemon/exposed-docker)

---

## 6. [Insecure Volume Mount — Docker атаки (Madhu Akula)](https://madhuakula.com/content/attacking-and-auditing-docker-containers-using-opensource/attacking-docker-containers/insecure-volume-mount.html)

Как небезопасное монтирование папок (особенно `docker.sock`) может:

- Позволить доступ к хосту из контейнера
    
- Привести к выполнению команд на уровне root
    
- Использоваться для Container Escape атак
    

🔗 [Подробный анализ](https://madhuakula.com/content/attacking-and-auditing-docker-containers-using-opensource/attacking-docker-containers/insecure-volume-mount.html)

---

## 7. [Docker Exploitation Techniques (Pentips)](https://csbygb.gitbook.io/pentips/web-pentesting/docker-exploitation)

Материал включает:

- Методики взлома Docker-контейнеров
    
- Трюки с volume, escape техники
    
- Настройки для безопасной эксплуатации
    

🔗 [Открыть статью](https://csbygb.gitbook.io/pentips/web-pentesting/docker-exploitation)

---

## 8. [PentestBook от six2dez](https://pentestbook.six2dez.com/)

Широкий справочник по пентестингу:

- Раздел по CI/CD, DevSecOps
    
- Docker, Kubernetes, Cloud, Web, AD
    
- Сценарии атак и инструменты
    

🔗 [Перейти на сайт](https://pentestbook.six2dez.com/)

---

## ✅ Заключение

**Рекомендации:**

- Пройдите по [OWASP Top 10](https://coky-t.gitbook.io/owasp-top-10-ci-cd-security-risks-ja) и проверьте свою инфраструктуру CI/CD.
    
- Используйте [DevSecOps roadmap от hahwul](https://github.com/hahwul/DevSecOps) как чеклист внедрения безопасности.
    
- Защитите Docker: избегайте монтирования сокетов, запуска от root, сканируйте образы.
    
- Изучите практики по [GitLab CI/CD](https://handbook.gitlab.com/handbook/customer-success/professional-services-engineering/education-services/gitlabcicdhandsonlab1/) и [PentestBook](https://pentestbook.six2dez.com/) для тестирования безопасности.
    

---
